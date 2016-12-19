---
title:  Eclipse启动报错java.lang.NoClassDefFoundError
date: 2016-04-14 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Java
tags:   # 文章标签，参数可省略
    - 
---
今天一打开Eclipse就报错，java.lang.NoClassDefFoundError
<!--more-->
报错信息如下：
```java
!ENTRY org.eclipse.osgi 4 0 2016-04-14 10:31:06.244
!MESSAGE Application error
!STACK 1
java.lang.NoClassDefFoundError: org/eclipse/core/resources/IContainer
    at org.eclipse.ui.internal.ide.application.IDEApplication.start(IDEApplication.java:136)
    at org.eclipse.equinox.internal.app.EclipseAppHandle.run(EclipseAppHandle.java:196)
    at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.runApplication(EclipseAppLauncher.java:134)
    at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.start(EclipseAppLauncher.java:104)
    at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:380)
    at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:235)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:497)
    at org.eclipse.equinox.launcher.Main.invokeFramework(Main.java:648)
    at org.eclipse.equinox.launcher.Main.basicRun(Main.java:603)
    at org.eclipse.equinox.launcher.Main.run(Main.java:1465)
Caused by: java.lang.ClassNotFoundException: An error occurred while automatically activating bundle org.eclipse.core.resources (77).
    at org.eclipse.osgi.internal.hooks.EclipseLazyStarter.postFindLocalClass(EclipseLazyStarter.java:116)
    at org.eclipse.osgi.internal.loader.classpath.ClasspathManager.findLocalClass(ClasspathManager.java:531)
    at org.eclipse.osgi.internal.loader.ModuleClassLoader.findLocalClass(ModuleClassLoader.java:324)
    at org.eclipse.osgi.internal.loader.BundleLoader.findLocalClass(BundleLoader.java:320)
    at org.eclipse.osgi.internal.loader.sources.SingleSourcePackage.loadClass(SingleSourcePackage.java:36)
    at org.eclipse.osgi.internal.loader.BundleLoader.findClassInternal(BundleLoader.java:391)
    at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:345)
    at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:337)
    at org.eclipse.osgi.internal.loader.ModuleClassLoader.loadClass(ModuleClassLoader.java:160)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
    ... 13 more
Caused by: org.osgi.framework.BundleException: Exception in org.eclipse.core.resources.ResourcesPlugin.start() of bundle org.eclipse.core.resources.
    at org.eclipse.osgi.internal.framework.BundleContextImpl.startActivator(BundleContextImpl.java:792)
    at org.eclipse.osgi.internal.framework.BundleContextImpl.start(BundleContextImpl.java:721)
    at org.eclipse.osgi.internal.framework.EquinoxBundle.startWorker0(EquinoxBundle.java:936)
    at org.eclipse.osgi.internal.framework.EquinoxBundle$EquinoxModule.startWorker(EquinoxBundle.java:319)
    at org.eclipse.osgi.container.Module.doStart(Module.java:571)
    at org.eclipse.osgi.container.Module.start(Module.java:439)
    at org.eclipse.osgi.framework.util.SecureAction.start(SecureAction.java:454)
    at org.eclipse.osgi.internal.hooks.EclipseLazyStarter.postFindLocalClass(EclipseLazyStarter.java:107)
    ... 22 more
Caused by: org.eclipse.core.internal.resources.ResourceException: Incompatible file format. Workspace was saved with an incompatible version: 0.
    at org.eclipse.core.internal.resources.WorkspaceTreeReader.getReader(WorkspaceTreeReader.java:58)
    at org.eclipse.core.internal.resources.WorkspaceTreeReader.getReader(WorkspaceTreeReader.java:66)
    at org.eclipse.core.internal.resources.SaveManager.restoreTree(SaveManager.java:1036)
    at org.eclipse.core.internal.resources.SaveManager.restore(SaveManager.java:699)
    at org.eclipse.core.internal.resources.SaveManager.startup(SaveManager.java:1565)
    at org.eclipse.core.internal.resources.Workspace.startup(Workspace.java:2464)
    at org.eclipse.core.internal.resources.Workspace.open(Workspace.java:2219)
    at org.eclipse.core.resources.ResourcesPlugin.start(ResourcesPlugin.java:447)
    at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run(BundleContextImpl.java:771)
    at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run(BundleContextImpl.java:1)
    at java.security.AccessController.doPrivileged(Native Method)
    at org.eclipse.osgi.internal.framework.BundleContextImpl.startActivator(BundleContextImpl.java:764)
    ... 29 more
```
Eclipse启动不了，经过查阅资料，发现解决方法是：
***workspace/.metadata/.plugins/org.eclipse.core.resources/.snap 删除后再启动即可。***
但我的org.eclipse.core.resources目录下没有.snap文件。

于是发现可能是昨天新添加的一个工程找不到类，***就将.project文件夹删除***，再重新启动Eclipse，于是可以成功启动，只是要将以前在workspace中的工程重新导入而已。

### eclipse导入已经存在workspace里的工程
按照file->import->general->existing project into workspace->browse，选择了要导入的工程或者整个workspace，这时下一步是灰色的，点不了，显示Some projects cannot be imported because they already exist in the workspace.
只需要把copy to worksapce的勾去掉,然后点击refresh一下,就可以了。