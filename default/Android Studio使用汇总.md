title: Android Studio 使用问题汇总
date: 2016-01-21
tags: Android Studio,
---
## Gradle
> * gradle sync failed：Plugin is too old！  AndroidStudio2.0版本
```
将项目的gradle版本改成低版本，默认的是2.0.0-alpha1，可以改成1.2.3等，具体看情况
```
> * 出现如下报错：
```
Error:Unable to start the daemon process.  
This problem might be caused by incorrect configuration of the daemon.  
For example, an unrecognized jvm option is used.  
Please refer to the user guide chapter on the daemon at
...
```
解决办法：
```
即gradle中缺少默认配置，在当前用户的文件夹下的.gradle下新建gradle.properties文件，添加如下信息
org.gradle.jvmargs=-Xmx512m
```