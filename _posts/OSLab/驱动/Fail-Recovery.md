---
title:  Fail-Recovery
date: 2017-01-11 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - OSLab
    - 驱动
tags:   # 文章标签，参数可省略
---
### Fail-Recovery的特点
some drivers like SATA are based on the
fail-recovery model, namely they will restart when an
error occurs. Thus, many branches are needed to handle
the recovery procedure. For these drivers, injecting a
single fault is not enough to cover most error handling
code.



The main reason is that
most user-mode applications are based on fail-recovery
model. During recovery, error handling code should
handle other errors. Therefore, multiple faults need to
be injected in user-mode applications to cover most
error handling code in runtime testing.



R. Banabic and G. Candea. Fast black-box testing of system
recovery code. In Proceedings of the 7th European
conference on Computer Systems, pages 281-294, 2012.



P. D. Marinescu and G. Candea. Efficient testing of recovery
code using fault injection. In ACM Transactions
on Computer Systems, volume 29, issue 4, 2011.

<!--more-->