---
title: 雪碧图在移动端的自适应
date: 2021-02-16 01:16:03
tags: CSS
---

```
//方法一，可以使用网易转换rem的那一套，配合scss函数使用
background-size: x y; //为实际上完成后的雪碧图的宽高
@function torem($px){
	@return $px / 64px *1rem;
}

@mixin spImg($url, $left, $top. $imgWidth){
	background: url($url) no-repeat $left $top;
	background-size: $imgWidth;
}

//方法二，使用px，使用插件postcss-px-to-viewport

```

