---
title: Hybrid Architecture
tags: 
- hybrid
categories: 
- [architecture, hybrid]
---

## Hybrid Architecture

此文源自于 Basecamp 的技术博客 [Basecamp 3 for iOS: Hybrid Architecture][original_article]

#### What's Hybrid
Hybrid 架构的特点是使用 Web 技术渲染页面，跟下面这些是不同的：
+ 使用框架通过 HTML/CSS 模拟原生的控制 (PhoneGap)
+ 使用框架将别的类型的语言编译成原生的代码 (Xamarin)
+ 使用框架将一套代码库生成多平台的 app

#### Under the hood
BaseCamp 使用 [Tuobilinks][turbolinks] 来为 hybrid 架构提供支撑，Tuobilinks 的主要功能有：
+ **快速发布，高效的 hybrid app:**避免重复加载 JavaScript 和 CSS 文件，使用单一 WebView 节省系统资源。
+ **可跨平台复用移动网页:**开发新的移动页面部署到服务端，可同步分发到 iOS, Android 和移动浏览器。新功能体验无需等待发布新版本。
+ **增强网页和本地 UI 交互：**页面导航使用本地的范式，与网页的交互使用本地的控制。
+ **利用小团队生产大型 app：**免费实现基线 HTML 全覆盖，按需原生开发某一页面。

#### Router/Nagivator
BaseCamp iOS 版大部分页面导航(navigation)基于 URL 驱动，URL 可以来自于：
+ 网页链接：https://zacash.cn
+ 推送通知:appnotify://
+ Universal Link:https://zacash.cn/t, 安卓见 [App Link][app_link]
+ 原生动作：scheme://example
+ etc.

所有这些动作通过 Router 跳转，Router 的功能是根据提供的 url 指定下一步 action，这些 action 可以包括：
+ 打开浏览器加载链接（假如 url 是白名单外的域名）
+ 打开播放器加载音频/视频（假如 url 是多媒体的链接）
+ 打开指定的业务页面
+ etc.

#### Bridge
"Bridge" 是组成 hybrid 架构的一个重要组件，Bridge 是涵盖性术语，主要描述：native -> web (web - native) 之间的交流。BaseCamp 的思路是使用一套 JavsSctipt 文件（使用 TypeScript 编写）嵌入到 app 中，通过 `WKUserScript` 注入到 WebView 中。这种方式提供一套 API 可以让 native 的代码与 webView 交互，而不必通过查询 DOM 或进行复杂的 JS 操作。使用 `WKScriptMessageHandler` 可以响应 webview 通过 bridge 发出的信息。

![bridge_example][bridge_example]

图片左边部分是移动浏览器的界面，右边是 app 中的样式。app 通过 bridge 隐藏了顶部导航栏，breadcrumbs，以及其他不希望在 app 中渲染的元素。

#### Basecamp app 的示例
下面的例子中，紫色的图层表示网页元素，绿色图层表示原生的 UI。

**MainTabs**
iOS 版的 Basecamp 3 有 4 个主标签页 main Tabs (*Home*, *Hey!*, *Activity*, *Find*).这些 tabs 是 100% 原生实现的，它们是 app 中主要交互的点，我们希望它们尽可能快速，团队同样希望移动端能获取到与桌面端不同的用户体验，例如加入了统一的 *Hey!* 对于所有的通知都包括最近的 *Pings*

![main_tabs][main_tabs]

Basecamp 3 for iOS -- Main tabs

**Message**
当点击 *Hey!*中的某条通知，通常称为 Message，我们在 navigation 栈推入一个新的 `TurbolinksViewController`

![hey!][hey]
从左到右依次为:Hey!, viewing a message, actions menu, tools menu

Message 页面通过 *Bridge* 提取需要的数据展示在 navigation bar，actions menu 的数据来自网页的 DOM.

**Campfire**
Campfire 页面是由 web 和原生混合开发：

![campfire][campfire]

从左到右依次为：Hey! -> campfire -> completing a mention -> attaching a file

Chat 页的主要内容是网页，软键盘和输入框是原生，我们解决了 Web 输入的许多问题，例如在滚动时保持输入框正确的位置，控制软键盘的隐藏等。点击某个人姓名时，我们使用原生的 mention auto-completer 组件。点击回形针按钮时展示原生的 attachment picker 组件。

**总结**
上面这些只是几个例子，但展示了这种方法的灵活性。这种架构的优点是我们没有被锁定在一个方法或框架中。原生或 web 不是二元选择，而是 Specturm:

![spectrum][spectrum]

对于 app 内的任意屏幕，我们可以调整这个 specturm，我们可以将使用不太频繁的原生页面转化为 web 页，对于需要提供最佳用户体验的页面，我们可以用原生的方式实现。我们还可以尝试混合使用 React Native。无论何时 Apple 发布新的 API，我们都可以立即支持它，因为我们不依赖第三方框架去更新。

我们在Basecamp非常重视的一件事是团队的独立性。如果我们必须协调 web，iOS 和 Android 团队一起开发和发布每个功能，那么我们永远不会发布任何东西。 这种架构允许我们的Web团队构建新功能并在所有平台上同时发布。默认情况下，我们有一个 ViewController，可以在 Basecamp 3 中显示任何 URL，因此任何新的URL都可以在 app 中运行。我们可以在 web 端进行迭代和实验，在所有平台上立即发布，如果我们觉得可以改善体验，那么我们可以将用原生开发。

这也让我们移动团队专注于如何最好地服务于平台。我们的目标之一是 Basecamp 的功能在移动 app 中100％覆盖，你将永远不必因为移动 app 不支持某些内容，而去使用桌面端。凭借我们由 web 提供的坚实的基础，我们可以实现这一目标，然后将精力集中在特定于平台的改进上。其中包括丰富的内容推送通知，universal links，hand-off support，iCloud Keychain 支持，share extension，today widget 等功能。如果我们没有完全的原生支持，那么其中一些功能将是不可能的或不重要的。


[original_article]:https://m.signalvnoise.com/basecamp-3-for-ios-hybrid-architecture-afc071589c25?tdsourcetag=s_pcqq_aiomsg
[turbolinks]:https://github.com/turbolinks/turbolinks-ios
[app_link]:http://www.google.cn
[bridge]:http://o6mq6hyfb.bkt.clouddn.com/basecamp_bridge.png
[main_tabs]:http://o6mq6hyfb.bkt.clouddn.com/basecamp_main_tabs.png
[hey]:http://o6mq6hyfb.bkt.clouddn.com/basecamp_hey.png
[campfire]:http://o6mq6hyfb.bkt.clouddn.com/basecamp_campfire.png
[specturm]:http://o6mq6hyfb.bkt.clouddn.com/basecamp_specturm.png