﻿title: android-pluginmgr分析
date: 2016-01-07
tags: Android, 插件开发
---
android—pluginmgr这个插件框架和之前开发者在博客上介绍的已经不同了，今天主要就是记录下新的框架的开发流程。
这个框架的开发者之前介绍的的博客是：[http://blog.csdn.net/hkxxx/article/details/42194387](http://blog.csdn.net/hkxxx/article/details/42194387)。


## 之前该框架的总结
宿主中只注册一个Activity，加载插件的时候就动态生成对应插件中每个Activity的子类，子类的名称就是之前注册的那个Activity，包名也都一致。这个子类通过Dexmaker动态字节码的方式放到以这个类完整名称所命名的dex文件中，即一个Activity就有个对应的dex文件。
每个dex的类加载器都不一样，同个插件的Activity类共用一个插件的类加载器作为父加载器。这边会有个Map来保存对应关系。这里的亮点：依赖反转，宿主中动态生成插件中activity的子类。通过不同的类加载器来区分不同的插件的子类。

## 更新后的插件总结
更新之后的版本中，没有了动态生成dex的这个过程，也不会生成那么多的类加载器了。这里劫持了ActivityThread的Instrumentation，通过反射替换成自己自定义的Instrumentaiton。这个Instrumentation就是负责Activity实际跳转的。
自定义的Instrumentation中重写Activity的跳转的逻辑，当到跳转到插件中Activity时，这里会查看这个Activity有没有生成过，如果生成了，就是将要跳转的Activity的ActivityInfo替换成生成的ActivityInfo。没有生成的就会通过这个插件的类加载器来重新生成一个Activity，这个Acitivity。
