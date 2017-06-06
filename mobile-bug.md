---
title:  移动端Bug
date: 2017-06-06 16:35:08
tags: 移动端
categories: Front-End
---
### video 不能自动播放
---

- (1) `autoplay `及 `js `控制播放，仍然有部分设备不起作用
- (2) `$("html").one("touchstart",function(){
		video.play();
	})`

### input 输入框，软键盘弹出的时候会把底部的内容往上压缩，使得底部布局混乱
---

- 获取内容的总高度，赋值给body，写死`body`的高度

```javascript
$("body").css("height",parseInt($(".scroll-wrap").height()+$(".fixNav").height()));
```
- fix固定定位元素
	- 当获取焦点(软键盘出来时候) 就把fix定位的元素改为静态定位
	- 当失去焦点(软键盘消失时候) 再把原fix定位的元素改为fix定位

- `input`框被软键盘遮盖
	- `https://mingyili.github.io/2015/11/05.html`

### 手机拍照及上传
---

 - 部分安卓设备不支持
```html
   <input type="file" accept="image/*">
   <input type="file" accept="video/*">
```


### 移动端模拟hover
--- 

- `active`模拟` document.addEventListeren("touchstart",function(){},true)`;
- `js`监听触屏事件  动态添加/移除class类名

### 修改input  placeholder文字颜色
---

```css
input::-webkit-input-placeholder{color:red;}
```


### 去除webkit默认的滚动条
---

```css
element::-webkit-scrollbar{
	display:none
}
```