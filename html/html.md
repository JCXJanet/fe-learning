## html

### href 和 src 区别

href 标识超文本引用，用在 link 和 a 等元素上，href 是引用和页面关联，是在当前元素和引用资源之间建立联系。

src 表示引用资源，表示替换当前元素，用在img，script，iframe 上，src 是页面内容不可缺少的一部分。

src 是 source 的缩写，是指向外部资源的位置，指向的内部会迁入到文档中当前标签所在的位置；在请求 src 资源时会将其指向的资源下载并应用到当前文档中，例如js脚本，img图片和frame等元素。

```
<script src="JavaScript.js"></script>
```

当浏览器解析到这一句的时候会暂停其他资源的下载和处理，直至将该资源加载，编译，执行完毕，图片和框架等元素也是如此，类似于该元素所指向的资源嵌套如当前标签内，这也是为什么要把js脚本放在底部而不是头部。

```
<link href="common.css" rel="stylesheet"/>
```

当浏览器解析到这一句的时候会识别该文档为 css 文件，会下载但是不会停止对当前文档的处理，这也是为什么建议使用 link 方式来加载 css 而不是使用 @import。

#### link 和 @import

两者都是外部引用 CSS 的方式，但是存在一定的区别：

> 区别1：link是XHTML标签，除了加载 CSS 外，还可以定义 RSS 等其他事务；@import属于 CSS 范畴，只能加载CSS。
> 区别2：link引用 CSS 时，在页面载入时同时加载；@import需要页面网页完全载入以后加载。
> 区别3：link是XHTML标签，无兼容问题；@import是在 CSS2.1 提出的，低版本的浏览器不支持。
> 区别4：link支持使用 Javascript 控制DOM去改变样式；而@import不支持。

### HTML 语义化

#### 为什么要语义化?

a. 为了在没有CSS的情况下，页面也能呈现出很好地内容结构、代码结构:为了裸奔时好看；

b. 用户体验：例如title、alt用于解释名词或解释图片信息的标签尽量填写有含义的词语、label标签的活用；

c. 有利于SEO：和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息：爬虫依赖于标签来确定上下文和各个关键字的权重；

d. 方便其他设备解析（如屏幕阅读器、盲人阅读器、移动设备）以有意义的方式来渲染网页；

e. 便于团队开发和维护，语义化更具可读性，遵循W3C标准的团队都遵循这个标准，可以减少差异化。

### H5

HTML5 的一些最有趣的新特性：

- 新的语义元素，比如 <header>, <footer>, <article>, and <section>。
- 新的表单控件，比如数字、日期、时间、日历和滑块。
- 强大的图像支持（借由 <canvas> 和 <svg>）
- 强大的多媒体支持（借由 <video> 和 <audio>）
- 强大的新 API，比如用本地存储取代 cookie。

### CSS3

新增选择器 p:nth-child（n）{color: rgba（255, 0, 0, 0.75）}

弹性盒模型 display: flex;

多列布局 column-count: 5;

媒体查询 @media （max-width: 480px） {.box: {column-count: 1;}}

个性化字体 @font-face{font-family:BorderWeb;src:url（BORDERW0.eot）；}

颜色透明度 color: rgba（255, 0, 0, 0.75）；

圆角 border-radius: 5px;

渐变 background:linear-gradient（red, green, blue）；

阴影 box-shadow:3px 3px 3px rgba（0, 64, 128, 0.3）；

倒影 box-reflect: below 2px;

文字装饰 text-stroke-color: red;

文字溢出 text-overflow:ellipsis;

背景效果 background-size: 100px 100px;

边框效果 border-image:url（bt_blue.png） 0 10;

##### 转换

旋转 transform: rotate（20deg）；

倾斜 transform: skew（150deg, -10deg）；

位移 transform:translate（20px, 20px）；

缩放 transform: scale（.5）；

平滑过渡 transition: all .3s ease-in .1s;

动画 @keyframes anim-1 {50% {border-radius: 50%;}} animation: anim-1 1s;

### video

```html
<!DOCTYPE html> 
<html> 
  <head> 
    <meta charset="utf-8"> 
    <title>菜鸟教程(runoob.com)</title> 
  </head>
  <body> 
    <div style="text-align:center"> 
      <button onclick="playPause()">播放/暂停</button> 
      <button onclick="makeBig()">放大</button>
      <button onclick="makeSmall()">缩小</button>
      <button onclick="makeNormal()">普通</button>
      <br> 
      <video id="video1" width="420">
        <source src="mov_bbb.mp4" type="video/mp4">
        <source src="mov_bbb.ogg" type="video/ogg">
        您的浏览器不支持 HTML5 video 标签。
      </video>
    </div> 
    <script> 
      var myVideo=document.getElementById("video1"); 
      function playPause() { 
        if (myVideo.paused) 
          myVideo.play(); 
        else 
          myVideo.pause(); 
      } 

      function makeBig() { 
        myVideo.width=560; 
      } 

      function makeSmall() { 
        myVideo.width=320; 
      } 

      function makeNormal() { 
        myVideo.width=420; 
      } 
    </script> 
  </body> 
</html>
```

| 属性                                                         | 值                 | 描述                                                         |
| :----------------------------------------------------------- | :----------------- | :----------------------------------------------------------- |
| [autoplay](https://www.runoob.com/tags/att-video-autoplay.html) | autoplay           | 如果出现该属性，则视频在就绪后马上播放。                     |
| [controls](https://www.runoob.com/tags/att-video-controls.html) | controls           | 如果出现该属性，则向用户显示控件，比如播放按钮。             |
| [height](https://www.runoob.com/tags/att-video-height.html)  | *pixels*           | 设置视频播放器的高度。                                       |
| [loop](https://www.runoob.com/tags/att-video-loop.html)      | loop               | 如果出现该属性，则当媒介文件完成播放后再次开始播放。         |
| [muted](https://www.runoob.com/tags/att-video-muted.html)    | muted              | 如果出现该属性，视频的音频输出为静音。                       |
| [poster](https://www.runoob.com/tags/att-video-poster.html)  | *URL*              | 规定视频正在下载时显示的图像，直到用户点击播放按钮。         |
| [preload](https://www.runoob.com/tags/att-video-preload.html) | auto metadata none | 如果出现该属性，则视频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性。 |
| [src](https://www.runoob.com/tags/att-video-src.html)        | *URL*              | 要播放的视频的 URL。                                         |
| [width](https://www.runoob.com/tags/att-video-width.html)    | *pixels*           | 设置视频播放器的宽度。                                       |

### media

```html
<head>
  <link rel="stylesheet" type="text/css" href="theme.css" />
  <link rel="stylesheet" type="text/css" href="print.css" media="print"/>
</head>
```

media 属性规定被链接文档将显示在什么设备上。

media 属性用于为不同的媒介类型规定不同的样式。

| 值         | 描述                                         |
| :--------- | :------------------------------------------- |
| screen     | 计算机屏幕（默认）。                         |
| tty        | 电传打字机以及类似的使用等宽字符网格的媒介。 |
| tv         | 电视机类型设备（低分辨率、有限的滚屏能力）。 |
| projection | 放映机。                                     |
| handheld   | 手持设备（小屏幕、有限带宽）。               |
| print      | 打印预览模式/打印页面。                      |
| braille    | 盲人点字法反馈设备。                         |
| aural      | 语音合成器。                                 |
| all        | 适用于所有设备。                             |

### img标签的title和alt有什么区别

alt用于图片无法加载时显示，title为该属性提供信息，通常当鼠标滑动到元素上的时候显示.