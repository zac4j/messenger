---
title:Launch App from Web
tags: 
- hybrid
categories: 
- [hybrid]
---

Hybrid 架构中常见的几种 Launch App 的方式：
+ Android intents with chrome
+ Create deep links to app content

### Android Intent with Chrome
在 Chrome for Android ver.18 或更早之前，Android 系统允许网页通过 Android intent 启动 app。一种场景是网页内嵌入 iframe 并且 src 设定自定义的 URI-scheme:
```xml
<iframe scr="zacash://launchapp"></iframe>
```

在 Chrome for Android ver.25 以后，不再允许通过在 iframe 设置 src 属性的方式启动 app 功能。取而代之，你需要定义 `intent:` 句式来启动 app。

#### 最佳实践
构造 `intent` 句式的最佳实践是将其 anchor 和 embed 到网页中启动 app。我们可以通过 [Intent Extras][intent_extra] 方便地传递 extra 信息。
基本的语法是：
```
intent:
	HOST/URI-path		// Optional host
	#Intent;
		package=[string];
		action=[string];
		category=[string];
		component=[string];
		scheme=[string];
		S.<extra>=[string];
	end;
```

当 intent 不能被识别或者外部 app 不能被启动，用户将会被重定向到定义的 fallback URL `S.browser_fallback_url=[encoded_full_url]`。

一些通过 Chrome 无法启动外部 app 可能的原因有：
+ Intent 无法被识别，没有 app 适配当前 intent
+ JavaScript timer 试图不经用户点击 button 直接打开 app

#### 示例
假设我的 App 的 CaptureActivity 在 manifest 文件中的声明像这样：
```xml
<!-- Support zxing://scan/?... like iPhone app -->
<activity android:name=".CaptureActivity"
          android:screenOrientation="sensorLandscape"
          android:clearTaskOnLaunch="true"
          android:stateNotNeeded="true"
          android:theme="@style/CaptureTheme"
          android:windowSoftInputMode="stateAlwaysHidden">
  <intent-filter>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <data android:scheme="zxing" android:host="scan" android:path="/"/>
  </intent-filter>
</activity>
```
为了启动 Zxing 扫码 app ，我们将 anchor 的 href 编码为：
```html
<a href="intent://scan/#Intent;scheme=zxing;package=com.google.zxing.client.android;end"> Take a QR code </a>
```
假设有 fallback URL 定义的话：
```html
<a href="intent://scan/#Intent;scheme=zxing;package=com.google.zxing.client.android;S.browser_fallback_url=http%3A%2F%2Fzxing.org;end"> Take a QR code </a>
```
这样假设 app 未能启动，我们将会跳转到 fallback URL.

#### 注意事项
+ 具有 [`android.intent.category.BROWSABLE`][category_browsable] filter 的 Activity 能够使用此方法调用，因为它表明 app 可以安全地从浏览器打开。
+ 以下场景 Chrome 不会启动外部 app：
	- Intent URI 由输入的 URL 重定向而来。
	- Intent URI 未经用户操作直接启动。

[intent_extra]:http://developer.android.com/guide/components/intents-filters.html#extras
[category_browsable]:http://developer.android.com/reference/android/content/Intent.html#CATEGORY_BROWSABLE