[Ant编译的jar文件，ANE不识别](http://zengrong.net/post/1855.htm)

## 问题描述

Android的ANE打包需要jar文件。Eclipse可以提供jar文件的导出。

然而，当我使用Ant来自动化完成ANE打包流程的时候，jar文件出了问题。

如果使用Ant生成的jar文件来打包ANE。那么ANE在使用的时候，会发生 `ExtensionContext` 无法初始化的情况。

也就是说，在调用 `ExtensionContext.createExtensionContext(EXTENSION_ID)` 的时候，得到的永远是null。

## 问题分析

以下是构建jar的target：<!--more-->

<pre lang="XML">
<target name="android" description="Build Android Library">
	<delete dir="../android/temp"/>
	<mkdir dir="../android/temp/classes"/>
	<echo message="Using Java version ${ant.java.version}."/>
	<javac source="1.6" srcdir="../android/src" destdir="../android/temp/classes" includeantruntime="false">
		<classpath>
			<pathelement location="${android.sdk}/android.jar"/>
			<pathelement location="${flex.sdk}/lib/android/FlashRuntimeExtensions.jar"/>
			<pathelement location="../android/libs/android-support-v4.jar"/>
			<pathelement location="${android.tools}/support/annotations.jar"/>
		</classpath>
	</javac>
	<mkdir dir="../temp/android/"/>
	<jar basedir="../android/temp/classes" destfile="../temp/android/lib${name}.jar"/>
	<copy todir="../temp/android/res/">
		<fileset dir="../android/res"/>
	</copy>
	<delete dir="../android/temp"/>
</target>
</pre>

Ant打包得到的jar文件，比使用Eclipse导出的jar文件要小。解压打包的jar文件之后，我发现使用Ant编译出来的class文件比Eclipse编译的要小。

于是，应该从javac上找原因。

## 解决过程

### 酱油debug

先为JAVAC task加上 `debug="on"` 参数。这样编译出来的class文件，大小和Eclipse类似了。但是做成的ANE依然不管用。

### JDT编译器

因为Eclipse的编译器和Ant默认使用的编译器是不同的，我尝试将Ant的编译器换成Eclipse看看。使用JDT编译器的步骤为：

1. 进入 [Eclipse downloads](http://download.eclipse.org/eclipse/downloads/) 找一个版本，进入下载页面，搜索 `JDT Core Batch Compiler`，可以下载到JDT编译器；
1. 将解压后得到的 `ecj-<version>.jar` 复制到Ant的lib目录；
1. 为JAVAC task加入 `compiler="org.eclipse.jdt.core.JDTCompilerAdapter"` 参数，运行task。

JAVAC报告如下错误：

>Target level '1.2' is incompatible with source level '1.6'. A target level '1.6' or better is required

为JAVAC task加入 `target="1.6"` 参数，运行task正常。完整JAVAC参数如下：

<pre lang="XML">
.....
<javac source="1.6" srcdir="../android/src" destdir="../android/temp/classes" includeantruntime="false" compiler="org.eclipse.jdt.core.JDTCompilerAdapter" target="1.6">
.....
</pre>

然后生成ANE进行调试，一切正常，问题解决。

### javac编译器

既然使用JDT编译器正常，那么使用javac的默认编译器（JDK自带的编译器）是否也正常？

删除 `compiler` 参数设置，保留 `target` 参数设置，运行task正常，生成ANE调试，一切正常。

<pre lang="XML">
<target name="android" description="Build Android Library">
	<delete dir="../android/temp"/>
	<mkdir dir="../android/temp/classes"/>
	<echo message="Using Java version ${ant.java.version}."/>
	<javac source="1.6" target="1.6" srcdir="../android/src" destdir="../android/temp/classes" includeantruntime="false">
		<classpath>
			<pathelement location="${android.sdk}/android.jar"/>
			<pathelement location="${flex.sdk}/lib/android/FlashRuntimeExtensions.jar"/>
			<pathelement location="../android/libs/android-support-v4.jar"/>
			<pathelement location="${android.tools}/support/annotations.jar"/>
		</classpath>
	</javac>
	<mkdir dir="../temp/android/"/>
	<jar basedir="../android/temp/classes" destfile="../temp/android/lib${name}.jar"/>
	<copy todir="../temp/android/res/">
		<fileset dir="../android/res"/>
	</copy>
	<delete dir="../android/temp"/>
</target>
</pre>

## 结论

原来，导致class不能使用的真正原因，并非编译器差别，而是在编译的时候没有设置目标版本。Eclipse编译设置中，默认就为编译参数加上了目标版本，而Ant则没有。

我的自动编译ANE功能终于完善了。

## 参考

* [Compiling Java code](http://help.eclipse.org/juno/nav/1_3_8)
* [Why are class files different size when compiling the same code in eclipse, and then with the eclipse compiler via ant?](http://stackoverflow.com/questions/3629158/why-are-class-files-different-size-when-compiling-the-same-code-in-eclipse-and)
* [Eclipse vs. javac (ant) compilation problem](http://forum.springsource.org/showthread.php?31252-Eclipse-vs-javac-(ant)-compilation-problem)

