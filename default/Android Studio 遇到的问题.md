# Android Studio 遇到的问题
@(学习笔记)[android studio]

### AndroidStudio调试时报NosuchField或者NosuchField
> * 现象：Debug过程中无法获取相关变量值，报NosuchField，NosuchField
> * 原因：
> 1、工程中出现重复包名，实际被编译的代码不是当前断点的代码
> 2、androidstudio编译过程中没有将上一次的build目录删除掉，这个文件是只读属性的
> * 验证：经验证，我这边目前出现的状况是第二种
