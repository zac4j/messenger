---
title: JavaScript Bridge
tags:
	- hybrid
	- js-bridge
categories:
	- [hybrid]
---

Hybrid 开发中比较常见框架如 Ionic 和 PhoneGap。这些框架允许开发者使用 Web 技术开发 app 并允许 Webkit WebView 访问 native 代码。这其中的交互可分为：
+ Calling Native in JavaScript
+ Calling JavaScript in Native

### Calling Native in JavaScript
JavaScript call native 主要有三种途径：
+ Interface-based bridges: `WebView.addJavascriptInterface(...)`
+ Event-based bridges: `WebChromeClient.onJsPrompt(...)`、`WebChromeClient.onJsConfirm(...)`
+ URL interposition-based bridges: `WebViewClient.shouldOverrideUrlLoading(...)`

#### Interface-based bridges
`WebView.addJavascriptInterface` 的使用方法：
+ WebView：
```java
public class WebViewUI extends Activity {
  WebView mWebView;

  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mWebView = new WebView(this);
    mWebView.getSettings().setJavaScriptEnabled(true);
    mWebView.addJavascriptInterface(new JsInterface(), "callByJs");
    mWebView.loadUrl("file:///android_asset/www/index.html");
    setContentView(mWebView);
  }

  final class JsInterface {
    JsInterface () { }
    
    @JavascriptInterface
    public String foo() {
    	return "foo";
    }

    @JavascriptInterface
    public String bar(final String text) {
    	return "bar: " + text;
    }
  }
}
```
+ HTML：
```html
<script>
	// JavaScript 调用 native 中的 foo() 方法
	var foo = window.callByJs.foo();
	console.log("callByJs.foo():" + foo);
	// JavaScript 调用 native 中的 bar(...) 方法
	var bar = window.callByJs.bar(foo);
	console.log("callByJs.bar():" + bar);
</script>
```
+ 注意事项
  - 对 Android 4.2 ( API level 17) 以后的版本，`JavascriptInterface` 类中的 `public` 方法需要被 `[@JavascriptInterface][jsi]` 注解才能供 `JavaScript` 代码正常调用。
  - Android 4.1 之前，`addJavascriptInterface` 方法存在的安全风险：(With these older versions, JavaScript could use reflection to access an injected object's public fields. Use of this method in a WebView containing untrusted content could allow an attacker to manipulate the host application in unintended ways, executing Java code with the permissions of the host application. Use extreme care when using this method in a WebView which could contain untrusted content.)。
  - JavaScript 和 Java 对象的交互发生在 WebView (UI Thread)之外的线程，需要注意线程安全问题。
  - 对于 Android L 及更高版本的 app，可以从 JavaScript 中枚举注入的 Java 对象的方法。

#### Event-based bridges
+ HTML：

```html
var result = prompt('[]','zacash://pay?type=alipay')
```
+ WebView：
```java
@Override
public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {
  if (StringUtils.isValidText(message)) {
    Uri uri = Uri.parse(message);
    if ("zacash".equals(uri.getScheme())) {
      int method = uri.getAuthority();
      switch(method) {
        case "pay":
          // pay something...
          break;
        case "buy":
          // buy something...
          break;
      }
      return true;
    }
  }
  return super.onJsPrompt(view, url, message, defaultValue, result);
}
```

#### URL  interposition-based bridges
+ HTML:
```html
window.location.href = "zacash://pay?type=alipay";

<iframe src = "zacash://pay?type=alipay">
```

+ WebView:
```java
@Override
public boolean shouldOverrideUrlLoading(WebView view, String url) {
  if (StringUtils.isValidText(url)) {
    Uri uri = Uri.parse(url);
    if ("zacash".equals(uri.getScheme())) {
      int method = uri.getAuthority();
      switch(method) {
        case "pay":
          // pay something...
          break;
        case "buy":
          // buy something...
          break;
      }
      return true;
    }
  }
  return super.shouldOverrideUrlLoading(view, url);
}
```

### Calling JavaScript in Native
WebView 中执行 JavaScript 的方式比较简单：
+ Android 4.4 前的方法：
```java
String js = "function getDeviceId(){}";

mWebView.loadUrl(js);
```
+ Android 4.4 后新增的方法：
```java
String js = "function getDeviceId(){}";

mWebView.evaluateJavascript("javascript:" + js, new ValueCallback() {
        @Override
        public void onReceiveValue(String value) {
          // do something with callback
        }
});
```

[aji]:https://developer.android.google.cn/reference/android/webkit/WebView#addJavascriptInterface(java.lang.Object,%20java.lang.String)
[bajb]:https://labs.mwrinfosecurity.com/blog/building-android-javajavascript-bridges/
[mr1]:https://developer.android.google.cn/reference/android/os/Build.VERSION_CODES.html#JELLY_BEAN_MR1
[jsi]:https://developer.android.google.cn/reference/android/webkit/JavascriptInterface.html
[security-risk]:http://www.cis.syr.edu/~wedu/Research/paper/webview_acsac2011.pdf
[hybrid-security]:http://www.powerofcommunity.net/poc2017/huiyu.pdf