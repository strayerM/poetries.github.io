---
title: 移动端布局适配方案
date: 2017-11-05 15:35:08
tags: 
   - 移动端
   - HTML5
categories: Front-End
---


一、为什么要做适配
---

- 为了适应各种移动端设备，完美呈现应有的布局效果  
- 各个移动端设备，分辨率大小不一致，网页想铺满整个屏幕，并在各种分辨下等比缩放

二、适配方案
---

- 固定高度，宽度百分比适配-布局非常均匀，适合百分比布局	
- 固定宽度，改变缩放比例适配-什么情况都可以
- `Rem`适配
- 像素比适配

三、单位
---

- `em`根据元素自身的字体大小计算,元素自身 `16px` `1em=16px`
-  `Rem`  `R -> root` 根节点( `html` ) 根据`html`的字体大小计算其他元素尺寸

四、百分比适配(常用)
---

> 固定高度，宽度百分比适配

- 根据设置的大小去设置高度，单位可以用`px` 百分比 `auto` 
- 常用`Flex`布局
- 百分比宽度

> 以`640`设计稿为例，在外层容器上设置最大以及最小的宽

```css
#wrapper {
    max-width: 640px; /*设置设计稿的宽度*/
    min-width: 300px;
    margin: 0 auto;
}
```

> 后面的区块布局都用百分比，具体元素大小用`px`计算

- 也就是宽度用百分比，高度用`px`
- 高度以及图片不要定死，让它自动撑开

五、Rem适配(常用)
---

- 根据屏幕的分辨率动态设置`html`的文字大小，达到等比缩放的功能
- 保证`html`最终算出来的字体大小，不能小于`12px`
- 在不同的移动端显示不同的元素比例效果
- 如果`html`的`font-size:20px `的时候，那么此时的`1rem = 20px`
- 把设计图的宽度分成多少分之一，根据实际情况

- `rem`做盒子的宽度，`viewport`缩放

> `head`加入常见的`meta`属性

```html
<meta name="format-detection" content="telephone=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<!--这个是关键-->
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0，minimum-scale=1.0">
```

> 把这段代码加入`head`中的`script`预先加载

```javascript
// rem适配用这段代码动态计算html的font-size大小
(function(win) {
    var docEl = win.document.documentElement;
    var timer = '';

    function changeRem() {
        var width = docEl.getBoundingClientRect().width;
        if (width > 750) { // 750是设计稿大小
            width = 750;
        }
        var fontS = width / 10; // 把设备宽度十等分 1rem=10px
        docEl.style.fontSize = fontS + "px";
    }
    win.addEventListener("resize", function() {
        clearTimeout(timer);
        timer = setTimeout(changeRem, 30);
    }, false);
    win.addEventListener("pageshow", function(e) {
        if (e.persisted) { //清除缓存
            clearTimeout(timer);
            timer = setTimeout(changeRem, 30);
        }
    }, false);
    changeRem();
})(window)
```


**布局细节**

- 结构用`section`区块划分更语义化
- 然后在`body`设置宽度

```css
body {
    width: 10rem;
    margin: auto;
}
```

> 后面的区块都以百分比布局

```html
<div class="wrapper">
   <header><header>
   <section><section>
   <section><section>
   <section><section>
   <section><section>
</div>
```

```css
section,
header {
    width: 100%;
}
```

**其他详情**

> 假定设计稿的大小为`750`，那么我们则将整个图分成`10`等份来看

- 那么，我们现在就让根部元素的`font-size`为`75px`

```css
html{
	font-size: 75px;
}
```

> 那么，我们现在就可以比对设计稿，比如设计稿中，有一个`div`元素，宽度，高度都为`20px`,那么我们这样写即可（可以用 `markman`标准设计稿的元素大小）

```css
div {
	height: 0.27rem; /*20/75*/
	width: 0.27rem;
}
```

- 动态计算的`rem`最后会帮我们动态计算元素在不同屏幕下的宽高
- 对于设计稿上的每个元素的尺寸在设计稿大小已知的时候，我们需要测量出，然后在用测量的宽高除以设计稿`750`的十分之一也就是`75`,得到我们想要的`rem`。而`rem`是根据屏幕动态变化的，也就达到了适配的效果。也就是同一套设计稿运用在不同的设备上。

> 比如当我们切换到`375`设备大小的时候，这时候`1rem=32px;` `div`的像素实际是`0.27*32=8.64px` `0.27`是我们在已知设计稿是`750`的情况下计算出来的，`rem`用来动态计算而已


**文字适配的解决方案**

- 对于一些标题性的文字，我们依然可以用`rem`。让他随着屏幕来进行缩放，因为标题性文字一般较大，而较大的文字，点阵对其影响就越小。这样，即使出现奇怪的尺寸，也能够让字体得到很好的渲染。
- 对于一些正文内容的文字（即站在使用者的角度，你不希望他进行缩放的文字）。我们采用`px`来进行处理

六、缩放比适配
---

> 固定宽度，改变缩放比例适配

- 设计图的宽度就是网页显示的宽度
- 改变视口的缩放比例
- 页面宽度固定死

```javascript
// 缩放比例适配方案--用这个代码 
var width = window.screen.width,
    fixedW = 320, //设计稿宽度的一半
    scale = width / fixedW, // 缩放比例
    meta = document.createElement('meta'),
    metaAttr = {
        name : 'viewport',
        content : 'width='+fixedW+', initial-scale='+scale+', maximum-scale='+scale+', user-scalable=0'
    };
    for (var key in metaAttr) {
        meta[key] = metaAttr[key];
    }
    document.head.appendChild(meta);
```




七、像素比适配
---

- `window.devicePixelRatio `
- 物理像素是手机屏幕分辨率 
- 独立像素 指`css`像素 屏幕宽度
- 像素比 = 物理像素 / `css宽度`
- 获取设备的像素比	`window.devicePixelRatio`



八、小结
---

> 关于移动端布局方案有很多，`rem`和百分比运用最多的


**相关文章阅读**

- http://blog.poetries.top/2017/05/23/mobile-adaptation/
- https://www.tuicool.com/articles/nmm6reE
