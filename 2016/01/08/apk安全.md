title: Android反编译对抗
date: 2016-01-08
tags: Android, 反编译
---
## 资料收集
[APK伪加密](http://bbs.pediy.com/showthread.php?t=187789)
[反编译对抗1](http://blog.csdn.net/asmcvc/article/details/14126507)
[反编译对抗2](http://my.oschina.net/u/2323218/blog/406860)

## 起因
做出来的应用拿到安全公司检测，发现了一堆问题，美其名高危漏洞。
主要问题：
> * 1、客户端未做反编译对抗
> * 2、客户端未进行完整性校验
> * 3、使用系统键盘，易被劫持
> * 4、组件暴露
> * 5、组件劫持

## 处理
主要还是做了加壳处理，使用了爱加密的加壳服务，还能防止二次打包。

## 分析
### 1、组件暴露
组件暴露即是说Android的组件：Activity，BroadcastReceiver，Service能够通过Intent在其他程序中被调用。
这类问题可以使用如下防护措施：
**andorid:exported属性**
在AndroidMenifest中相关的组件声明时候，添加`android:exported="false"`属性，这个属性表示这个组件是本程序私有的，只有本程序的组件才可以调用。
```xml
<activity
	...
	android:exported="false"
	... />
```
**设置组件访问权限**
首先，在组件的声明中添加自定义权限
```xml
<activity
	...
	android:permission="com.test.test"
	... />
```
然后，同样声明`<permission>`属性
```xml
<permission
	android:description="test"
	android:label="test"
	android:name="com.test.test"
	android:protectionLevel="normal"
	/>
```
这里说明下`android:protectionLevel`有4个级别：`normal`, `dangerous`, `signature`, signatureOrSystem`。其中后面两个指定只有同签名的应用才可以调用
最后，在调用的应用中，声明权限
```xml
<uses-permission android:name="com.test.test" />
```
这里还有通过代码来进行权限的验证：
```java
if(context.checkCallingOrSelfPermission("com.test.test") != PackageManager.PERMISSION_GRANTED){
	// 没有权限
}else{
	// 有权限
}
```
#### 参考
[android应用安全——组件通信安全（Intent）](http://blog.csdn.net/xyz_lmn/article/details/8803467)
[Android安全机制（2） Android Permission权限控制机制](http://blog.csdn.net/vshuang/article/details/44001661)

### 2、组件劫持

### 3、完整性校验
完整校验即是通过对证书的比较来进行的，有服务器的建议在服务端做验证，本地也可以做验证，但是安全性低。
目前项目中的安全性校验代码：
```java
// 获得当前程序证书的方法
String packageName = this.getPackageName();
PackageInfo packageInfo = this.getPackageManager().getPackageInfo(packageName, PackageManager.GET_SIGNATURES);
Signature[] signs = packageInfo.signatures;
Signature sign = signs[0];
CertificateFactory certFactory = CertificateFactory.getInstance("X.509");
X509Certificate cert = (X509Certificate)certFactory.generateCertificate(new ByteArrayInputStream(sign.toByteArray()));
RSAPublicKey key = (RSAPublicKey)cert.getPublicKey();
BigInteger biKey = key.getModulus();
pubKey = biKey.toString();
```