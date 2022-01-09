---
title: 第一篇文章
---

flow
st=>start:Start:>https://www.zybuluo.com
io=>inputoutput: verification
op=>operation:YourOperation
cond=>condition:YesorNo?
sub=>subroutine:YourSubroutine
e=>end
st->io->op->cond
cond(yes)->e
cond(no)->sub->io
