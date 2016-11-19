title: Android Gradle 官方文档翻译
date: 2016-06-22
tags: Android, Gradle
---
原文地址请戳[这里](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Introduction)（工具自备）

### 基本项目配置说明
Gradle项目是指根目录包含build.gradle构建脚本的项目。这个脚本描述了整个项目的构建过程。详细了解该构建系统请查看[Gradle用户指南](https://docs.gradle.org/current/userguide/userguide_single.html)

#### 简单脚本示例
最简单的Android项目的build.gradle中会包含如下信息：
```grooy
buildscript{
	repositories{
		jcenter()
	}
	dependencies{
		classpath 'com.android.tools.build:gradle:1.3.1'
	}
}
apply plugin: 'com.android.application'
android{
	compileSdkVersion 23
	buildToolsVersion "23.1.0"
}
```
这段代码中包含了3个部分：
`buildscript{...}`，这部分配置了构建系统的构建环境。在这个示例中，先声明了构建系统将使用jCenter库，接着定义了一个Maven依赖库，该库是个针对Android的Gradle插件库，版本是1.3.1
**注意**：这里的仓库以及依赖配置指的是构建时的构建系统，而不是项目的。项目的将会在下面声明。

然后引用了`com.android.application`插件，这个插件使用用来构建Android应用的。

最后，`android{...}`配置了整个android项目编译时需要的参数。这部分将用到[Android DSL](http://google.github.io/android-gradle-dsl/current/)的部分。默认只需要配置compileSdkVersion属性和builToolsVersion属性，指代编译时使用的sdk版本以及编译工具版本。

**重要提示**：你只能引用`com.android.application`插件，引用`java`插件将会在整个编译过程中报错。

**注意**：你也需要在项目的根目录中建立local.properties文件，该文件中配置`sdk.dir`字段来表明当前系统中Android Sdk路径。另一种方法，你也可以在环境变量中新建ANDROID_HOME选项来配置SDK路径。两种方法都可以。这里展示一个local.properties文件示例：
```bash
sdk.dir=/path/to/Android/Sdk
```

#### 项目结构
上面提到的构建文件都需要放在指定的文件结构中，Gradle遵循约定优于配置，尽量使用合理的配置。一般的项目代码包含两个组成部分，都是`sourc sets`，其中一个代表项目源码，另一个代表测试代码，分别在以下两个路径中：
> * src/main/
> * src/androidTest/
在每个目录下面，又会都有如下的两个子目录，一个是java源码目录，另一个是资源文件目录，android插件和Java插件都是这样。
> * java/
> * resources/
针对Android插件，另外还需要以下文件或目录：
> * AndroidManifest.xml
> * res/
> * assets/
> * aidl/
> * rs/
> * jni/
> * jniLibs/
如此，意味着项目的java文件将在src/main/java/中，manifest文件将是src/main/AndroidManifest.xml
**注意**：AndroidManifest.xml将会自动创建。

##### 配置项目结构
当默认的项目结构不能满足要求时，可以通过脚本来配置。Gradle的官方文档的[这个部分](https://docs.gradle.org/current/userguide/java_plugin.html#N12394)中介绍了配置纯Java项目的详细方法。
在Android插件中使用了相似的语法，但是因为Android中使用了自己的sourceSets，这些将会配置在android语句块中。
如下所示中：项目老的目录结构（用在Eclipse中的）放在main语句块中，将androidTest设置到tests目录中：
```grooy
android{
	sourceSets{
		main{
			manifest.srcFile 'AndroidManifest.xml'
			java.srcDirs=['src']
			resources.srcDirs=['src']
			aidl.srcDirs=['src']
			renderscript.srcDirs=['src']
			res.srcDirs=['res']
			assets.srcDirs=['assets']
		}
		androidTest.setRoot('tests')
	}
}
```
**注意**：因为老版的项目结构中将所有的源文件（java,aidl,renderscript）都放在同一个目录中，所以，我们也需要将这些文件全都对应到同一个src目录中
**注意**：`setRoot()`是将整个sourceSets（原目录和子目录）都移动到新目录中，这里就是将`src/androidTest/*`移到`test/*`中，这是android中独有的，不适用在java插件的sourceSets中。

#### 构建任务
##### 通用构建任务
当引用一个插件去构建文件都是自动生成一系列构建任务去运行。Java插件和Android插件都是如此。这些任务都遵从一下约定：
> * assemble
	这个任务是去生成项目的输出项
> * check
	这个任务是去运行所有检查
> * build
	这个任务就是做check和assemble这两个任务
> * clean
	这个任务清除整个项目的输出项
这3个任务实际上并不做任何事，他们是整个构建过程的定位符，这些任务依赖插件提供的任务来做实际的构建工作（PS：就和多态的意思差不多吧，可能实现是分成3个区块，代表这三个任务，然后插件往这个区块里面填具体的执行任务，外部调用时还是调用的这三个区块，实际运行还是区块内部任务运行。）
这样就允许你在不同的项目不同的插件中运行相同的任务。举例说明：引用findbugs插件将会创建一个新的任务并让check依赖于这个插件，保证了当调用check时都会调用这个任务。
通过命令行使用以下命令获得当前最高级的任务
```bash
gradle tasks
```
以下命令将会获取到整个任务列表以及任务间的关系：
```bash
gradle tasks --all
```
**注意**：gradle会自动监控一个任务的输入和输出
当没有对项目进行修改后重复运行build任务，gradle就会打印出`UP-TO-DATE`，意思是没有新操作发生。这样就会使任务能被合适的依赖，避免不必要的构建操作发生。

##### Java项目的构建任务
Java插件创建了两个最重要的任务，定位任务依赖关系如下：
> * assemble
	+ jar 这个任务用来创建输出
> * check
	+ test 这个任务运行测试
jar任务本身会直接或者间接的依赖于其他的任务，如classes任务就会编译java代码。项目的test的部分会被testClasses来编译，但是一般都是运行test来达到要求，test也是依赖于testClasses的。
通常情况，你一般只会调用asse或者check任务，忽略其他的任务，你可以通过[这里](https://docs.gradle.org/current/userguide/java_plugin.html)查看整个java插件的所有任务。

##### Android项目的构建任务
Android构建插件也遵循相同的插件任务约定，所以能和其他插件兼容，添加了以下定位任务：
> * assemble 这个任务会生成项目的各个输出项
> * check 这个任务会运行所有的验证任务
> * connectedCheck 验证确保有真机设备或者模拟器连接，多个设备连接时并发运行
> * deviceCheck 验证连接设备的系统Api版本，这个是通过CI(持续集成)服务器来运行的。
> * build 这个任务运行assemble和check
> * clean 这个任务清除这个项目的输出项

新的定位任务主要是为了保证当没有设备连接时也能运行常规的检查，注意build并不是依赖于deivceCheck或者是connectedCheck

一个Android项目最起码有至少两个输出项，一个Debug版本客户端，一个Release版本客户端。这两个都是分别通过两个对应的任务产生的：
> * assemble
	+ assembleDebug
	+ assembleRelease
这两个又分别依赖于其他的任务，这些任务通过多步来编译生成一个APK客户端，assemble 任务又依赖于这两个，所以会生成两个客户端。

这些check任务都有他们自己的依赖，
> * check
	+ lint
> * connectedCheck
	+ connectedAndroidTest
> * deviceCheck
	这个任务依赖与其他插件扩展的test节点。

最后，插件会创建任务去安装和卸载所有的编译生成的经过签名的客户端（Debug，Release，test）	
> * installDebug
> * installRelease
> * uninstallAll
	+ uninstallDebug
	+ uninstallRelease
	+ uninstallDebugAndroidTest

#### 基本的编译配置
Android插件允许通过DSL来直接配置相关的编译属性

##### Manifest元素
通过DSL，可以对Manifest中的很多重要元素进行配置，例如：
> * minSdkVersion
> * targetSdkVersion
> * versionCode
> * versionName
> * applicationId 即包名
> * testApplication 
> * testInstrumentationRunner
示例：
```bash
android{
	compileSdkVersion 23
	buildToolsVersion "23.0.1"
	
	defaultConfig{
		versionCode 12
		versionName "2.0"
		minSdkVersion 16
		targetSdkVersion 23
	}
}
```
请查看[Android插件DSL说明](http://google.github.io/android-gradle-dsl/current/)了解详细的可配置编译参数以及它们的默认值

将编译参数放到编译脚本中的好处是这些参数可以动态控制。这里就有个通过读取文件配置或者其他定制逻辑来获得versionName的示例：
```bash
def computeVersionName(){
	...
}
android{
	compileSdkVersion 23
	buildToolsVersion "23.0.1"
	defaultConfig{
		versionCode 12
		versionName computeVersionName()
		minSdkVersion 16
		targetSdkVersion 23
	}
}
```
**注意**：不要使用会和已存在的默认包含的get方法相冲突的方法名，比如`defaultConfig{...}`中使用了getVesionName会自动调用插件中defaultConfig.getVersionName而不是自己在外部配置的方法。

##### 编译类型
默认情况下，Android插件会自动设置项目同时编译Debug版本和Release版本，他们主要的不同就是是否可以在非开发模式的安全的设备上进行调试以及安装包是否签名，Debug版本是会在编译完成后自动使用一个已知的签名信息来签名，Release则不会，而是需要后期自己签名。

这些配置都会在BuildType这部分配置，默认的，一般都会有两个实例，debug和release。Android插件允许定制包括这两个实例的其他多个实例。这些都是在buildTypes中配置。
```bash
android{
	buildTypes{
		debug{
			applicationIdSuffix '.debug'
		}
		jnidebug{
			initWith(buildTypes.debug)
			applicationIdSuffix ".jnidebug"
			jniDebuggable true
		}
	}
}
```
以上的配置完成了以下几点：
> * 配置默认的debug版本编译类型，设置生成的Debug客户端的包名以debug结尾，这样debug版本和release版本都可以安装在同一个机器上
> * 创建另一个jnidebug编译版本然后配置先按照debug版本的初始化，然后通过后面的配置再针对jnidebug进行特殊配置
配置新的编译类型就是简单在buildTypes中的最后添加一个新的元素，在其中使用initWith()或者直接配置，查看[DSL说明](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html)了解详细的可配置编译参数。

为了修改编译参数，`Build Types`加相关的代码来配置。每一个编译类型都会有对应的sourceSet，一般对应的源码都是放在`src/<buildtypename>`，比如`src/debug/java`目录就会在编译debug版本客户端是被编译。同时这意味着编译类型不能是main或者是androidTest，这些都是插件内部强制使用的，并且，还需要保持唯一。
就像其他的SourceSet，源码路径也都是可以重新设置的
```bash
android{
	sourceSets.jnidebug.setRoot('foo/jnidebug')
}
```
另外，对于每个编译类型来做，都会有新的`assemble<BuildTypeName>`创建，比如`assembleDebug`。之前提及的assembleDebug和assembleRelease也都是这样来的。当Debug和Release编译类型将要被创建时，他们对应的任务都已经被创建了。根据这个规则，上面的build.gradle语句段中，也会自动生成assembleJnidebug任务，然后这个assemble也会同样的依赖与assembleDebug和assembleRelease任务。
**注意**：同样的你也可以使用aJ简写来运行assembleJnidebug任务。
以上可能的使用场景：
> * 某些权限只能在Debug版本使用，而不能在Release版本使用
> * 定制Debug版本的实现
> * 针对Debug版本定义不同的资源（比如可能一个值是和签名的证书相关的）




















