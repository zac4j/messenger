---
title:Launch App from Web
tags: 
- hybrid
categories: 
- [hybrid]
---

+ Android intents with chrome
+ Allowing other apps to start your activity
+ Create deep links to app content

#### Android Intent with Chrome
在 Chrome for Android ver.18 或更早之前，Android 系统允许网页通过 Android intent 启动 app。一种场景是网页内嵌入 iframe 并且 src 设定自定义的 URI-scheme:
``` xml
<iframe scr="zacash://launchapp"></iframe>
```

在 Chrome for Android ver.25 以后，不再允许通过在 iframe 设置 src 属性的方式启动 app 功能。取而代之，你需要定义 `intent:` 句式来启动 app。

**最佳实践**
构造 `intent` 句式的最佳实践是将其 anchor 或嵌入到网页中启动 app。我们可以通过 [Intent Extras][intent_extra] 方便地传递 extra 信息。
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
		s.<extra>=[string]
	end;
```
举个例子，我的 *hostname* 是 `https://zacash.cn`
*uri-path* 是 `launchapp`
``` html
<div class="button intent">
	<a href="intent://https://zacash.cn/launchapp/#Intent;package=com.zac4j.app;scheme=zacash;S.browser_fallback_url=https://zacash.cn/dl/;end">打开失败？点我试试</a>
</div>
```

当 intent 不能被识别或者外部 app 不能被启动，用户将会被重定向到定义的 fallback URL `S.browser_fallback_url=[encoded_full_url]`。

一些通过 Chrome 无法启动外部 app 可能的原因有：
+ Intent 无法被识别，没有 app 适配当前 intent
+ JavaScript timer 试图不经用户点击 button 直接打开 app

[intent_extra]:http://developer.android.com/guide/components/intents-filters.html#extras