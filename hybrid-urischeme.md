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
其中 authority 还可以分解为 3 个子组件：
`authority=[userinfo@]host[:port]`

**常见的例子**
```
 		userinfo       host        port
          ┌─┴────┐ ┌────┴────────┐ ┌┴┐ 
  https://john.doe@www.example.com:123/forum/questions/?tag=networking&order=newest#top
  └─┬─┘ └───────┬────────────────────┘└─┬─────────────┘└──┬───────────────────────┘ └┬┘  
  scheme     authority                 path              query                      fragment
```

+ scheme 组件由一系列以字母开头的字符组成，字母后跟任意 lower case letters，digits，数字，加号（"+"），句号（"."）或连字符（"-"）的组合。常见的 scheme 协议有：
	- http, https, ftp, mailto, file, data etc.
+ authority 可选组件，通常由基于网络的服务器或特定 scheme 的注册表定义。

[wikipedia]:https://en.wikipedia.org/wiki/Uniform_Resource_Identifier
[rfc1630]:https://tools.ietf.org/html/rfc1630
[rfc1736]:https://tools.ietf.org/html/rfc1736
[rfc1737]:https://tools.ietf.org/html/rfc1737
[namespaces]:https://en.wikipedia.org/wiki/Namespace
[iana]:https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority