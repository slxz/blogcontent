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
	> * jar 这个任务用来创建输出
> * check
	> * test 这个任务运行测试
jar任务本身会直接或者间接的依赖于其他的任务，如classes任务就会编译java代码。项目的test的部分会被testClasses来编译，但是一般都是运行test来达到要求，test也是依赖于testClasses的。
通常情况，你一般只会调用asse或者check任务，忽略其他的任务，你可以通过[这里](https://docs.gradle.org/current/userguide/java_plugin.html)查看整个java插件的所有任务。

##### Android项目的构建任务


