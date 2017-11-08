---
title: 浅谈styled-components
date: 2017-11-08 16:55:24
tags: 
 - JavaScript
 - react
categories: Front-End
---


一、简介
---

> `styled components`一种全新的控制样式的编程方式，它能解决`CSS`全局作用域的问题，而且移除了样式和组件间的映射关系

```javascript
import React from 'react';
import styled from 'styled-components';
import { render } from 'react-dom';
 
const Title = styled.h1`
    font-size: 1.5em;
    text-align: center;
    color: palevioletred;
`;
 
class App extends React.Component {
    render() {
        return (
            <Title>Hello world</Title>
        )
    }
}
 
render(
    <App />,
    document.getElementById('app')
);
 
```

- `styled.h1`是一个标签模板函数

> `styled.h1`函数返回一个`React Component`，`styled components`会为这个`React Component`添加一个`class`，该`class`的值为一个随机字符串。传给`styled.h1`的模板字符串参数的值实际上是`CSS`语法，这些`CSS`会附加到该`React Component`的`class`中，从而为`React Component`添加样式

![image.png](http://upload-images.jianshu.io/upload_images/1480597-1c0b2f09980a8a0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


二、基于 props 定制主题
---

```javascript
const Button = styled.button`
  background: ${props => props.primary ? 'palevioletred' : 'white'};
  color: ${props => props.primary ? 'white' : 'palevioletred'};
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
  </div>
);
```

> 我们在组件中传入的所有 `props` 都可以在定义组件时获取到，这样就可以很容易实现组件主题的定制。如果没有 `styled-components `的情况下，需要使用组件 `style` 属性或者定义多个 `class` 的方式来实现

三、组件样式继承
---

> 通常在 `css` 中一般会通过给 `class `传入多个 `name` 通过空格分隔的方式来复用 `class` 定义，类似 `class="button tomato"`。在 `styled-components `中利用了 `js` 的继承实现了这种样式的复用：

```javascript
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

const TomatoButton = Button.extend`
  color: tomato;
  border-color: tomato;
`;
```

> 子组件中的属性会覆盖父组件中同名的属性

四、组件内部使用 className
---

> 在日常开发中总会出现覆盖组件内部样式的需求，你可能想在 `styled-components` 中使用 `className`，或者在使用第三方组件时。

```javascript
<Wrapper>
  <h4>Hello Word</h4>
  <div className="detail"></div>
</Wrapper>
```

五、组件中维护其他属性
---

> `styled-components` 同时支持为组件传入 `html` 元素的其他属性，比如为 `input` 元素指定一个 `type` 属性，我们可以使用 `attrs` 方法来完成

```javascript
const Password = styled.input.attrs({
  type: 'password',
})`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;
```

- 在实际开发中，这个方法还有一个有用处，用来引用第三方类库的 `css `样式：

```javascript
const Button = styled.button.attrs({
  className: 'small',
})`
  background: black;
  color: white;
  cursor: pointer;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid black;
  border-radius: 3px;
`;
```
- 编译后的 `html` 结构如下：

```javascript
<button class="sc-gPEVay small gYllyG">
  Styled Components
</button>
```

> 可以用这种方式来使用在别处定义的 `small` 样式，或者单纯为了识别自己定义的 `class`，因为正常情况下我们得到的 `class `名是不可读的编码

六、CSS 动画支持
---

- `styled-components` 同样对 `css` 动画中的 `@keyframe` 做了很好的支持。

```javascript
import { keyframes } from 'styled-components';
const fadeIn = keyframes`
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
`;

const FadeInButton = styled.button`
  animation: 1s ${fadeIn} ease-out;
`;
```

七、兼容现在已有的 react components 和 css 框架
---

> `styled-components` 采用的 `css-module` 的模式有另外一个好处就是可以很好的与其他的主题库进行兼容。因为大部分的 `css` 框架或者` css `主题都是以 `className` 的方式进行样式处理的，额外的 `className` 和主题的 `className` 并不会有太大的冲突

- `styled-components` 的语法同样支持对一个 `React `组件进行扩展

```javascript
const StyledDiv = styled(Row)`
  position: relative;
  height: 100%;
  .image img {
    width: 100%;
  }
  .content {
    min-height: 30em;
    overflow: auto;
  }
  .content h2 {
    font-size: 1.8em;
    color: black;
    margin-bottom: 1em;
  }
`;
```

八、总结
---

- 提出了 `container` 和 `components` 的概念，移除了组件和样式之间的映射关系，符合关注度分离的模式；
- 可以在样式定义中直接引用到 `js` 变量，共享变量，非常便利；
- 支持组件之间继承，方便代码复用，提升可维护性；
- 兼容现有的 `className` 方式，升级无痛；
