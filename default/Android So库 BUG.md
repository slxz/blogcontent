#Android So库 BUG
@(学习笔记)[android bug]
## bug 1：64位机型，couldn't find "*.so"
> * 错误日志：/data/app/******/lib/arm64, /vendor/lib64, /system/lib64]]] couldn't find "*.so"
> * 出现机型：魅族MX5等arm64位手机上
> * 问题原因：根据我的理解是，如果apk中包含了arm64位的文件夹，系统就会默认app使用的是64位的so库，因此就只会寻找64位的文件夹，从而不再去寻找32位的文件夹了。导致报找不到so库这个错误。
> * 注意：armebi与armebi-v7a也存在类似的问题，目前的做法就是只保留armebi和x86这两个文件夹中的so库，起到最大化适配的作用
> * 相关阅读：以下内容可能与我理解的有所出入
>   http://www.cnblogs.com/poe-blog/p/4728970.html
>   http://blog.csdn.net/lihuapinghust/article/details/45825063