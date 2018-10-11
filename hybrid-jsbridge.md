---
title: JavaScript Bridge
tags:
	- hybrid
	- js-bridge
categories:
	- [hybrid]
---

Hybrid 开发中比较常见框架如 Ionic 和 PhoneGap。这些框架允许开发者使用 Web 技术开发 app 并允许 Webkit WebView 访问 native 代码。这其中又分为：
+ Calling Native in JavaScript
+ Calling JavaScript in Native

### Calling Native in JavaScript
JavaScript call native 主要有三种途径：
+ Interface-based bridges: `[WebView.addJavascriptInterface(...)][aji]`
+ Event-based bridges: `WebViewClient.onJsPrompt(...)`
+ URL interposition-based bridges: `WebViewClient.shouldOverrideUrlLoading(...)`

#### WebView.addJavascriptInterface 使用方法：
+ 在 WebView 页面的配置：
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
+ 在 HTML 页面调用的方法：
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

#### CallByJs.注意事项
+ 对 Android 4.2 ( API level 17) 以后的版本，`JsInterface` 类中的 `public` 方法需要被 `[@JavascriptInterface][jsi]` 注解才能供 `JavaScript` 代码正常调用。
+ Android 4.1 之前，`addJavascriptInterface` 方法有潜在的安全风险。

### Calling JavaScript in Native

[aji]:https://developer.android.google.cn/reference/android/webkit/WebView#addJavascriptInterface(java.lang.Object,%20java.lang.String)
[bajb]:https://labs.mwrinfosecurity.com/blog/building-android-javajavascript-bridges/
[mr1]:https://developer.android.google.cn/reference/android/os/Build.VERSION_CODES.html#JELLY_BEAN_MR1
[jsi]:https://developer.android.google.cn/reference/android/webkit/JavascriptInterface.html
[security-risk]:http://www.cis.syr.edu/~wedu/Research/paper/webview_acsac2011.pdf
[hybrid-security]:http://www.powerofcommunity.net/poc2017/huiyu.pdf