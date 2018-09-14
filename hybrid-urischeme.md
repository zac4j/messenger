---
title:URI Scheme
tags: 
- hybrid
categories: 
- [hybrid]
---

#### [Introduction][wikipedia]
URI（统一资源标识符）提供了一种简单的和可扩展的方式用于识别资源。这种标识允许使用特定的协议通过网络（通常是 WWW[RFC1630][rfc1630]）与资源的表示进行交互。指定具体语法和相关协议的方案定义每个 URI。URI 的最常见形式是 URL（统一资源定位符 [RFC1736][rfc1736]），通常被非正式地称为 Web地址。在使用中还有一种更少见的形式是 Uniform.Name，URN（统一资源名称 [RFC1737][rfc1737]），它被设计为通过提供用于标识特定 [namespaces][namespaces]中的资源的机制来补充 URL。

URI 常见的形式有：

+ `ftp://ftp.is.co.za/rfc/rfc1808.txt`
  -- ftp scheme for File Transfer Protocol services

+ `gopher://spinaltap.micro.umn.edu/00/Weather/California/Los%20Angeles`
  -- gopher scheme for Gopher and Gopher+ Protocol services

+ `http://www.math.uio.no/faq/compression-faq/part1.html`
  -- http scheme for Hypertext Transfer Protocol services

+ `mailto:mduerst@ifi.unizh.ch`
  -- mailto scheme for electronic mail addresses

+ `news:comp.infosystems.www.servers.unix`
  -- news scheme for USENET news groups and articles

+ `telnet://melvyl.ucop.edu/`
  -- telnet scheme for interactive services via the TELNET Protocol

#### 通用语法
URI 的通用语法依次由 5 个组件构成：
`URI = scheme:[//authority]path[?query][#fragment]`

**常见的例子**
```
 		     userinfo      host        port
          ┌─┴────┐ ┌────┴────────┐ ┌┴┐ 
  https://john.doe@www.example.com:123/forum/questions/?tag=networking&order=newest#top
  └─┬─┘ └───────┬────────────────────┘└─┬─────────────┘└──┬───────────────────────┘ └┬┘  
  scheme     authority                 path              query                      fragment
```

+ **scheme**组件由一系列以字母开头的字符组成，字母后跟任意小写字母，数字，加号（"+"），句号（"."）或连字符（"-"）的组合。常见的 scheme 协议有：
	- http, https, ftp, mailto, file, data etc.
+ **authority**可选组件，以双斜线 `//` 开头，以下一个斜线 `/` 或 `?` 或 URI 的尾部结束，通常由基于网络服务器或特定的 scheme 注册表定义。
  - authority 通常的形式为：
  `authority = [userinfo@]host[:port]`
  - authority 中有如下组件：
    + 可选的 **userinfo** 子组件，以 `username:password` 形式表示， 后面以`@`结尾。
    + 非空的 **host** 子组件。
    + 可选的 **post** 子组件, 以 `:` 开头。
+ **path**组件，由一系列由斜线 `/` 分隔的 path segments 组成。URI 始终定义 path，虽然定义的 path 可能为空。segment 也有可能为空，从而在 path 组件中产生两个连续的斜杠 `//`。
+ **query**组件，以问号 `?` 开头。query 组件的语法并没有很好的定义，按照惯例通常以一系列 [name-value pairs][name_value_pairs] 并以 `&` 号分隔的字符串组成。
+ **fragment**组件，以 `#` 号开头。fragment 通常包含 fragment 标识符（identifier），该标识符通常指向其他辅助的资源，例如由 URI 的 fragment 部分标识文章中的节标题。当主资源是 HTML 文档时，fragmnet 通常是特定元素的 id 属性，Web 浏览器将滚动到此元素的视图。

[wikipedia]:https://en.wikipedia.org/wiki/Uniform_Resource_Identifier
[rfc1630]:https://tools.ietf.org/html/rfc1630
[rfc1736]:https://tools.ietf.org/html/rfc1736
[rfc1737]:https://tools.ietf.org/html/rfc1737
[namespaces]:https://en.wikipedia.org/wiki/Namespace
[iana]:https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority
[name_value_pairs]:https://en.wikipedia.org/wiki/Attribute%E2%80%93value_pair