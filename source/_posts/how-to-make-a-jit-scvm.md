---
title: 如何实现一个带JIT的Scratch虚拟机
mermaid: true
---

众所周知，由于 Scratch 原生虚拟机的实现非常暴力，因此 Scratch 的虚拟机一直是众多有高性能需求的 Scratch 作品的噩梦。你甚至能看到在极少的循环内执行延迟就已达到了惊人的 300ms。为此，各路 Scratcher 争相拿出了解决方案（例如 forkphorus, TurboWarp 等），其中最普遍的思路就是使用浏览器自带的 JavaScript 引擎来把积木转换成原生代码再运行。（当然也有用其他语言重写虚拟机的，本篇不细讲）本文将结合本人编写 JIT 功能的经验，谈谈如何在原有的 Scratch 虚拟机上添加一个 JIT 功能。
![](https://s6.jpg.cm/2021/11/12/Iae5CH.gif)
*PS：本文仅提供思路，具体代码需要各位读者自行实践。*
## 怎么生成
我们分析源码可得，Scratch 的运行逻辑大概是这样的：
{% mermaid %}
graph LR
    A(Runtime) -->|从 target 读取 block 创建| B(Thread - FRESH)
    B -->|开始运行| C[Thread - RUNNING]
    C -->G[Execute]
    C -->|进入“休息”时间| D[Thread - YIELD_TICK]
    D -->|完成该线程的使命| E[Thread - DONE]
    F(sequencer) -->|进行调度管理| C
    F -->|进行调度管理| D
    F -->|进行调度管理| E
{% endmermaid %}
由以上流程我们可知，我们要抢先在线程进入运行环节前生成相关代码，所以我们要在生成完新鲜的 Thread 后立刻开始生成。
生成的最基本思路便是取出当前 Thread 的积木容器，并挨个遍历积木并生成相应代码。比如走( )步积木的实现就理所应当的如下所示：
```javascript
class Motion {
　　// ...
　　moveSteps (args, util) {
　　　　const steps = Cast.toNumber(args.STEPS);
　　　　const radians = MathUtil.degToRad(90 - util.target.direction);
　　　　const dx = steps * Math.cos(radians);
　　　　const dy = steps * Math.sin(radians);
　　　　util.target.setXY(util.target.x + dx, util.target.y + dy);
　　}
　　// ...
}
```
### 参数
按照上面的代码生成，相信明眼人都能看出问题。我们生成的代码返回的是字符串而不是函数，因此参数是不能直接传过去的。所以我们的思路是使用一个函数来实现对参数的处理：
```javascript
class Motion {
　　//...
　　motion_movesteps: (args) => {
        　　const steps = args.STEPS;
        　　return `
　　　　　　const radians = 　MathUtil.degToRad(90 - util.target.direction);
　　　　　　const dx = ${steps} * Math.cos(radians);
　　　　　　const dy = ${steps} * Math.sin(radians);
　　　　　　util.target.setXY(util.target.x + dx, util.target.y + dy);
　　　　`
}
    //...
}
```
至于内部运行需要的 util，则在生成时使用前缀的方式，运行时作为参数传入即可。
### 运行时刷新
像以上代码，运行时间短的积木可以忽略，但运行时间长就会阻塞整个编辑器，造成无休止的卡顿。为此我们需要引入``Generator Function``来防止这种情况的出现。
GeneratorFunction 是一种允许控制运行流程的函数，在函数内可以使用``yield [value];``的方式暂停函数的运行并等待调用方的下一步操作。
于是我们就可以将控制积木类的重复执行以以下的方式呈现：
```javascript
class Control {
　　control_forever: (args) => {
　　　　const SUBSTACK = args.SUBSTACK;
　　　　return `
　　　　　　while(true) {
　　　　　　　　${SUBSTACK}
　　　　　　　　yield;
　　　　　　}
　　　　`
　　}
}
```
之后在运行时由调度器进行调度即可, REPORTER 同理。
## 没有对应 JIT 代码的积木
本着最大化效率和确保兼容性的理念，我们不可能为了一个线程中的一个没有对应 JIT 代码的积木而放弃整个线程的编译。所以我们需要在生成时记录下这个没有对应 JIT 代码的积木并跳转到下一个有对应 JIT 代码的积木进行编译。
## 不刷新屏幕的自定义积木
我们可以通过在生成该自定义积木时传参，控制要不要 yield 即可。
（PS：不会真的有人在定义里写高耗时积木吧）
## 热更新
像 Scratch 这种按积木执行的模式是非常难在 JIT 在确保实时热更新的。因此我们只能在每次执行完这个编译片段（或遇到 yield 的情况）时返回当前的 blockID 并检测更新才能一定程度上确保热更新。由于 Scratch 是单线程的，且只有在高耗时积木中才会使用 yield（低耗时积木你感受不出来（不是），因此该问题不会有太大影响。
## 写在最后
以上仅为目前本人的实现思路，因此很可能存在谬误。如果你在编写过程中发现存在错误，请发邮件予以指正！
### 参考
[SteveXMH - 关于如何优化 Scratch 虚拟机的运行速度思路](https://steve-xmh.github.io/blog/2020/05/16/scratchvmjit/)
[TurboWarp 源码](https://github.com/TurboWarp/scratch-vm/)
[ClipCC 源码](https://github.com/Clipteam/clipcc-vm/)
