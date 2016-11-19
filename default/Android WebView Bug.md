# Android WebView Bug
@(学习笔记)[android bug]

## Bug 1：WebView 多次加载后显示空白
> * 错误日志：待获取
> * 出现机型：ALL
> * 问题原因：根据我的理解是，打包后或者打包过程中针对字节码修改，然后重新打包，可能会造成程序不稳定，WebView因为复杂，可能显示的现象更明显。有以上的分析，是因为在一开始的打包中，会嵌入听云SDK，而听云SDK是会在打包过程中进行字节码的Hook（nbs.newlens.class.rewriter.jar），去掉这块后本现象就不会发生了。还有，打包完成后如果进行了反编译，修改了smali文件，再进行回编译，也会造成这种现象，现在还需要排除下具体是反编译工具的问题还是apk本身结构就不支持如此频繁的修改。

## Bug 2：Webview调用照相机，无图片
> * 错误日志：指定文件没有生成
> * 问题原因：这个问题其实不是Webview的问题，是调用时图片保存路径设置的问题。因为是Webview调用的，所以写在这里。
>```java
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(photoFile));
activity.startActivityForResult(intent, requestCode);
>```
> 这两行代码就是调用摄像头拍照的代码，这里指定了图片的输出路径，当在设置了相关权限后，还需要注意，这个路径是不能设置为程序内部存储中，要放在SD卡中。否则会包错，即photoFile需要在getExternalXXX之类的路径中。
> * 解析部分，也是因为兼容性的问题，需要在onActivityForResult中详细实现将文件从Content转化为具体File类型的方式，然后在通过Uri.fromFile(file)生成新的uri来处理
> * **注意：**以上的代码中设置了拍摄图片的保存路径，其实最好不要使用指定的路径，直接通过将图片放入相册中是最好的结果。相关代码：
> ```java
ContentValues values = new ContentValues();
Uri photoUri = getContext().getContentResolver().insert(MediaStore.Image.Media.EXTERNAL_CONTENT_URI, values);
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
intent.putExtra(MediaStore.EXTRA_OUTPUT, photoUri);
activity.startActivityForResult(intent, requestCode);
> ```

## Bug 3：WebView在小于5.0的系统上调用失败
> * 错误现象：通过打包服务器打包后，在4.4及以下系统上点击webview中的选择图片按钮无法弹框。
> * 出现机型：版本低于5.0的机型
> * 问题原因：混淆删除了没有被调用的用于适配3.0到4.4的`openFileChooser`方法。
> * 具体：由于webview暴露的相关api一直在变化，当webview中的html需要进行打开文件操作时，需要同时适配多个版本的api。但是当我们使用打包工具打包的时候因为需要经过混淆，而混淆会有个优化代码的过程，会把适配其他api版本的方法当作无用方法给过滤掉，从而打出来的包中会缺少调用方法
> * 处理：在混淆文件`proguard-project.txt`中加入如下代码。
>```bash
-keepclassmembers class XXXXX{
    public *
}
>```

