---
title: Launch App from Web
tags:
	- hybrid
categories:
	- [hybrid]
---

Hybrid 架构中常见的几种 Launch App 的方式：
+ Android intents with chrome
+ Deep linking & Android App Links
+ 应用宝 AppLinks 接入

### Android Intent with Chrome
在 Chrome for Android ver.18 或更早之前，Android 系统允许网页通过 Android intent 启动 app。一种场景是网页内嵌入 iframe 并且 src 设定自定义的 URI-scheme:
```xml
<iframe src="zacash://launchapp"></iframe>
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

### [Deep linking & Android App Links][applinks]

#### Deep linking
Deep links 是指允许用户直接访问 app 中特定内容的 URL。Android 开发中 ，我们可以通过配置 `intent-filters` 并从传入的 intent 中取出数据将用户带到相应的页面。

**a.配置：**
+ AndroidManifest 页面配置 intent-filters:
```xml
<activity
	android:name="com.zac4j.applinks.AppLinksActivity"
	android:label="@string/app_name"
	android:launchMode="singleTask">
	<intent-filter android:label="@string/filter_view_zac4j">
		<action android:name="android.intent.action.VIEW"/>
		<category android:name="android.intent.category.DEFAULT"/>
		<category android:name="android.intent.category.BROWSABLE"/>
		<!-- Accepts URIs that begin with "zac4j://zaccc” -->
		<data
		    android:host="zaccc"
		    android:scheme="zac4j"/>
	</intent-filter>
</activity>
```
+ AppLinksActivity 页面取出 intent 数据：
```java
override fun onCreate(savedInstanceState: Bundle?) {
  super.onCreate(savedInstanceState)
  setContentView(R.layout.activity_main)

  readIntentData()
}

/**
 * Retrieve data from Intent
 * sample scheme => zac4j://zaccc?actionId=jumpPage&pageId=10086
 */
private fun readIntentData() {
  val link = intent.data.toString()
  val uri = Uri.parse(link)
  val actionId = uri.getQueryParameter("actionId")
  val pageId = uri.getQueryParameter("pageId")

  if (Action.JUMP_PAGE == actionId) {
      PageNavigator.navigate(this, pageId)
  }
}
```
**b.缺点：**
+ 假如别的 App 在 `intent-filter` 配置了识别相同 scheme 的逻辑，当点击
 `zac4j://zaccc` 时，不会直接跳到我们的 App，而是会弹出消歧弹框，让用户选择打开 App。

#### AppLinks
与 Deep linking 类似，AppLinks 同样需要配置 Handler Activity 的 intent-filter，不同之处是 AppLinks 配置的 URL 是 HTTP URL，此外这个链接还需要经过[安全验证][verify_applinks]。

**a.配置：**
+ AndroidManifest 页面配置 intent-filters:
```xml
<activity
	android:name="com.zac4j.applinks.AppLinksActivity"
	android:label="@string/app_name"
	android:launchMode="singleTask">
	<intent-filter android:label="@string/filter_view_zac4j">
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <!-- Accepts URIs that begin with "http://www.zac4j.com/zaccc” -->
    <data
        android:host="www.zac4j.com"
        android:pathPrefix="/zaccc"
        android:scheme="http"/>
    <data android:scheme="https"/>
    <!-- note that the leading "/" is required for pathPrefix-->
	</intent-filter>
</activity>
```
+ Handler Activity 同 Deep Linking 中的 AppLinksActivity。
+ 为 `www.zac4j.com` 这个域名配置 [Digital Asset Links][applinks_dal] 认证。

#### Deep links 和 App links 的区别
```

```

[intent_extra]:http://developer.android.com/guide/components/intents-filters.html#extras
[category_browsable]:http://developer.android.com/reference/android/content/Intent.html#CATEGORY_BROWSABLE
[applinks]:https://developer.android.google.cn/training/app-links/
[verify_applinks]:https://developer.android.google.cn/training/app-links/verify-site-associations
[applinks_dal]:https://developers.google.cn/digital-asset-links/v1/getting-started
