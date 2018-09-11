---
title: Hybrid Architecture
tags: 
- hybrid
categories: 
- [architecture, hybrid]
---

## Hybrid Architecture

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

[turbolinks]:https://github.com/turbolinks/turbolinks-ios
[app_link]:http://www.google.cn