---
title: 浏览器缓存机制简介
date: 2018-05-31 
tags:
---


解决前端开发中遇到的资源重复加载造成的浪费、以及载入速度慢的问题，可以使用web缓存的解决方案。web缓存主要可以分为以下几种：浏览器缓存、代理服务器缓存、CDN缓存、服务器缓存、数据库缓存。

本文将主要探讨**浏览器缓存**机制。一般来说浏览器缓存可以分为 _强缓存_ 和 _协商缓存_ 。他们的最大区别在于，如果强缓存被命中，则不会请求服务器；而协商缓存一定会发送一个请求到服务器，如果协商缓存命中，服务器会回复请求，但不会返回这个资源的实体。本会会介绍浏览器是如何储存缓存、如何命中缓存和如何对比缓存资源。

## 浏览器缓存机制

浏览器中每个页面的缓存是由各个请求的请求头控制的。

先来介绍两个古典的字段：

### Pragma

Pragma 是HTTP 1.0 中控制缓存的一个字段。当这个字段的值为`no-chahe` 的时候，表示客户端在每次需要这个资源的时候都应该向服务器发送一次请求。

### Expires

Expires 在HTTP 1.0 中用来设置被缓存资源的到期时间，它是 _服务器_ 的具体时间点。如果浏览器需要某个资源且没有超过这个时间点，则不会向服务器请求这个文件，而是从缓存中获取并返回 `200 OK (from cache)` 。

当Expires 和Pragma 一起出现的时候，Pragma 的优先级会比Expires 高。

但是Expires 有几个比较致命的缺点：
- Expires 定义的过期时间点是服务器上的时间而不是客户端的时间，然而判断的时候是按照客户端的时间来进行的。如果客户端的时间和服务器的时间不一致，那么这个字段可能就无法发挥其应有的意义。
- 如果客户端上的缓存资源过期了，但服务器没有更新此资源，则客户端需要把服务器上的相同资源重新请求一遍，会浪费带宽和时间。

Expires 和Pragma 在HTTP 1.1 中已经被废弃了。但是为了保证兼容性，大部分情况下还是会带上这两个字段。

在HTTP 1.1 中，主要使用了下面的这些字段来控制浏览器缓存：

### Cache-Control

`Cache-Control` 既可以用于请求头，也可以用于响应头。它控制着两个缓存：本地缓存和共享缓存。它的值由一个或多个指令组合而成。它主要有三种指令： _可缓存性_ 、 _过期时间_ 、 _重新验证和重新加载_ 。这里只介绍用于响应头的几个值。

先说明一下本地缓存和共享缓存。本地缓存是私有的，由客户端（浏览器）保存在本地，这些缓存可以为浏览过的文档提供向前、先后缓存或查看源码等功能，避免向服务器发出多余请求。而共享缓存指的是可以被多个用户使用，保存在代理服务器（比如CDN）上的缓存。

#### 可缓存性
可缓存性指令下主要有四个值：

- `public` 指的是可以被任何对象缓存。（包括客户端、代理服务器等）
- `private` 指的是只能被单个用户缓存，不能作为共享缓存。（即代理服务器不能缓存它）
- `no-cache` **表示使用前强制校验本地缓存和服务器上的是否一致**。每次需要请求某些资源的时候，如果本地有该资源的缓存，会向服务器发送一次请求（该请求会带上与本地缓存相关的验证字段），校验是它否过期，如果没有过期（返回304），则使用本地缓存。
- `no-store` 表示不缓存任何内容。
- `no-transform` 表示不得对资源进行修改。


*_注：MDN上`no-store`和`no-transform`被归为“其他”类别_

#### 到期
到期主要有以下指令：

- `max-age=<seconds>` 指定资源被缓存的最大时间长度，单位是秒。这里的过期时间是相对于请求时间来计算的。`max-age` 指令会覆盖`Expires` 。如果设置了`max-age` ，在这段时间内，浏览器对于相同资源时都不会再向服务器发送请求，即使服务器上的资源发生了改变。
- `s-maxage=<seconds>` 同`max-age`，只用于共享缓存。会覆盖`max-age` 指令或`Expires`。也就是说，如果设置了`s-maxage` ，那么在这段时间内，即使更新了CDN的内容，浏览器也不会进行请求。

客户端在`max-age` 、`s-maxage` 或`Expires` 指定的过期时间前从缓存中直接读取资源且不请求服务器判断资源是否一致时，叫命中强缓存，其余使用缓存的情况都算作命中协商缓存。

#### 重新加载和重新验证

重新加载表示对缓存进行更精细的控制：

- `immutable` 表示文档会被更改。资源（如果未过期）在服务器上不会发生改变，因此客户端不用检查更新。
- `must-revalidate` 表示客户端必须在使用之前检查服务器上是否也存在这个资源，即使已经在本地缓存了该资源。
- `proxy-revalidate` 表示共享缓存必须要检查资源是否存在，即使已经有缓存。

看完上面的介绍，再举几个实际的例子来帮助理解。

例如我们请求一些动态网页的时候，由于页面的内容可能会随着时间发生变化（例如知乎的时间线），因此不应将他们进行缓存，所以响应头中的`Cache-Control`应该是：

```
Cache-Control: max-age=0, no-cache, no-store
```

在这个情况下我们每次刷新页面或重新进入页面都会向服务器发送一次请求。

而对于一些不会经常改动的资源文件（比如图片、脚本、样式表），为了节约网络资源，通常会允许客户端缓存一段时间，因此他们的`Cache-Control`会是：

```
Cache-Control: max-age=31536000, public
```

当你再次请求这个资源的时候，会看到状态码变成`200 OK (from memory cache)` 或 `200 OK (from disk cache)` ——这就表明了此时浏览器直接从缓存中获取了该资源。不过在实际生产环境下，我们一般会为这些静态资源文件的文件名加入hash 或版本号，来确保每次得到的静态资源文件都符合预期。

### Last-Modified

`Last-Modified` 指的是文件在服务器上的最后修改时间，是一个时间点，需要和`Cache-Control` 一起使用。这个请求头用于检查服务器端的资源和本地缓存的资源一致。

如果保存在客户端上的某个资源的缓存时间到了，会向服务器重新请求这个资源，并带上一个`If-Modified-Since` 头，询问`Last-Modified` 时间点后资源是否被修改过。这时如果服务器没有更新过这个资源，就可以向客户端返回304状态，不用重新发送资源，客户端会直接从缓存中读取这个资源。如果修改过，则和首次请求的流程相同，返回200状态码和资源。

当然还有一种请求策略是使用`If-Unmodified-Since` ，意思为从某个时间点开始没有被修改，这个请求头会用在断点续传的场景。如果文件没有被修改，服务器返回200状态码并继续传送文件；如果文件被修改，则返回412状态码（预处理错误），不传送文件。

### ETag

`ETag` 也属于保证服务器端资源和本地资源一致的策略。服务器会使用某种算法，给资源生成一个唯一标识符，在请求时作为`ETag` 的值返回给客户端。

与`Last-Modified` 类似，`ETag` 也有与之对应的一个请求头。在向服务器重新请求某个文件时，会把`ETag` 的值放在`If-None-Match` 头中。当前仅但服务器上没有任何资源的ETag值与请求头中列出的值匹配的时候，服务器端才会返回200状态码和说请求的资源，否则服务器返回304验证码。（如果这个请求可能会引起服务器状态改变，则返回421状态码。）

也有一种特殊情况，请求头中`IF-None-Match` 的值是`*` ，它只用于资源上传时，用来检测相同识别ID的资源是否已经上传过了。

在与`Last-Modified` 同时出现时，`IF-None-Match` 的优先级较高。ETag 属性之间的比较使用的是弱比较算法，即如果两个文件内容一致也可以认为是相同的——也就是说如果两个页面如果仅仅只是生成时间不一样，但内部内容一样，也可以认为二者是相同的。

这么做也解决了使用对比Last-Modified 的策略的几个痛点：
> 如果无法获取资源的最后修改时间时可能出现的问题
> 
> 资源最后修改时间改变了但是内容没变仍然要重新返回资源
> 
> 如果资源修改非常频繁，在秒以下的时间进行修改，而Last-Modified 只能精确到秒。

### Very

最后额外讲一下`Very` 响应头。这个响应头决定了对于后续请求头，应该回复一个新的资源（可能会向源服务器请求）还是回复缓存的文件。使用`Very` 头有利于内容服务的动态多样性。

一般来说，`Very` 头中的内容是一个或多个其他请求头的名字或`*` 。在客户端向服务器重新请求某个资源的时候，服务器会判断你请求的资源是否适用于`Very` 头中指定的某个请求头的内容，来选择返回304，还是返回200并重新返回资源。

例如你在第一次请求某个文件的时候使用了手机浏览器的UA，此时服务器在响应头中指定了`Vary: User-Agent` 。在你下次请求这个文件的时候，如果使用了桌面浏览器的UA，则服务器可能会因为这个资源的不适用于桌面浏览器而返回一个新资源给你。

## 最后

除了以上介绍的方法，想要把服务器上的内容保存在客户端上，其实还可以通过`Cookies`、`localStorage`、`sessionStorage` 或者`serviceWorker` 来实现，只不过他们所对应使用场景各不相同，不过都是为了提供给用户更好的体验来做的。

### 参考 & 推荐阅读

- [HTTP 缓存 - MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)
- [浅谈Web缓存 - AlloyTeam](http://www.alloyteam.com/2016/03/discussion-on-web-caching/)
- [掌握 HTTP 缓存——从请求到响应过程的一切（上） - 知乎](https://zhuanlan.zhihu.com/p/25512679)
- [【腾讯Bugly干货分享】彻底弄懂 Http 缓存机制 - 基于缓存策略三要素分解法 - 知乎](https://zhuanlan.zhihu.com/p/24467558)