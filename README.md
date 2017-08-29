# 与浏览器缓存相关的Http头

缓存分很多种：服务器缓存，第三方缓存，浏览器缓存等。其中浏览器缓存是代价最小的，因为浏览器缓存依赖的是客户 端，而几乎不耗费服务器端的资源。

## 1.Expires
  
优点：`Expires`属性告诉浏览器相关副本可以保存在缓存器中的时间。在此时间内，浏览器在访问相关内容的时候，几乎不需要任何的等待时间（不发任何请求）。

缺点：

1.1 是牵扯到了日期，这样Web服务器的时间和缓存服务器的时间必须是同步的，如果有些不同步， 要么是应该缓存的内容提前过期了，要么是过期结果没及时更新。

1.2 如果在固定时间前服务器更新了内容浏览器并不知道（此时，刷新会请求，地址栏敲回车【模拟新开webview】不会请求 ）。

1.3 过期时间头信息属性值只能是HTTP格式的日期时间，其他的都会被解析成当前时间“之前”，副本会过期，记住：HTTP的日期时间必须是格林威治时 间（GMT），而不是本地时间。举例：Expires: Fri, 30 Oct 1998 14:19:41

## 2.Cache-Control

2.1 max-age=[秒] — 缓存被保留的最长时间。
2.2 s-maxage=[秒] — 类似于max-age属性，除了他应用于共享（如：代理服务器）缓存
2.3 public — 表示响应可以被任何缓存区缓存。
2.4 private - 表示对于单个用户的响应，不能被共享缓存处理。私有的缓存区可以存储
2.5 no-cache — 强制每次请求直接发送给源服务器，而不经过本地缓存版本的校验。这对于需要确认认证应用很有用（可以和public结合使用），或者严格要求使用最新数据的应用（不惜牺牲使用缓存的所有好处）；
2.6 no-store — 强制缓存在任何情况下都不要保留任何副本
2.7 must-revalidate — 缓存必须在使用之前验证旧资源的状态，并且不可使用过期资源。
2.8 proxy-revalidate — 和 must-revalidate类似，除了他只对缓存代理服务器起作用
例:Cache-Control: max-age=3600, must-revalidate

例：
（1） 打开新窗口
如果指定cache-control的值为private、no-cache、must-revalidate，那么打开新窗口访问时都会重新访问服务器。而如果指定了max-age值，那么在此值内的时间里就不会重新访问服务器。
（2） 在地址栏回车
如果值为private、must-revalidate，则只有第一次访问时会访问服务器，以后就不再访问。如果值为no-cache，那么每次都会访问。如果值为max-age，则在过期之前不会重复访问。
（3） 按后退按扭
如果值为private、must-revalidate、max-age，则不会重访问，而如果为no-cache，则每次都重复访问
（4） 按刷新按扭
无论为何值，都会重复访问

## 3.Last-Modified

  通常服务器会知道你所请求的数据的最后修改时间，并且 HTTP 为服务器提供了一种将最近修改数据连同你请求的数据一同发送的方法。

  如果第二次 (或第三次，或第四次) 请求相同的数据，你可以告诉服务器你上一次获得的最后修改日期：在你的请求中发送一个 If-Modified-Since 头信息，它包含了上一次从服务器连同数据所获得的日期。如果数据从那时起没有改变，服务器将返回一个特殊的 HTTP 状态代码 304，这意味着 “从上一次请求后这个数据没有改变”。当服务器发送状态编码 304 时，不再重新发送数据。您仅仅获得了这个状态代码。所以当数据没有更新时，你不需要一次又一次地下载相同的数据；服务器假定你有本地的缓存数据。

## 4.ETag

ETag：是实体标签(Entity Tag)的缩写。ETag一般不以明文形式相应给客户端。在资源的各个生命周期中，它都具有不

同的值，用于标识出资源的状态。当资源发生变更时，如果其头信息中一个或者多个发生变化，或者消息实体发生变化

，那么ETag也随之发生变化。

ETag值的变更说明资源状态已经被修改。往往可以通过时间戳就可以便宜的得到ETag头信息。在服务端中如果发回给

消费者的相应从一开始起就由ETag控制，那么可以确保更细粒度的ETag升级完全由服务来进行控制。服务计算ETag值，

并在相应客户端请求时将它返回给客户端。

## 5.其他

Date：Date头域表示消息发送的时间，时间的描述格式由rfc822定义。例如，Date: Mon, 04 Jul 2011 05:53:36 GMT。
Age：当代理服务器用自己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了。

盗狼叔的图

![image](https://user-images.githubusercontent.com/9944527/29805178-94daffce-8cba-11e7-8c8f-13cf9c314462.png)
