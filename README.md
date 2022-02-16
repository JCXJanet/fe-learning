# fe-learning
# my-problems

记录问题

小数四舍五入至整数


```javascript
function toSmallRounding(nums) {
  return Math.round(nums.toFixed(1))
}
```

倒计时切换至后台后会被暂停的问题：

由于倒计时是基于requestAnimationFrame进行开发的，而在大多数浏览器里，当requestAnimationFrame() 运行在后台标签页或者隐藏的<iframe> 里时，requestAnimationFrame() 会被暂停调用以提升性能和电池寿命，所以会导致该问题。

可以采用监听页面可见性来刷新倒计时解决该问题

```javascript
// 应用demo
// startSimulation 和 pauseSimulation 在其他地方定义
function handleVisibilityChange() {
  if (document.hidden) {
    pauseSimulation();
  } else  {
    startSimulation();
  }
}

document.addEventListener("visibilitychange", handleVisibilityChange, false);
```

相关API

[`Document.hidden`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/hidden) 只读

如果页面处于被认为是对用户隐藏状态时返回true，否则返回false。

[`Document.visibilityState`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/visibilityState) 只读

是一个用来展示文档当前的可见性的[`DOMString`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString) 。该属性的值为以下值之一：

- `visible` : 页面内容至少是部分可见。 在实际中，这意味着页面是非最小化窗口的前景选项卡。
- `hidden` : 页面内容对用户不可见。 在实际中，这意味着文档可以是一个后台标签，或是最小化窗口的一部分，或是在操作系统锁屏激活的状态下。
- `prerender` : 页面内容正在被预渲染且对用户是不可见的(被document.hidden当做隐藏). 文档可能初始状态为prerender，但绝不会从其它值转为该值。>
- 注释：有的浏览器不支持此功能`unloaded` : 页面正在从内存中卸载。
- 注释：有的浏览器不支持此功能

[`Document.onvisibilitychange`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/onvisibilitychange)

[`EventListener`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventListener) 提供在`visibilitychange (en-US)` 事件被触发时要调用的代码。



1.边界值问题

​	使用text-overflow,如果要使这个属性生效必须使元素有宽度。

```css
*{
	text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}
```

2.布局问题

​	absolute、fixed

​	flex

可以考虑少使用div，多使用类似ul、li的元素。

设计的原则是：能够让元素在正常的文档流下布局则不适用absolute，如果必要使用absolute则必须要注意子绝父相的道理。

3.禁止双击放大

```javascript
<meta name="viewport" content="width=device-width,inital-scale=1.0,maximum-scale=1.0,user-scale=0">

window.onload=function() {
  document.addEventListener('touchstart',function (event){
    if(event.touches.length>1){
      event.preventDefault();
    }
  });
  var lastTouchEnd = 0;
  document.addEventListener('touchend',function (event){
    var now = (new Date()).getTime();
    if(now -lastTouchEnd <= 300){
      event.preventDefault();
    }
    lastTouchEnd = now;
  },false);
  document.addEventListener('gesturestart',function (event){
    event.preventDefault();
  });
}
```

4.CSS overscroll-behavior让滚动嵌套时父滚动不触发、scroll-behavior用于滚动平滑、scroll snap用于滚动定位加上文本的滚动边界。

在模板字符串中，空格、缩进、换行都会被保留

  
1.接口返回，统一结构

2.try-catch融入

3.sql本身字段
				
TCP/IP协议族按层次分别分为以下4层：应用层、传输层、网络层和数据链路层。

ARP是一种用以解析地址的协议，根据通信方的IP地址就可以反查出对应的MAC地址。

所谓的字节流服务（Byte Stream Service）是指，为了方便传输，将大块数据分割成以报文段（segment）为单位的数据包进行管理。

HTTP是一种不保存状态，即无状态（stateless）协议。

因为HTTP1.0是无状态协议，虽然HTTP/1.1虽然是无状态协议，但为了实现期望的保持状态功能，于是引入了Cookie技术。

在HTTP/1.1中，所有的连接默认都是持久连接
				
Cookie会根据从服务器端发送的响应报文内的一个叫做Set-Cookie的首部字段信息，通知客户端保存Cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入Cookie值后发送出去。



应用HTTP/1.1版本的缓存服务器遇到同时存在Expires首部字段的情况时，会优先处理max-age指令，而忽略掉Expires首部字段。而HTTP/1.0版本的缓存服务器的情况却相反，max-age指令会被忽略掉。



Connection首部字段具备如下两个作用。

●控制不再转发给代理的首部字段

●管理持久连接



HttpOnly属性

Cookie的HttpOnly属性是Cookie的扩展功能，它使JavaScript脚本无法获得Cookie。其主要目的为防止跨站脚本攻击（Cross-site scripting,XSS）对Cookie的信息窃取。

首部字段X-Frame-Options属于HTTP响应首部，用于控制网站内容在其他Web网站的Frame标签内的显示问题。其主要目的是为了防止点击劫持（clickjacking）攻击。

首部字段X-Frame-Options有以下两个可指定的字段值。

● DENY：拒绝

● SAMEORIGIN：仅同源域名下的页面（Top-level-browsing-context）匹配时许可。（比如，当指定http://hackr.jp/sample.html页面为SAMEORIGIN时，那么hackr.jp上所有页面的frame都被允许可加载该页面，而example.com等其他域名的页面就不行了）

在采用SSL后，HTTP就拥有了HTTPS的加密、证书和完整性保护这些功能。

