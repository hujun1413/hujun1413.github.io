---
title:  EH-Test阅读笔记
date: 2016-12-11 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - OSLab
    - 驱动
tags:   # 文章标签，参数可省略
---
## 摘要（Abstract）
### 介绍背景
驱动错误处理代码中存在很多bug，现有的方法忽视了驱动的特征，因此效率和准确性都受到限制。
### 我们做了什么工作
<!--more-->
步骤：

1. study了Linux驱动的源代码，找到了错误处理代码的三个特征/模式。
    * 函数的返回值被后一条语句判断
    * 函数的返回值被其他的驱动判断
    * 在声明和注释中明确指定要被检查
2. 设计了一种基于模式的目标函数抽取策略，将执行过程中会真实执行的并引发错误处理的函数视为目标函数并抽取出来。（执行monitor_driver.ko）
3. 在这些函数上进行错误注入，强制令函数返回值为引发错误处理的值。(生成test_driver.ko)
4. 执行插桩后的驱动，执行时监控资源获取和释放，识别系统崩溃。(执行test_driver.ko和monitor_driver.ko)
5. 执行后报告资源释放中的遗漏。（找到哪些错误处理代码中有bug）

### 我们这样做有什么用
我们能找到错误处理中Bug，在Linux的15款3类驱动中找到50个新Bug，并在执行测试时提高代码覆盖率。

### 我们的工作很先进
和先前的相关工作对比，我们对比数据很好，很有效。

## 简介/摘要（Introduction）
### 我们为什么要做这个/必要性
study了linux的驱动补丁，发现其中40%和错误处理代码有关，因此说明驱动里的错误处理还不够可靠，我们做检测错误处理里的bug很有必要。
### 现有的方法有局限性/不足
1. 错误注入（SFI）的典型性不够：随机注入不能很好地反映真实错误，*手动选择会忽略代表性：新驱动中要手动加入新函数，手选的会错会漏*。
2. 很多重复的测试用例，运行测试的时间太长。
3. 只有少数几种错误可以注入。
4. 错误注入的种类和位置要手工确定。

*现有的SFI方法目标在支持通用软件，但忽视了目标程序的特征。*，所以我们找到错误处理代码的特征。
### 我们做了什么工作
同上的Abstract
### 和别人比我们有什么优势
1. SFI有典型性，只对目标函数进行注入。
2. 有效的测试用例：错误处理里分支很少，只注入一个错误就够了。抽取策略过滤了很多不必要的函数。
3. 精确的bug检测：能识别很多不同类别的错误注入。运行在真实的硬件中。
4. 高自动化和可扩展性。

### 概述后续章节的内容

## 动机/Motivation
五类驱动：I2C bus drivers, PCI bus drivers, PowerPC
drivers, real-time clock (RTC) drivers and network
drivers
Fault Representativeness:反映被注入的错误能否触发错误处理
Target Function:正确的target应该是失败后能触发错误处理的函数
## 基于模式的抽取算法（详细描述算法）
## 方法/Approach
PF-Miner:寻找paired function
inserted probes:计数执行的代码数
## 评估/Evaluation
过滤率
准确率
召回率
## 相关工作/Related Work
## 总结/Conclusion
## 鸣谢/Acknowledgments


## 局限：
**有些错误处理不是函数返回值，而是读到的寄存器的值**
**不会走到的代码怎么处理：这里面也会有bug，想想能不能改变程序执行的流程，让代码能尽可能地走到，及提高代码的执行覆盖率。**
**基于fail-recovery：每个测试用例仅注入一个错误不能覆盖大部分错误处理代码**

让fail-recovery在合适的地方停止，就和fail-stop类似了。
解决方案（随便想几个）：
1. 每个测试用例运行100s就停止
2. 尝试判断recovery是否发生，在发生recovery时停止（发生则rec标志位+1）

注入错误就是将函数返回值赋为null或随机负值，但fail-recovery会对函数的返回值进行多个判断，根据返回值进入不同的分支，若只赋一个值将测试不到其他分支
```C
pci = mallocxx();
if(pci == -1)
{
    ...
}
else if(pci == -2)
{
    ...
}
```
解决方案（随便想几个）：
1. 对于这种函数，错误注入的值依次是后面判断语句中进入分支的值（若判断到属于此类函数，则每次发生recovery时不停止，而是根据rec标志位确定此次注入的值是进入第几个分支的值）

看linux4.2.1的源码


K. Cong, L. Lei, Z. Yang and F. Xie. Automatic fault injection
for driver robustness testing. In Proceedings of
the 2015 International Symposium on Software Testing
and Analysis, pages 361-372, 2015.
ADFI injects multiple faults in each test case. The advantage is that much more configuration and error handling code can be covered to detect more bugs.