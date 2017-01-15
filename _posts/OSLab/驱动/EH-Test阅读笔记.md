---

title:  EH-Test阅读笔记

date: 2016-12-11 20:35:26 # 文章生成时间，一般不改

categories:   # 文章分类目录，参数可省略

    - OSLab

    - 驱动

tags:   # 文章标签，参数可省略

---
[TOC]

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

我们能找到错误处理中的Bug，在Linux的15款3类驱动中找到50个新Bug，并在执行测试时提高代码覆盖率。



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


## 局限

### 有些错误处理不是函数返回值，而是读到的寄存器的值

### 不会走到的代码怎么处理：这里面也会有bug，想想能不能改变程序执行的流程，让代码能尽可能地走到，及提高代码的执行覆盖率。

### 基于fail-recovery：每个测试用例仅注入一个错误不能覆盖大部分错误处理代码

### 错误注入的值不一定是-1和NULL，有很多都是和负整数进行比较，若遍历后面的比较值产生样例会造成测试用例爆炸增长吗？

让fail-recovery在合适的地方停止，就和fail-stop类似了。

解决方案（随便想几个）：

1. 每个测试用例运行100s就停止

2. 尝试判断recovery是否发生，在发生recovery时停止（发生则rec标志位+1）

注入错误就是将函数返回值赋为null或随机负值，但fail-recovery会对函数的返回值进行多个判断，根据返回值进入不同的分支，若只赋一个值将测试不到其他分支

```bash
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







### 阅读EH-Test源码记录

* 当pair中释放函数要释放的资源没有被申请，算bug吗？

* 判断函数的返回值是否被检查，里面的real和temp的具体含义？

* 用64位的虚拟机跑EH-Test会导致虚拟机出故障吗？如果只是不能正确运行出Bug检测但能运行来熟悉EH-Test流程，那我能试着在虚拟机里跑吗？

* 将EH-Test改成支持32位linux机器，如何通过ConstantInt* 获取它的值到32位的整型

* 当检测到bug时，只能知道是哪个函数出错了，能否定位到源代码中的位置（貌似转换成.bc后原始代码的位置就发生了改变，找找资料看看有没有可行的办法用于定位）

* `../../RemoveSymbol $bsfile`(将.bc改后缀变为.ll后执行这句的含义？)

* `rm -rf .*`的含义？（删除目录下所有.xxx的文件？）

```bash
rm -rf .* *.ko *.o *.mod.* *.symvers *.order

rm: 无法删除目录："."

rm: 无法删除目录：".."

make: *** [clean] 错误 1
```

* `dmesg -c`当完成打印显示后清除环缓冲内的内容？为什么要做这步？





### EH-Test使用流程
1. 将要插桩的驱动(sky2)进行卸载，（然后对Monitor这个用于监控的驱动编译[后面好像在GetInjectFun中有编译Monitor这步]）
2. 进入要插桩的驱动的源码文件夹，执行`./GetInjectFun.sh sky2`
3. 根据需要修改监控得到的func.txt（前面加`****`表示明显不是target的，`**`表示一定会引起system crashes or hands的，这都不需要进行错误注入）
4. 执行`./FaultTest.sh sky2`
5. current.txt记录的是当前插桩到的函数是哪个

### CompileDriver.sh流程

1. 在当前目录下新建tmp/和mytest/文件夹

2. 将当前编译器（$(CC)）变为`clang EXTRA_CFLAGS+="-emit-llvm -S"`(意思是编译成.ll？)，再加"-k"选项对驱动进行编译。

3. 对当前文件夹中的所有.o文件：

	* 将.o文件重命名为.ll后缀

	* 执行`../../RemoveSymbol $.ll_file`

	* 将RemoveSymbol后的.ll后缀文件拷贝到mytest/文件夹

	* 插桩：根据"-r"还是"-f"进行插入监控函数还是错误注入

	* 将.ll后缀通过llvm-dis反汇编成.ll文件

	* 将.ll文件变为.s文件：`clang -O2 -mcmodel=kernel -S $bsfile -o $ssfile -mno-sse -mno-mmx`

	* 将.s文件变为.o文件：`gcc -mcmodel=kernel -c $ssfile -o $objfile`

	* 将生成的.o文件移动到tmp/目录下

	* 删除.s和.ll文件，最终只有tmp/目录下有插桩后的.o文件

4. 将当前目录的Makefile和`../../Monitor/Module.symvers`拷贝到tmp/目录下

5. 进入tmp/目录，执行`make`对.o文件进行编译

6. 将编译生成的.ko模块移动到../

7. 进入../，删除tmp/文件夹和modules.order文件

8. 最终mytest/文件夹下只有原始.o改后缀为.ll再经过RemoveSymbol后的.ll后缀文件，在当前目录多了生成的驱动.ko模块



### GetInjectFun.sh流程

1. 进入要插桩的驱动的源码文件夹，编译Monitor得到MyMonitor.ko

2. `./CompileDriver.sh -r`对驱动进行插入监控函数的插桩，生成驱动模块

3. `insmod MyMonitor.ko run_mode=1`表示MyMonitor.ko进入记录执行函数的模式

4. `insmod $1".ko"`装载插桩后的驱动

5. 执行workload

6. 卸载驱动，再卸载MyMonitor.ko

7. 对监控得到的"/tmp/LogFile"文件进行去重和过滤，输出到temp.txt作为要筛选目标函数的原始函数列表

8. 执行`../../EHTest -a $file temp.txt func.txt`，获取返回值被检查的函数作为func.txt。

9. 输出temp.txt和func.txt的行数



### FaultTest流程

1. 将../../PairChecker/PairFile拷贝到/tmp/PairFile

2. `dmesg -c`当完成打印显示后清除环缓冲内的内容。

3. 对于fun.txt中的每一个行：

	* 跳过以*开头的行

	* 将当前行写入current.txt，代表目前插桩到哪个函数

	* 执行`./CompileDriver.sh -f $func $caller ../../PairChecker/PairFile`根据func(被调用函数)和caller(调用函数)找到目标函数的位置再进行错误注入，生成错误注入后的驱动

	* `../../Monitor/MyMonitor.ko run_mode=2 bugfile="/tmp/BugFile"`进行运行时系统资源获取和释放的监控

	* insmod 错误注入后的驱动

	* 执行workload，将运行结果写入"/tmp/BugFile"

	* 卸载驱动，再卸载MyMonitor.ko，将资源没有释放的获取函数写入/tmp/BugFile

	* `dmesg -c`当完成打印显示后清除环缓冲内的内容。

	* count计数加1，但count未写入文件，建议count,总行数,count/总行数都写入/tmp/BugFile代表目前的完成度

4. 将/tmp/BugFile拷贝到当前目录，命名为PairBugFile







































