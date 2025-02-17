---
title: 前端校招------计算机网络基础
date: 2021-02-15 22:01:24
tags: 计算机网络
---
#网络：

## **TCP、UDP的区别**

（1）**TCP是面向连接的；UDP是无连接的**，即发送数据前不需要先建立链接

（2）**TCP提供可靠的服务**，通过TCP连接传输的数据无差错、不丢失、不重复，且按序到达，适合大数据量的交换；**UDP尽最大努力交付**，但不保证可靠

（3）**TCP是面向字节流；UDP面向报文**，并且网络出现拥塞不会使发送速率降低（会出现丢包，比如IP电话或者视频会议）

（4）**TCP只能端对端；UDP支持1对1 、1对多**

（5）**TCP的首部较大，为20字节；UDP为8字节**

## 三次握手以及四次分手

在创建连接时，

1.客户端首先要SYN=1,表示要创建连接，

2.服务端接收到后，要告诉客户端：我接受到了！所以加个ACK=1，就变成了ACK=1,SYN=1

3.理论上这时就创建连接成功了，但是要防止一个意外（见疑问三），所以客户端要再发一个消息给服务端确认一下，这时只需要ACK=1就行了。

三次握手完成！

在四次分手时，

1.首先客户端请求关闭客户端到服务端方向的连接，这时客户端就要发送一个FIN=1，表示要关闭一个方向的连接（见上面四次分手的图）

2.服务端接收到后是需要确认一下的，所以返回了一个ACK=1

3.这时只关闭了一个方向，另一个方向也需要关闭，所以服务端也向客户端发了一个FIN=1 ACK=1

4.客户端接收到后发送ACK=1，表示接受成功

四次分手完成！

## **HTTP为什么需要三次握手**

如果发送两次就可以建立连接话，那么只要客户端发送一个连接请求，服务端接收到并发送了确认，就会建立一个连接。

**可能出现的问题：**如果一个连接请求在网络中跑的慢，超时了，这时客户端会从发请求，但是这个跑的慢的请求最后还是跑到了，然后服务端就接收了两个连接请求，然后全部回应就会创建两个连接，浪费资源！

如果加了第三次客户端确认，客户端在接受到一个服务端连接确认请求后，后面再接收到的连接确认请求就可以抛弃不管了。

## **HTTP为什么需要四次分手**

TCP是双向的，所以需要在两个方向分别关闭，每个方向的关闭又需要请求和确认，所以一共就4次。

## **什么是XSS，如何防止XSS？XSS和CSRF的防范**

**XSS（跨站脚本攻击）** 

XSS 攻击是指攻击者在网站上注入恶意的客户端代码，通过恶意脚本对客户端网页进行篡改，从而在用户浏览网页时，对用户浏览器进行控制或者获取用户隐私数据的一种攻击方式。会将一些隐私数据像 cookie、session 发送给攻击者，将受害者重定向到一个由攻击者控制的网站，在受害者的机器上进行一些恶意操作。

**XSS攻击防范** 

**HttpOnly 防止劫取 Cookie**：浏览器将禁止页面的Javascript 访问带有 HttpOnly 属性的Cookie。上文有说到，攻击者可以通过注入恶意脚本获取用户的 Cookie 信息。通常 Cookie 中都包含了用户的登录凭证信息，攻击者在获取到 Cookie 之后，则可以发起 Cookie 劫持攻击。所以，严格来说，**HttpOnly 并非阻止 XSS 攻击，而是能阻止 XSS 攻击后的 Cookie 劫持攻击。**

**输入、输出检查**：不要相信用户的任何输入。 对于用户的任何输入要进行检查、过滤和转义。建立可信任的字符和 HTML 标签白名单，对于不在白名单之列的字符或者标签进行过滤或编码。在 XSS 防御中，输入检查一般是检查用户输入的数据中是否包含 <，> 等特殊字符，如果存在，则对特殊字符进行过滤或编码，这种方式也称为 XSS Filter。而在一些前端框架中，都会有一份 decodingMap， 用于对用户输入所包含的特殊字符或标签进行编码或过滤，如 <，>，script，防止 XSS 攻击：

**CSRF/XSRF（跨站请求伪造）** 

CSRF，即 Cross Site Request Forgery，中译是跨站请求伪造，是一种劫持受信任用户向服务器发送非预期请求的攻击方式。通常情况下，CSRF 攻击是攻击者借助受害者的 Cookie 骗取服务器的信任，可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击服务器，从而在并未授权的情况下执行在权限保护之下的操作。

**CSRF攻击防范** 

**验证码**：验证码被认为是对抗 CSRF 攻击最简洁而有效的防御方法。从上述示例中可以看出，CSRF 攻击往往是在用户不知情的情况下构造了网络请求。而验证码会强制用户必须与应用进行交互，才能完成最终请求。因为通常情况下，验证码能够很好地遏制 CSRF 攻击。但验证码并不是万能的，因为出于用户考虑，不能给网站所有的操作都加上验证码。因此，验证码只能作为防御 CSRF 的一种辅助手段，而不能作为最主要的解决方案。

**Referer Check**：根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。通过 Referer Check，可以检查请求是否来自合法的"源"。

**添加token验证**：要抵御 CSRF，关键在于在请求中放入攻击者所不能伪造的信息，并且该信息不存在于 Cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。



## **强缓存和协商缓存（包括浏览器机制）**

缓存分为强缓存和协商缓存。强缓存不过服务器，协商缓存需要过服务器，协商缓存返回的状态码是304。两类缓存机制可以同时存在，强缓存的优先级高于协商缓存。当执行强缓存时，如若缓存命中，则直接使用缓存数据库中的数据，不再进行缓存协商。

**强缓存** 

**Expires(HTTP1.0)**：Exprires的值为服务端返回的数据到期时间。当再次请求时的请求时间小于返回的此时间，则直接使用缓存数据。但由于服务端时间和客户端时间可能有误差，这也将导致缓存命中的误差。另一方面，Expires是HTTP1.0的产物，故现在大多数使用Cache-Control替代。

缺点：使用的是绝对时间，如果服务端和客户端的时间产生偏差，那么会导致命中缓存产生偏差。

**Pragma(HTTP1.0)**：HTTP1.0时的遗留字段，当值为"no-cache"时强制验证缓存，Pragma禁用缓存，如果又给Expires定义一个还未到期的时间，那么Pragma字段的优先级会更高。服务端响应添加'Pragma': 'no-cache'，浏览器表现行为和刷新(F5)类似。

**Cache-Control(HTTP1.1)**：有很多属性，不同的属性代表的意义也不同：

- private：客户端可以缓存 

- public：客户端和代理服务器都可以缓存 

- max-age=t：缓存内容将在t秒后失效 

- no-cache：需要使用协商缓存来验证缓存数据 

- no-store：所有内容都不会缓存 

请注意no-cache指令很多人误以为是不缓存，这是不准确的，no-cache的意思是可以缓存，但每次用应该去想服务器验证缓存是否可用。no-store才是不缓存内容。当在首部字段Cache-Control 有指定 max-age 指令时，比起首部字段 Expires，会优先处理 max-age 指令。命中强缓存的表现形式：Firefox浏览器表现为一个灰色的200状态码。Chrome浏览器状态码表现为200 (from disk cache)或是200 OK (from memory cache)。

**协商缓存** 

协商缓存需要进行对比判断是否可以使用缓存。浏览器第一次请求数据时，服务器会将缓存标识与数据一起响应给客户端，客户端将它们备份至缓存中。再次请求时，客户端会将缓存中的标识发送给服务器，服务器根据此标识判断。若未失效，返回304状态码，浏览器拿到此状态码就可以直接使用缓存数据了。



- etag —— if-none-match

- last-modified —— if-modified-since

**Last-Modified**：服务器在响应请求时，会告诉浏览器资源的最后修改时间。

**if-Modified-Since**：浏览器再次请求服务器的时候，请求头会包含此字段，后面跟着在缓存中获得的最后修改时间。服务端收到此请求头发现有if-Modified-Since，则与被请求资源的最后修改时间进行对比，如果一致则返回304和响应报文头，浏览器只需要从缓存中获取信息即可。

- 如果真的被修改：那么开始传输响应一个整体，服务器返回：200 OK 

- 如果没有被修改：那么只需传输响应header，服务器返回：304 Not Modified 

**if-Unmodified-Since**: 从某个时间点算起, 是否文件没有被修改，使用的是相对时间，不需要关心客户端和服务端的时间偏差。

- 如果没有被修改：则开始`继续'传送文件，服务器返回: 200 OK 

- 如果文件被修改：则不传输，服务器返回: 412 Precondition failed (预处理错误) 

这两个的区别是一个是修改了才下载一个是没修改才下载。如果在服务器上，一个资源被修改了，但其实际内容根本没发生改变，会因为Last-Modified时间匹配不上而返回了整个实体给客户端（即使客户端缓存里有个一模一样的资源）。为了解决这个问题，HTTP1.1推出了Etag。

**Etag**：服务器响应请求时，通过此字段告诉浏览器当前资源在服务器生成的唯一标识（生成规则由服务器决定）

**If-Match**：条件请求，携带上一次请求中资源的ETag，服务器根据这个字段判断文件是否有新的修改

**If-None-Match**： 再次请求服务器时，浏览器的请求报文头部会包含此字段，后面的值为在缓存中获取的标识。服务器接收到次报文后发现If-None-Match则与被请求资源的唯一标识进行对比。

- 不同，说明资源被改动过，则响应整个资源内容，返回状态码200。 

- 相同，说明资源无心修改，则响应header，浏览器直接从缓存中获取数据信息。返回状态码304. 

但是实际应用中由于Etag的计算是使用算法来得出的，而算法会占用服务端计算的资源，所有服务端的资源都是宝贵的，所以就很少使用Etag了。

- 浏览器地址栏中写入URL，回车浏览器发现缓存中有这个文件了，不用继续请求了，直接去缓存拿（最快） 

- F5就是告诉浏览器，别偷懒，好歹去服务器看看这个文件是否有过期了。于是浏览器就胆胆襟襟的发送一个请求带上If-Modify-since 

- Ctrl+F5告诉浏览器，你先把你缓存中的这个文件给我删了，然后再去服务器请求个完整的资源文件下来。于是客户端就完成了强行更新的操作 

**缓存场景** 

对于大部分的场景都可以使用强缓存配合协商缓存解决，但是在一些特殊的地方可能需要选择特殊的缓存策略

- 对于某些不需要缓存的资源，可以使用 Cache-control: no-store ，表示该资源不需要缓存 

- 对于频繁变动的资源，可以使用 Cache-Control: no-cache 并配合 ETag 使用，表示该资源已被缓存，但是每次都会发送请求询问资源是否更新 

- 对于代码文件来说，通常使用 Cache-Control: max-age=31536000 并配合策略缓存使用，然后对文件进行指纹处理，一旦文件名变动就会立刻下载新的文件 





## **DNS解析时，有什么算法和方式减少重复操作？**

DNS解析的时候可以使用DNS缓存去减少重复操作，DNS缓存有浏览器DNS缓存、系统DNS缓存、路由DNS缓存、服务商DNS缓存。

DNS有两种查询的方式，分为递归查询和迭代查询。递归查询是访问根域名服务器，根域名服务器层层下发，找到目标域名的IP地址后返回。迭代查询是访问根域名服务器后，根域名服务器返回一个其他DNS服务器的地址，然后再向其他DNS服务器去查询。



## **Cookie 和Session、LocalStorage**

**三者的异同：** 

**生命周期：** 

cookie：可设置失效时间，没有设置的话，默认是关闭浏览器后失效

localStorage：除非被手动清除，否则将会永久保存。

sessionStorage： 仅在当前网页会话下有效，关闭页面或浏览器后就会被清除。

**存放数据大小：** 

cookie：4KB左右

localStorage和sessionStorage：可以保存5MB的信息。

**http请求：** 

cookie：每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题

localStorage和sessionStorage：仅在客户端（即浏览器）中保存，不参与和服务器的通信

**易用性：** 

cookie：需要程序员自己封装，源生的Cookie接口不友好

localStorage和sessionStorage：源生接口可以接受，亦可再次封装来对Object和Array有更好的支持

**应用场景：** 

从安全性来说，因为每次http请求都会携带cookie信息，这样无形中浪费了带宽，所以cookie应该尽可能少的使用，另外cookie还需要指定作用域，不可以跨域调用，限制比较多。但是用来识别用户登录来说，cookie还是比stprage更好用的。其他情况下，可以使用storage，就用storage。

## 缓存位置

- from Service Worker
- from memory cache
- from disk cache
- 真正的网络请求（显示资源的具体大小）

本质是作为服务器与客户端之间的代理服务器，伴随着PWA出现。Service Worker真正意义上将缓存控制权交给了前端，相比于LocalStorage、SessionStorage，后两者只是单纯的接口数据缓存，例如用户信息（一个对象）、列表信息（一个数组），而前者可以缓存静态资源，甚至拦截网络请求，根据网络状况作出不同的缓存策略。
**memory cache**
将资源缓存在了内存中。事实上，所有的网络请求都会被浏览器缓存到内存中，当然，内存容量有限，缓存不能无限存放在内存中，因此，注定是个短期缓存。
内存缓存的控制权在浏览器，前后端都不能干涉。
**disk cache**
将资源缓存在硬盘中，disk cache也叫http cahce，因为其严格遵守http响应头字段来判断哪些资源是否要被缓存，哪些资源是否已经过期。绝大多数缓存都是disk cache。**硬盘缓存的控制权在后端**
disk cahce分为强制缓存与对比缓存。storage在存储数据的大小上面秒杀了cookie，现在基本上很少使用cookie了，因为更大总是更好的，哈哈哈你们懂得。

localStorage和sessionStorage唯一的差别一个是永久保存在浏览器里面，一个是关闭网页就清除了信息。localStorage可以用来夸页面传递参数，sessionStorage用来保存一些临时的数据，防止用户刷新页面之后丢失了一些参数。

**浏览器支持情况：** 

localStorage和sessionStorage是html5才应用的新特性，可能有些浏览器并不支持，这里要注意。

## **WebSocket**

WebSocket 是除最初建立连接时需要借助于现有的HTTP协议，其他时候直接基于TCP完成通信。它可以让我们在客户端和服务器之间以数据流的形式实现各种应用数据交换（包括JSON 及自定义的二进制消息格式），而且两端都可以随时向另一端发送数据。

- 连接协商和同源策略；
- 与既有 HTTP 基础设施的互操作；
- 基于消息的通信和高效消息分帧；
- 子协议协商及可扩展能力。



## **Keep-alive**

Connection:Keep-alive

HTTP协议采用“请求-应答”模式，不开启KeepAlive模式时，每个req/res客户端和服务端都要新建一个连接，完成之后立即断开连接（HTTP协议为无连接的协议）；当开启Keep-Alive模式（又称持久连接、连接重用）时，Keep-Alive功能使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive功能避免了建立或者重新建立连接。



## **HTTP与HTTPS的区别**

1. HTTPS协议需要到CA（证书颁发机构）申请证书，一般免费证书很少，需要交费。

2. HTTP协议运行在TCP之上，所有传输的内容都是明文，HTTPS运行在SSL/TLS之上，SSL/TLS运行在TCP之上，所有传输的内容都经过加密的。

3. HTTP和HTTPS使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

## **HTTP2.0**

相对于HTTP1.0，HTTP1.1的优化：

- 缓存处理：多了Entity tag，If-Unmodified-Since, If-Match, If-None-Match等缓存信息（HTTTP1.0 If-Modified-Since,Expires） 

- 带宽优化及网络连接的使用 

- 错误通知的管理 

- Host头处理 

- 长连接： HTTP1.1中默认开启Connection： keep-alive，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。 

相对于HTTP1.1，HTTP2的优化：

- HTTP2支持二进制传送（实现方便且健壮），HTTP1.x是字符串传送 

- HTTP2支持多路复用 

- HTTP2采用HPACK压缩算法压缩头部，减小了传输的体积 

- HTTP2支持服务端推送 

## **DNS缓存**

在浏览器中输入URL的时候，DNS会根据域名找到对应的IP地址。在DNS查找目标域名对应的IP地址的时候，首先会去访问浏览器缓存，看最近访问的网址中有没有目标域名，如果没有则会访问系统DNS缓存中的是否存在，依旧不存在则会去路由缓存中查找，但是还是不存在的话就会去ISP服务商的缓存中查找是否有目标域名的IP地址。如果缓存中都不存在则会访问根域名服务器去查找目标域名的IP地址，根域名服务器会层层下发直到找到对应域名的IP地址为止。





## **常见的HTTP状态码**

1开头的http状态码
表示临时响应并需要请求者继续执行操作的状态代码。

100   （继续） 请求者应当继续提出请求。 服务器返回此代码表示已收到请求的第一部分，正在等待其余部分。  
101   （切换协议） 请求者已要求服务器切换协议，服务器已确认并准备切换。

2开头的http状态码
表示请求成功

200     成功处理了请求，一般情况下都是返回此状态码； 
201     请求成功并且服务器创建了新的资源。 
202     接受请求但没创建资源； 
203     返回另一资源的请求； 
204     服务器成功处理了请求，但没有返回任何内容；
205     服务器成功处理了请求，但没有返回任何内容；
206     处理部分请求；

3xx （重定向） 
重定向代码，也是常见的代码

300   （多种选择）  针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择。 
301   （永久移动）  请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置。 
302   （临时移动）  服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。 
303   （查看其他位置） 请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。 
304   （未修改） 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容。 
305   （使用代理） 请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理。 
307   （临时重定向）  服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。


4开头的http状态码表示请求出错

400    服务器不理解请求的语法。 
401   请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。 
403   服务器拒绝请求。 
404   服务器找不到请求的网页。 
405   禁用请求中指定的方法。 
406   无法使用请求的内容特性响应请求的网页。 
407   此状态代码与 401类似，但指定请求者应当授权使用代理。 
408   服务器等候请求时发生超时。 
409   服务器在完成请求时发生冲突。 服务器必须在响应中包含有关冲突的信息。 
410   如果请求的资源已永久删除，服务器就会返回此响应。 
411   服务器不接受不含有效内容长度标头字段的请求。 
412   服务器未满足请求者在请求中设置的其中一个前提条件。 
413   服务器无法处理请求，因为请求实体过大，超出服务器的处理能力。 
414   请求的 URI（通常为网址）过长，服务器无法处理。 
415   请求的格式不受请求页面的支持。 
416   如果页面无法提供请求的范围，则服务器会返回此状态代码。 
417   服务器未满足”期望”请求标头字段的要求。


5开头状态码并不常见，但是我们应该知道

500   （服务器内部错误）  服务器遇到错误，无法完成请求。 
501   （尚未实施） 服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码。 
502   （错误网关） 服务器作为网关或代理，从上游服务器收到无效响应。 
503   （服务不可用） 服务器目前无法使用（由于超载或停机维护）。 通常，这只是暂时状态。 
504   （网关超时）  服务器作为网关或代理，但是没有及时从上游服务器收到请求。 
505   （HTTP 版本不受支持） 服务器不支持请求中所用的 HTTP 协议版本。

## **浏览器是如何渲染页面的**

浏览器从网络或硬盘中获得HTML字节数据后会经过一个流程将字节解析为DOM树：

- 编码： **先将HTML的原始字节数据转换为文件指定编码的字符。** 

- 令牌化： 然后**浏览器会根据HTML规范来将字符串转换成各种令牌**（如<html>、<body>这样的标签以及标签中的字符串和属性等都会被转化为令牌，每个令牌具有特殊含义和一组规则）。令牌记录了标签的开始与结束，通过这个特性可以轻松判断一个标签是否为子标签（假设有<html>与<body>两个标签，当<html>标签的令牌还未遇到它的结束令牌</html>就遇见了<body>标签令牌，那么<body>就是<html>的子标签）。

- 生成对象： **接下来每个令牌都会被转换成定义其属性和规则的对象（这个对象就是节点对象）。** 

- 构建完毕： **DOM树构建完成，整个对象集合就像是一棵树形结构**。可能有人会疑惑为什么DOM是一个树形结构，这是因为标签之间含有复杂的父子关系，树形结构正好可以诠释这个关系（CSSOS同理，层叠样式也含有父子关系。例如： div p {font-size: 18px}，会先寻找所有p标签并判断它的父标签是否为div之后才会决定要不要采用这个样式进行渲染）。 

整个DOM树的构建过程其实就是： **字节 -> 字符 -> 令牌 -> 节点对象 -> 对象模型**，下面将通过一个示例HTML代码与配图更形象地解释这个过程。 

当上述HTML代码遇见<link>标签时，浏览器会发送请求获得该标签中标记的CSS文件（使用内联CSS可以省略请求的步骤提高速度，但没有必要为了这点速度而丢失了模块化与可维护性）。

在构建了DOM树和CSSOM树之后，浏览器只是拥有了两个互相独立的对象集合，DOM树描述了文档的结构与内容，CSSOM树则描述了对文档应用的样式规则，**想要渲染出页面，就需要将DOM树与CSSOM**树结合在一起，这就是渲染树。

- 浏览器会先从DOM树的根节点开始遍历每个可见节点（不可见的节点自然就没必要渲染到页面了，不可见的节点还包括被CSS设置了display: none属性的节点，值得注意的是visibility: hidden属性并不算是不可见属性，它的语义是隐藏元素，但元素仍然占据着布局空间，所以它会被渲染成一个空框）。

- 对每个可见节点，找到其适配的CSS样式规则并应用。

- 渲染树构建完成，每个节点都是可见节点并且都含有其内容和对应规则的样式。

渲染树构建完毕后，浏览器得到了每个可见节点的内容与其样式，下一步工作则**需要计算每个节点在窗口内的确切位置与大小，也就是布局阶段。** 

当Layout布局事件完成后，浏览器会立即发出Paint Setup与Paint事件，开始将渲染树绘制成像素，绘制所需的时间跟CSS样式的复杂度成正比，绘制完成后，用户就可以看到页面的最终呈现效果了。

## **GET和POST的区别**

- Get 请求能缓存，Post 不能

- Post 相对 Get 安全一点点，因为Get 请求都包含在 URL 里，且会被浏览器保存历史纪录，Post 不会，但是在抓包的情况下都是一样的。

- Post 可以通过 request body来传输比 Get 更多的数据，Get 没有这个技术

- URL有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的

- Post 支持更多的编码类型且不对数据类型限制

## **URL输入浏览器的整个过程**

​    （1）DNS缓存

​    （2）TCP的三次握手

​    （3）浏览器页面的渲染

​    （4）TCP的四次握手

## **回流和重绘**

**何时触发回流和重绘** 

何时发生回流：

- 添加或删除可见的DOM元素 

- 元素的位置发生变化 

- 元素的尺寸发生变化（包括外边距、内边框、边框大小、高度和宽度等） 

- 内容发生变化，比如文本变化或图片被另一个不同尺寸的图片所替代。 

- 页面一开始渲染的时候（这肯定避免不了） 

- 浏览器的窗口尺寸变化（因为回流是根据视口的大小来计算元素的位置和大小的） 

何时发生重绘（回流一定会触发重绘）：

当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、background-color、visibility等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

有时即使仅仅回流一个单一的元素，它的父元素以及任何跟随它的元素也会产生回流。现代浏览器会对频繁的回流或重绘操作进行优化，浏览器会维护一个队列，把所有引起回流和重绘的操作放入队列中，如果队列中的任务数量或者时间间隔达到一个阈值的，浏览器就会将队列清空，进行一次批处理，这样可以把多次回流和重绘变成一次。你访问以下属性或方法时，浏览器会立刻清空队列：

- clientWidth、clientHeight、clientTop、clientLeft 

- offsetWidth、offsetHeight、offsetTop、offsetLeft 

- scrollWidth、scrollHeight、scrollTop、scrollLeft 

- width、height 

- getComputedStyle() 

- getBoundingClientRect() 

以上属性和方法都需要返回最新的布局信息，因此浏览器不得不清空队列，触发回流重绘来返回正确的值。因此，我们在修改样式的时候，**最好避免使用上面列出的属性，他们都会刷新渲染队列。**如果要使用它们，最好将值缓存起来。

**如何避免触发回流和重绘** 

CSS：

- 避免使用table布局。 

- 尽可能在DOM树的最末端改变class。 

- 避免设置多层内联样式。 

- 将动画效果应用到position属性为absolute或fixed的元素上 

- 避免使用CSS表达式（例如：calc()） 

- CSS3硬件加速（GPU加速） 

JavaScript：

- 避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性 

- 避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中 

- 也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘 

- 避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来 

- 对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流 




## **HTTPS的握手**


1、客户端发送“ClientHello”消息，消息包含 SSL 的版本、客户端支持的加密套件和数据压缩算法及随机数1。

2、服务器响应“ServerHello”消息，消息包含选定的加密套件、选定的数据压缩方法、会话标识，数字证书及另一个随机数2。

3、客户端（ Web 浏览器）验证服务器的SSL数字证书的有效性，不通过则提示警告。

4、客户端发送“客户端密钥交换”消息。此消息包含 pre-master secret（用于生成对称加密密钥生的随机数3）。

5、客户端使用一系列加密运算将 [随机数1 随机数2 随机数3] 转化为 master secret，其中将派生出所有用于加密和消息认证的密钥。

6、服务器使用一系列加密运算将 [随机数1 随机数2 随机数3] 转化为 master secret，其中将派生出所有用于加密和消息认证的密钥。

7、客户端发出“更改密码规范”通知服务器端之后使用协商好的对称加密算法及密钥通信。

8、服务器发出“更改密码规范”通知客户端之后使用协商好的对称加密算法及密钥通信。

9、SSL握手结束，使用对称加密算法进行加密通信。

## 介绍下如何实现 token 加密

1. 需要一个secret（随机数）
2. 后端利用secret和加密算法(如：HMAC-SHA256)对payload(如账号密码)生成一个字符串(token)，返回前端
3. 前端每次request在header中带上token
4. 后端用同样的算法解密



## 介绍下 HTTPS 中间人攻击

中间人攻击过程如下：

1. 服务器向客户端发送公钥。
2. 攻击者截获公钥，保留在自己手上。
3. 然后攻击者自己生成一个【伪造的】公钥，发给客户端。
4. 客户端收到伪造的公钥后，生成加密hash值发给服务器。
5. 攻击者获得加密hash值，用自己的私钥解密获得真秘钥。
6. 同时生成假的加密hash值，发给服务器。
7. 服务器用私钥解密获得假秘钥。
8. 服务器用加秘钥加密传输信息

防范方法：

1. 服务端在发送浏览器的公钥中加入CA证书，浏览器可以验证CA证书的有效性

## 将url转换为对象

```
 function query(string) {
        let arr=string.split("?")  //截取字符，变为伪数组
        let arr1=arr[1].split("&"); //截取字符，变为伪数组
        let obj={}
        for (let i=0;i<arr1.length;i++){ //便利数组
            let arr2=arr1[i].split("=") //截取arr1下面的每个=
            if(arr2[0].indexOf("[")!= -1){
            		let arr3 =arr2[0].split("[")
            		if(obj.hasOwnProperty(arr3[0])){
            				obj[arr3[0]].push(arr2[1]);
            		}else{
            				obj[arr3[0]] = [];
            				obj[arr3[0]].push(arr2[1]);
            		}
            }else{
                 obj[arr2[0]]=arr2[1] //让obj[arr2数组下的每个下标为0的]=arr2的数组下的每个下标为1的
            }
    }
    return obj
}
```

## http请求中cookie可以怎么设置？

![image-20190911094352928](/Users/caiyifan/Library/Application Support/typora-user-images/image-20190911094352928.png)

## CDN原理

给源站域名添加CNMAE,别名为加速节点的域名。当用户向源站发起请求时，dns服务器解析源站域名时会发现有CNMAE记录，这时dns服务器会向CNAME域名发起请求，请求会被调度至加速节点的域名。

## 用sort给json对象数组排序

```
var jsonStudents = [
    {name:"Dawson", totalScore:"197", Chinese:"100",math:"97"},
    {name:"HanMeiMei", totalScore:"196",Chinese:"99", math:"97"},
    {name:"LiLei", totalScore:"185", Chinese:"88", math:"97"},
    {name:"XiaoMing", totalScore:"196", Chinese:"96",math:"100"},
    {name:"Jim", totalScore:"196", Chinese:"98",math:"98"},
    {name:"Joy", totalScore:"198", Chinese:"99",math:"99"}];
jsonStudents.sort(function(a,b){
    var value1 = a.totalScore,
        value2 = b.totalScore;
    if(value1 === value2){
        return b.Chinese - a.Chinese;
    }
    return value2 - value1;
});
```

## HTTP3

**1.HTTP/3简介**

虽然 HTTP/2 解决了很多之前旧版本的问题，但是它还是存在一个巨大的问题，主要是底层支撑的 TCP 协议造成的。

上文提到 HTTP/2 使用了多路复用，一般来说同一域名下只需要使用一个 TCP 连接。但当这个连接中出现了丢包的情况，那就会导致 HTTP/2 的表现情况反倒不如 HTTP/1 了。

因为在出现丢包的情况下，整个 TCP 都要开始等待重传，也就导致了后面的所有数据都被阻塞了。但是对于 HTTP/1.1 来说，可以开启多个 TCP 连接，出现这种情况反到只会影响其中一个连接，剩余的 TCP 连接还可以正常传输数据。

**Google 就更起炉灶搞了一个基于 UDP 协议的 QUIC 协议，并且使用在了 HTTP/3 上**，HTTP/3 之前名为 HTTP-over-QUIC，从这个名字中我们也可以发现，HTTP/3 最大的改造就是使用了 QUIC。

**2.QUIC新功能**

- 0-RTT

通过使用类似 TCP 快速打开的技术，缓存当前会话的上下文，在下次恢复会话的时候，只需要将之前的缓存传递给服务端验证通过就可以进行传输了。**0RTT 建连可以说是 QUIC 相比 HTTP2 最大的性能优势**。那什么是 0RTT 建连呢？

这里面有两层含义:

1.传输层 0RTT 就能建立连接。

2.加密层 0RTT 就能建立加密连接。

![img](http://5b0988e595225.cdn.sohucs.com/images/20190305/89ac5d6c6b4a408b8f86e078441ee430.jpeg)

上图左边是 HTTPS 的一次完全握手的建连过程，需要 3 个 RTT。就算是会话复用也需要至少 2 个 RTT。

而 QUIC 呢？由于建立在 UDP 的基础上，同时又实现了 0RTT 的安全握手，所以在大部分情况下，只需要 0 个 RTT 就能实现数据发送，在实现前向加密的基础上，并且 0RTT 的成功率相比 TLS 的会话记录单要高很多。

- 多路复用

虽然 HTTP/2 支持了多路复用，但是 TCP 协议终究是没有这个功能的。QUIC 原生就实现了这个功能，并且传输的单个数据流可以保证有序交付且不会影响其他的数据流，这样的技术就解决了之前 TCP 存在的问题。

同HTTP2.0一样，同一条 QUIC连接上可以创建多个stream，来发送多个HTTP请求，但是，QUIC是基于UDP的，一个连接上的多个stream之间没有依赖。比如下图中stream2丢了一个UDP包，不会影响后面跟着 Stream3 和 Stream4，不存在 TCP 队头阻塞。虽然stream2的那个包需要重新传，但是stream3、stream4的包无需等待，就可以发给用户。

![img](http://5b0988e595225.cdn.sohucs.com/images/20190305/d61228860d9c48d182a0274cf09f051f.jpeg)

另外QUIC 在移动端的表现也会比 TCP 好。因为 TCP 是基于 IP 和端口去识别连接的，这种方式在多变的移动端网络环境下是很脆弱的。但是 QUIC 是通过 ID 的方式去识别一个连接，不管你网络环境如何变化，只要 ID 不变，就能迅速重连上。

- 加密认证的报文

TCP 协议头部没有经过任何加密和认证，所以在传输过程中很容易被中间网络设备篡改，注入和窃听。比如修改序列号、滑动窗口。这些行为有可能是出于性能优化，也有可能是主动攻击。

但是 QUIC 的 packet 可以说是武装到了牙齿。除了个别报文比如 PUBLIC_RESET 和 CHLO，所有报文头部都是经过认证的，报文 Body 都是经过加密的。

这样只要对 QUIC 报文任何修改，接收端都能够及时发现，有效地降低了安全风险。

![img](http://5b0988e595225.cdn.sohucs.com/images/20190305/986f23e92daa4a95bcf9622b5892bee2.jpeg)

如上图所示，红色部分是 Stream Frame 的报文头部，有认证。绿色部分是报文内容，全部经过加密。

- 向前纠错机制

QUIC协议有一个非常独特的特性，称为向前纠错 (Forward Error Correction，FEC)，每个数据包除了它本身的内容之外，还包括了部分其他数据包的数据，因此少量的丢包可以通过其他包的冗余数据直接组装而无需重传。向前纠错牺牲了每个数据包可以发送数据的上限，但是减少了因为丢包导致的数据重传，因为数据重传将会消耗更多的时间（包括确认数据包丢失、请求重传、等待新数据包等步骤的时间消耗）。

假如说这次我要发送三个包，那么协议会算出这三个包的异或值并单独发出一个校验包，也就是总共发出了四个包。当出现其中的非校验包丢包的情况时，可以通过另外三个包计算出丢失的数据包的内容。**当然这种技术只能使用在丢失一个包的情况下，如果出现丢失多个包就不能使用纠错机制了，只能使用重传的方式了**。

## websocket和长连接区别

- 长连接、

连接->传输数据->保持连接 -> 传输数据-> 。。。 ->关闭连接。

长连接指建立SOCKET连接后不管是否使用都保持连接，但安全性较差。

WebSocket优势： 浏览器和服务器只需要要做一个握手的动作，在建立连接之后，双方可以在任意时刻，相互推送信息。同时，服务器与客户端之间交换的头信息很小。

HTTP1.1通过使用Connection:keep-alive进行长连接，HTTP 1.1默认进行持久连接。在一次 TCP 连接中可以完成多个 HTTP 请求，但是对每个请求仍然要单独发 header，Keep-Alive不会永久保持连接，它有一个保持时间，可以在不同的服务器软件（如Apache）中设定这个时间。这种长连接是一种“伪链接”

websocket的长连接，是一个真的全双工。长连接第一次tcp链路建立之后，后续数据可以双方都进行发送，不需要发送请求头。

## 谈一谈对CDN的理解

(1)节省骨干网带宽，减少带宽需求量；

(2)提供服务器端加速，解决由于用户访问量大造成的服务器过载问题； 

(3)服务商能使用Web Cache技术在本地缓存用户访问过的Web页面和对象，实现相同对象的访问无须占用主干的出口带宽，并提高用户访问因特网页面的相应时间的需求； 

(4)能克服网站分布不均的问题，并且能降低网站自身建设和维护成本； 

(5)降低“通信风暴”的影响，提高网络访问的稳定性。 

## osi模型 

[物理层](https://baike.baidu.com/item/物理层): 将数据转换为可通过物理介质传送的[电子信号](https://baike.baidu.com/item/电子信号) 相当于邮局中的搬运工人。

[数据链路层](https://baike.baidu.com/item/数据链路层): 决定访问网络介质的方式。

在此层将数据分帧，并处理流控制。本层指定[拓扑结构](https://baike.baidu.com/item/拓扑结构)并提供硬件寻址，相当于邮局中的装拆箱工人。

[网络层](https://baike.baidu.com/item/网络层/4329439): 使用权数据路由经过大型网络 相当于邮局中的排序工人。

[传输层](https://baike.baidu.com/item/传输层): 提供终端到终端的可靠连接 相当于公司中跑邮局的送信职员。

[会话层](https://baike.baidu.com/item/会话层): 允许用户使用简单易记的名称建立连接 相当于公司中收寄信、写信封与拆信封的秘书。

[表示层](https://baike.baidu.com/item/表示层): 协商数据交换格式 相当公司中简报老板、替老板写信的助理。

[应用层](https://baike.baidu.com/item/应用层/4329788): 用户的应用程序和网络之间的接口。

## 路由器工作在哪一层 

网络层

## http协议，ssl工作在哪一层 

传输层

## 绝对链接与相对链接

要尽可能的使用相对链接

1.检查代码，相对URL会更加有效，因为相对URL一般会比绝对URL短的多。

2.使用绝对URL的时候，浏览器会先通过DNS查找服务器的真实位置，然后再转到该服务器并查找所请求的文件。

相对URL只会在同一服务器上查找被请求的文件。

### **长轮询和短轮询是什么？**

【2.1】http 长轮询
1）介绍：http 长轮询是server 收到请求后如果有数据，立刻响应请求；如果没有数据 就会 停留 一段时间，这段时间内，如果 server 请求的数据到达（如查询数据库或数据的逻辑处理完成），就会立刻响应；如果这段时间过后，还没有数据到达，则以空数据的形式响应http请求；若浏览器收到的数据为空，会再次发送同样的http请求到server；
2）http 长轮询 的缺点：server 没有数据到达时，http连接会停留一段时间，这会造成服务器资源浪费；
3）看个荔枝：假设有 1000个人停留在某个客户端页面，等待server端的数据更新，那就很有可能服务器这边挂着1000个线程，在不停检测数据是否发生变化，这依然是有问题的；

【2.2】http 短轮询
1）介绍：http 短轮询是 server 收到请求 不管是否有数据到达都直接响应http 请求；如果浏览器收到的数据为空，则隔一段时间，浏览器又会发送相同的http请求到server 以获取数据响应；
2） http 短轮询的缺点：消息交互的实时性较低（server端到浏览器端的数据反馈效率低）；

【2.3】http 长轮询 和 短轮询的异同
1）相同点：当server 的数据不可达时，基于http长轮询和短轮询 的http请求，都会 停留一段时间；
2）不同点：http长轮询是在服务器端的停留，而http 短轮询是在 浏览器端的停留；
3）性能总结：从这里可以看出，不管是长轮询还是短轮询，都不太适用于客户端数量太多的情况，因为每个服务器所能承载的TCP连接数是有上限的，这种轮询很容易把连接数顶满；

### **HTTP2有什么特点**？

HTTP/2采用二进制格式而非文本格式。
在应用层(HTTP/2)和传输层(TCP or UDP)之间增加一个二进制分帧层。在该二进制分帧层中，HTTP2.0将传输的消息划分为更小的消息和帧，并采用二进制编码。其中HTTP1.1的首部信息被封装到HEADER frame，而请求实体被封装到DATA frame中。
HTTP/2是完全多路复用的，而非有序并阻塞的——只需一个连接即可实现并行
使用报头压缩，HTTP/2降低了开销（采用了HPACK压缩算法）

HTTP/2让服务器可以将响应主动“推送”到客户端缓存中

### DNS的解析过程是什么？

首先检查浏览器缓存中以及操作系统缓存中有没有对应的已解析过的结果（hosts文件），若没有则请求本地域名服务器（LDNS）来解析这个域名，若未成功解析，则跳转到根域名服务器，根域名服务器给予一个主域名服务器地址，然后本地域名服务器再去请求主域名服务器地址，接着主域名服务器会返回网站注册域名的服务器Name server的地址，本地服务器又去访问Name server，最终找到ip地址并返回给本地域名服务器，然后缓存该ip地址，解析结束。

### 说一下浏览器从发起请求到呈现页面的整个过程？

DNS解析ip地址 > TCP三次握手连接 > 发送HTTP请求 > 服务器处理，返回HTTP响应 > 浏览器解析响应 > 构建DOM树、CSSrule树，合并生成render树，计算布局并绘制



### **分别列举几个HTTP请求头字段和响应头字段？**

###### 请求首部字段：

**Accept：**告知服务器，用户代理支持的媒体类型及相对优先级

**Accept-Charset：**告知服务器，用户代理支持的字符集及相对优先级，格式与Accept类似，常应用于服务器驱动协商

**Accept-Encoding：**告知服务器，用户代理支持的内容编码及相对优先级，格式与Accept类似，可指定多种编码

**Accept-Language：**告知服务器，用户代理支持的自然语言及相对优先级，格式与Accept类似

**Authorization**：告知服务器，用户代理的认证信息（证书值），通常在**401 Unauthorized** 响应后将其加入请求头部

**Expect**：告知服务器，期望出现的某种特定行为 
例如在POST大数据时，征询服务器是否能处理，若能，则返回100-continue状态码，否则返回417 Expectation Failed状态码

**From**：告知服务器，使用用户代理的用户电子邮箱地址

**Host**：告知服务器，请求资源所处的互联网主机及端口号 
在Http/1.1中，该字段必须被包含在首部字段中，可设置为空值。常用于区分运行在同一IP之上的多台虚拟主机

**Proxy-Authorization**：与Authorization字段类似，但用于客户端与代理服务器之间，告知代理认证信息

**Range**：用于范围请求，告知服务器请求资源的范围，若处理成功，则返回206 Partial Content响应，否则返回全部资源

**Referer**：告知服务器请求发出来源的URI，可用于查看请求是从哪个Web页面发出的

**User-Agent**：告知服务器，创建请求的浏览器和用户代理名称等信息 
如果请求经过代理，则可能会附加代理服务器的名称

###### 响应请求字段

**Accept-Ranges**：告知客户端，服务器是否能处理范围请求，若能，该字段值为bytes，否则为none

**Age**：告知客户端，源服务器在多久之前创建了该响应。若创建响应的服务器是缓存服务器，则age值代表该缓存从源服务器生成开始经历的时间（秒），当代理创建响应时，该字段必须被加入

**Location**：重定向的URI地址 
该字段会配合3XX Redirection响应，提供重定向的URI地址，几乎所有浏览器在接收到该字段时，都会尝试去访问

**Server**：告知客户端当前服务器上安装的HTTP服务器应用程序信息

**Proxy-Authenticate**：将代理服务器所要求的认证信息发送给客户端

**ETag**：资源标识符 
每个资源都对应着一个唯一的标识符，当资源更新时，标识符也随之更新

- 强ETag值：无论资源实体发生多么细微的变化，该值都会改变

- 弱ETag值：只有资源发生了根本的变化，产生差异时，才会改变值，这时，会在该字段值的前方附加W/

  

### **HTTP强缓存和协商缓存是什么？**

缓存的优点：

- 减少了不必要的数据传输，节省带宽
- 减少服务器的负担，提升网站性能
- 加快了客户端加载网页的速度
- 用户体验友好



#### 协商缓存

协商缓存需要进行对比判断是否可以使用缓存。浏览器第一次请求数据时，服务器会将缓存标识与数据一起响应给客户端，客户端将它们备份至缓存中。再次请求时，客户端会将缓存中的标识发送给服务器，服务器根据此标识判断。若未失效，返回304状态码，浏览器拿到此状态码就可以直接使用缓存数据了。