---
title: Apk 瘦身指南
tags:
	- optimize
	- app size
	- tools
categories:
	- [optimize]
---
原文：[Build your Android app Faster and Smaller than ever][Line-build-faster-smaller-app]
APK 的大小会影响到 app 加载的速度，内容的占用以及电量的消耗。首先推荐优化 APK 大小的工具就是 Android Studio 自带的 APK Analyzer。你可以通过在 Android Studio 选择 "Profile or debug APK"浏览 APK 文件。 APK Analyzer 会将 APK 文件的内容分解归类，如 layouts, images, classes, libraries 等。

#### Tip 1: 删除无用资源
开发遗留项目时，会遇到很多从未使用的图片、布局和字符串文件，但是你又不想自己移除它们，因为担心可能引起 app 崩溃，Android Studio 内提供了这个功能：Refactor-Remove Unused Resources.

#### Tip 2: 按需添加依赖资源
举例来说，就是如果你需要使用 Google Auth，那么你只需添加 `com.google.android.gms:play-services-auth:16.x.x` 而不是 `com.google.android.gms:play-services:16.x.x`
。

你可以下面的命令通过分解依赖资源：

```
$ ./gradlew app:dependencies
```

获得如下依赖分解图：

```
...
+--- com.android.support:design:28.0.0
|    +--- com.android.support:support-annotations:28.0.0
|    +--- com.android.support:support-compat:28.0.0 (*)
|    +--- com.android.support:support-core-ui:28.0.0 (*)
|    +--- com.android.support:support-core-utils:28.0.0 (*)
|    +--- com.android.support:support-fragment:28.0.0 (*)
|    +--- com.android.support:transition:28.0.0
|    |    +--- com.android.support:support-annotations:28.0.0
|    |    \--- com.android.support:support-compat:28.0.0 (*)
|    +--- com.android.support:appcompat-v7:28.0.0 (*)
|    +--- com.android.support:cardview-v7:28.0.0
|    |    \--- com.android.support:support-annotations:28.0.0
|    \--- com.android.support:recyclerview-v7:28.0.0
|         +--- com.android.support:support-annotations:28.0.0
|         +--- com.android.support:support-compat:28.0.0 (*)
|         \--- com.android.support:support-core-ui:28.0.0 (*)
+--- com.android.support.constraint:constraint-layout:1.1.3
|    \--- com.android.support.constraint:constraint-layout-solver:1.1.3
\--- junit:junit:4.12
     \--- org.hamcrest:hamcrest-core:1.3
...
```
#### Tip 3: 为不同的屏幕分辨率构建不同的 APKs
Android Studio 默认会生成一个供所有分辨率通用的 APK 安装包，这个 tip 你可以在 app/build.gradle 里定义排除/包括不用的屏幕分辨率，Android Studio 会生成不同的 APK:

```groovy
android {
	splits {
	  density {
	    enable true

	    // Specify a list of screen densities which Gradle won't create multiple APKs for
	    exclude 'ldpi', 'mdpi'

	    // Specify a list of compatible screen size for the manifest
	    compatibleScreens 'small', 'normal', 'large', 'xlarge'
	  }
	}
}
```
+ 生成的文件 `~\app\build\outpus\apk\debug`：

```
+--- ~\apk\debug
|    +--- app-hdpi-debug.apk
|    +--- app-universal-debug.apk
|    +--- app-xhdpi-debug.apk
|    +--- app-xxhdpi-debug.apk
|    +--- app-xxxhdpi-debug.apk
|    \--- output.json
```

#### Tip 4: 为不同的 ABIs 构建不同的 APKs
跟 Tip 3比较类似，这个 tip 是为了支持不同的 Application Binary Interfaces(ABIs)。目前市面上有7种CPU架构，其中的 3 种应该是用户不常见的，如：mips, mips64 以及 armeabi.你可以在 app/gradle.build 定义 ABIs 相关属性，Android Studio 会生成不同 APKs:

```groovy
android {
	splits {
	  abi {
      enable true

      reset()

      // Specify a list of ABIs that Gradle should create APKs for
      include 'x86', 'x86_64', 'arm64-v8a', 'armeabi-v7a'

      // If you don't want to generate a universal APK that includes all ABIs.
      universalApk false
	  }
	  density {...}
	}
}
```
+ 生成的文件 `~\app\build\outpus\apk\debug`：

```
+--- ~\apk\debug
|    +--- app-arm64-v8a-debug.apk
|    +--- app-armeabi-v7a-debug.apk
|    +--- app-x86-debug.apk
|    +--- app-x86_64-debug.apk
|    +--- app-universal-debug.apk
...
|    \--- output.json
```

#### Tip 5: 为特定的 ABIs 构建 APK
跟 Tip 4 不同，这个 tip 会根据定制的 ABIs 构建一个 APK:

```groovy
android {
  defaultConfig {
    ...
    ndk {
      abiFilters 'x86', 'x86_64', 'arm64-v8a', 'armeabi-v7a'
      // armeabi, mips and mips64 has removed since NDK r17
    }
  }
}
```

#### Tip 6: 移除无用的可替换资源
有时候我们只需要特定国家的语言，`resConfigs` 属性可以帮助我们限定：

```groovy
android {
  defaultConfig {
    resConfigs 'en', 'cn'
    ...
  }
}
```

#### Tip 7: 压缩未使用的代码和资源
默认 `minifyEnabled` 是 false 状态，设为 true 可以压缩可混淆代码，另外推荐添加 `shrinkResources` 以便在 minify 之后移除无用的资源文件。

```groovy
android {
  buildTypes {
    release {
      minifyEnabled true
      
      shrinkResources true
      proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }
  }
}
```

#### Tip 8:使用 Shape Drawable
对于一些纯色或渐变色的图片背景我们可以使用 Shape Drawable 替代。

#### Tip 9:使用 WebP
对于无法用 Shape Drawable 替换的图片，使用 WebP 格式替换。

#### Tip 10: 使用 VectorDrawable

[Line-build-faster-smaller-app]:https://medium.com/linedevth/build-your-android-app-faster-and-smaller-than-ever-25f53fdd3cdc
