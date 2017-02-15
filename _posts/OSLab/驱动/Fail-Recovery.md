---
title:  Fail-Recovery
date: 2017-01-11 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - OSLab
    - 驱动
tags:   # 文章标签，参数可省略
---
### Fail-Recovery的特点
* 对比fail-stop和fail-recovery两者的差异，确定EH-Test如何修改完善到支持fail-recovery的驱动


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



ADFI injects multiple faults in each test case. The advantage is that much more configuration and error handling code can be covered to detect more bugs.



R. Banabic and G. Candea. Fast black-box testing of system
recovery code. In Proceedings of the 7th European
conference on Computer Systems, pages 281-294, 2012.



P. D. Marinescu and G. Candea. Efficient testing of recovery
code using fault injection. In ACM Transactions
on Computer Systems, volume 29, issue 4, 2011.

K. Cong, L. Lei, Z. Yang and F. Xie. Automatic fault injection
for driver robustness testing. In Proceedings of
the 2015 International Symposium on Software Testing
and Analysis, pages 361-372, 2015.


<!--more-->

### SATA驱动
#### linux 2.6.10 drivers/scsi/sata_sx4.c


#### linux 4.2.1 drivers/ata/sata+sx4.c
pdc_reset_port


fail-stop在错误出现的时候直接pci_disable_device(pdev)，而fail-recovery并没有，而是做一些重新初始化的reset工作。


#### 2.6.10 drivers/scsi/sata_promise.c/pdc_port_start
有kfree(pp);

#### 4.2.1 drivers/ata/sata_promise.c/pdc_common_port_start
无kfree(pp);
Memory allocated using this function will be automatically released on driver detach.

drivers/ata/sata_promise.c/pdc_error_handler赋值给了一个结构体的元素，通过这个元素来调用pdc_error_handler()

if (sata_scr_valid(&ap->link) && (ap->flags & PDC_FLAG_GEN_II))

if (!(ap->pflags & ATA_PFLAG_FROZEN))

寄存器的值
多个条件满足，多分支
