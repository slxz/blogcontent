title: Android 加固技术分析
date: 2016-10-07
tags: Android, 安全，反编译
---
### 资料收集
> * [早期大致总结](http://www.freebuf.com/articles/terminal/42525.html)
> * [加固具体实现博客](http://blog.csdn.net/androidsecurity/article/details/8809542)，这篇还是比较早期的文章，只是对dex文件做处理
> * [各大补丁方案分析与比较](http://blog.zhaiyifan.cn/2015/11/20/HotPatchCompare/)
> * [Android中的Apk的加固(加壳)原理解析和实现](http://blog.csdn.net/jiangwei0910410003/article/details/48415225)
> * [Android安全专项-Apk加固](http://blog.csdn.net/itfootball/article/details/50962459)
> * [Andorid APK反逆向解决方案---梆梆加固原理探寻](http://blog.csdn.net/androidsecurity/article/details/8892635)
> * [Android中apk加固完善篇之内存加载dex方案实现原理(不落地方式加载)](http://blog.csdn.net/jiangwei0910410003/article/details/51557135)
> * [仿爱加密模式加固](http://www.cnblogs.com/joey-hua/p/5402599.html)
> * [Apk脱壳圣战之—脱掉“360加固”的壳](https://1x0.xyz/archives/999.html)
> * [Android逆向之旅—动态方式破解apk终极篇(应对加固apk破解方式)](http://www.wjdiankong.cn/android%E9%80%86%E5%90%91%E4%B9%8B%E6%97%85-%E5%8A%A8%E6%80%81%E6%96%B9%E5%BC%8F%E7%A0%B4%E8%A7%A3apk%E7%BB%88%E6%9E%81%E7%AF%87%E5%BA%94%E5%AF%B9%E5%8A%A0%E5%9B%BAapk%E7%A0%B4%E8%A7%A3%E6%96%B9/)