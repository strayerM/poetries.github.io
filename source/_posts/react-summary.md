---
title: react知识点回顾
date: 2017-17-07 19:55:24
tags: 
 - JavaScript
 - react
categories: Front-End
---

## 一、npm的配置


> 切换淘宝镜像源

```javascript
npm config set registry https://registry.npm.taobao.org

npm config get registry

npm install -g cnpm --registry=https://registry.npm.taobao.org
```

> 使用npm安装react

```javascript
cnpm install react react-dom --save
```


## 二、开发环境配置


> 这里使用`create-react-app`初始化项目


```javascript
npm install create-react-app -g
```

> 安装完成之后就可以在命令行使用 `create-react-app` 了，首先选择一个合适的目录，然后只需要简单地输入

```javascript
create-react-app yourfilename
```

## 三、认识JSX


### 3.1 JSX 简介

> `JSX` 其是一个语法扩展，它既不是单纯的字符串，也不是` HTML`，虽然长得和 `HTML` 很像甚至基本上看起来一样。但事实上它是 `React` 内部实现的一种，允许我们直接在 `JS` 里书写 `UI` 的方式

### 3.2 JSX 属性

> `JSX` 的标签同样可以拥有自己的属性

```javascript
const title = <h1 id="main">React Learning</h1>
```

```javascript
// 注意是 className 而不是 class
const title = <h1 className="main">React Learning</h1>
```

### 3.3 JSX 嵌套

> `JSX` 的标签也可以像 `HTML` 一样相互嵌套，一般有嵌套解构的 `JSX` 元素外面，我们习惯于为它加上一个小括号


```javascript
const title = (
    <div>
        <h1 className="main">React Learning</h1>
        <p>Let's learn JSX</p>
    </div>
)
```

> 需要注意的是，`JSX` 在嵌套时，最外层有且只能有一个标签，否则就会出错

```javascript
// 这是一个错误示例
const title = (            
    <h1 className="main">React Learning</h1>
    <p>Let's learn JSX</p>
)
```

### 3.4 JSX表达式

> 在 `JSX` 元素中，我们同样可以使用 `JavaScript` 表达式，在 `JSX` 当中的表达式需要用一个大括号括起来

```javascript
function sayhi(name) {
  return 'Hi,' + name
}

const title = (
    <div>
        <h1 className="main">React Learning</h1>
        <p>Let's learn JSX. <span>{sayhi('you')}</span></p>
    </div>
)
```

## 四、组件类型


### 4.1 函数定义与类定义组件


> 第一种函数定义组件，非常简单啦，我们只需要定义一个接收`props`传值，返回`React`元素的方法即可

```javascript
function Title(props) {
  return <h1>Hello, {props.name}</h1>
}
```

```javascript
// 甚至使用ES6的箭头函数简写之后可以变成这样
const Title = props => <h1>Hello, {props.name}</h1>
```

> 第二种是类定义组件，也就是使用`ES6`中新引入的类的概念来定义`React`组件

- 组件在定义好之后，可以通过`JSX`描述的方式被引用，组件之间也可以相互嵌套和组合

```javascript
class Title extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```

### 4.2 展示与容器组件


```javascript
// 展示组件

class CommentList extends React.Component {
  constructor(props) {
    super(props)
  }

  renderComment({body, author}) {
    return <li>{body}—{author}</li>
  }
  
  render() { 
    return <ul> {this.props.comments.map(this.renderComment)} </ul>
  } 
  
}
```

```javascript
// 容器组件
class CommentListContainer extends React.Component {
  constructor() {
    super()
    this.state = { comments: [] }
  }
  
  componentDidMount() {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: function(comments) {
        this.setState({comments: comments})
      }.bind(this)
    })
  }
  
  render() {
    return <CommentList comments={this.state.comments} />
  }
}
```

**展示组件**

- 主要负责组件内容如何展示
- 从`props`接收父组件传递来的数据
- 大多数情况可以通过函数定义组件声明

**容器组件**

- 主要关注组件数据如何交互
- 拥有自身的`state`，从服务器获取数据，或与`redux`等其他数据处理模块协作
- 需要通过类定义组件声明，并包含生命周期函数和其他附加方法

**那么这样写具体有什么好处呢？**

- 解耦了界面和数据的逻辑
- 更好的可复用性，比如同一个回复列表展示组件可以套用不同数据源的容器组件
- 利于团队协作，一个人负责界面结构，一个人负责数据交互

### 4.3 有状态与无状态组件


**有状态组件**

> 这个组件能够获取储存改变应用或组件本身的状态数据，在`React`当中也就是`state`，一些比较明显的特征是我们可以在这样的组件当中看到对`this.state`的初始化，或`this.setState`方法的调用

**无状态组件**

> 这样的组件一般只接收来自其他组件的数据。一般这样的组件中只能看到对`this.props`的调用

```javascript
// 有状态组件
class StatefulLink extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      active: false
    }
  }
  handleClick() {
    this.setState({
      active: !this.state.active
    })
  }
  render() {
    return <a 
          style={{ color: this.state.active ? 'red' : 'black' }}
          onClick={this.handleClick.bind(this)}
         >
           Stateful Link
         </a>
  }
}
```

```javascript
// 无状态组件
class StatelessLink extends React.Component {
  constructor(props) {
    super(props)
  }
  handleClick() {
    this.props.handleClick(this.props.router)
  }
  render() {
    const active = this.props.activeRouter === this.props.router
    return (
        <li>
            <a 
              style={{ color: active ? 'red' : 'black' }}
              onClick={this.handleClick.bind(this)}
             >
                Stateless Link
            </a>
    </li>
    )
  }
}
```


> 在`React`的实际开发当中，我们编写的组件大部分都是无状态组件。毕竟`React`的主要作用是编写用户界面。再加上`ES6`的新特性，绝大多数的无状态组件都可以通过箭头函数简写成类似下面这样

```javascript
const SimpleButton = props => <button>{props.text}</button>
```

### 4.4 受控与非受控组件


**受控组件**

> 比如说设置了`value`的`<input>` 是一个受控组件。对于受控的`<input>`，渲染出来的`html`元素始终保持着`value`属性的值，如以下代码

![](https://pic2.zhimg.com/v2-fd4a419002278790cf9c365b3dc9a2f5_b.png)


- 此时如果想要更新用户的值。需要使用`onChange`事件

![](https://pic2.zhimg.com/v2-bb660fb6484cea3262b7bd07a3778435_b.png)

**非受控组件**

> 即没有设置`value`或者设置为`null`的是一个非受控组件，对于非受控的`input`组件，用户的输入会直接反映在页面上

![](https://pic1.zhimg.com/v2-9358c1c15d4e6b803191cf3d112dd350_b.png)


- 上面的代码渲染出一个空值的输入框，用户的输入立即会反映在元素上
- 和受控组件一样，使用`onChange`事件来监听值的变化，如果想要给组件设置一个非空的初始值。可以使用`defaultValue`属
- 通常情况下，`React`当中所有的表单控件都需要是受控组件

### 4.5 组合与继承


- `React`当中的组件是通过嵌套或组合的方式实现组件代码复用的
- 通过`props`传值和组合使用组件几乎可以满足所有场景下的需求。这样也更符合组件化的理念，就好像使用互相嵌套的`DOM`元素一样使用`React`的组件，并不需要引入继承的概念



> 继承的写法并不符合`React`的理念。在`React`当中`props`其实是非常强大的，`props`几乎可以传入任何东西，变量、函数、甚至是组件本身


```javascript
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  )
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  )
}
```

> React官方也希望我们通过组合的方式来使用组件，如果你想实现一些非界面类型函数的复用，可以单独写在其他的模块当中在引入组件进行使用


## 五、组件数据
---

### 5.1 props

- 传入变量
- 传入函数
- 传入组件
- `props.children`

> - 在形式上，`props`之于`JSX`就相当于`attributes`之于`HTML`。从写法上来看呢，我们为组件传入`props`就可以像为`HTML`标签添加属性一样
> - 在概念上，props对于组件就相当于JS中参数之于函数。我们可以抽象出这样一个函数来解释

- `props` 几乎可以传递所有的内容，包括变量、函数、甚至是组件本身

**props是只读的**

- 在`React`中，`props`都是自上向下传递，从父组件传入子组件
- 并且`props`是只读的，我们不能在组件中直接修改`props`的内容
- 也即是说组件只能根据传入的`props`渲染界面，而不能在其内部对`props`进行修改

**props类型检查**

> 正是因为`props`的强大，什么类型的内容都可以传递，所以在开发过程中，为了避免错误类型的内容传入，我们可以为`props`添加类型检查


**props默认值**

> 由于`props`是只读的，我们不能直接为`props`赋值。`React`专门准备了一个方法定义`props`的默认值

```javascript
import React from 'react'
import PropTypes from 'prop-types'

const Title = props => <h1>{props.title}</h1>

Title.defaultProps = {
  title: 'Wait for parent to pass props.'
}

Title.propTypes = {
  title: PropTypes.string.isRequired
}
```

### 5.2 state

- 初始化
- `setState`方法
- 向下传递数据

> - 在`React`中`state`也是我们进行数据交互的地方，又或者叫做`state management`状态管理。
> - 一个应用需要进行数据交互，比如同服务器之间的交互，同用户输入进行交互。话反过来，从`API`获取数据，处理用户输入也就是我们需要用到`state`的时候

- 在新版本的`React`当中，我们通过类定义组件来声明一个有状态组件，之后在它的构造方法中初始化组件的`state`，我们可以先赋予它默认值。
- 之后就可以在组件中通过`this.state`来访问它，既然是`state`那么肯定涉及到数据的改变，因此我们还需额外定义一个负责处理`state`变化的函数，这样的函数中一般都会包含`this.setState`这个方法
- 和之前的`props`一样，初始化`state`之后，如果我们想改变它，是不可以直接对其赋值的，直接修改`state`的值没有任何意义，因为这样的操作脱离了`React`运行的逻辑，不会触发组件的重新渲染。所以需要`this.setState`这个方法，在改变`state`的同时，触发`React`内部的一系列函数，最后在页面上重新渲染出组件

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      counter: 0
    }
  }
  
  addOne() {
    this.setState((prevState) =>({
      counter: prevState.counter + 1
    }))
  }
  
  render() {
    return (
      <div>
        <p>{ this.state.counter }</p>
        <button
          onClick={() => this.addOne()}>
          Increment
        </button>
      </div>
    )
  }
}
```

六、组件生命周期
---

### 6.1 React是如何渲染组件的

> - 在新版本的`React`当中，`React`的底层被重写了。`React`换上了一个新的引擎，这个引擎叫做`React Fiber.React Fiber` 作用的也即是`React`最核心的功能，它将`React`应用界面更新的过程分为了两个主要的部分：

- 调度过程
- 执行过程

> 在调度过程中，有4个生命周期函数会被触发

- `componentWillMount`
- `componentWillReceiveProps`
- `shouldComponentUpdate`
- `componentWillUpdate`

> 在执行过程中，有3个生命周期函数会被触发：

- `componentDidMount`
- `componentDidUpdate`
- `componentWillUnmount`

### 6.2 React组件生命周期方法

> `React`为了方便我们更好地控制自己的应用，提供了许多预置的生命周期方法。这些固定的生命周期方法分别会在组件的挂载流程、更新流程、卸载流程中触发

- `componentWillMount` 开始插入真实DOM
- `componentDidMount` 插入真实`DOM`完成
- `componentWillUpdate` 开始重新渲染
- `componentDidUpdate` 重新渲染完成
- `componentWillUnmount `已移出真实 `DOM`
- `componentWillReceiveProps` 已加载组件收到新的参数时调用
- `shouldComponentUpdate `组件判断是否重新渲染时调用


![image.png](http://upload-images.jianshu.io/upload_images/1480597-2921ad93a9b5c407.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/1480597-5c75fb0760cf0c1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**componentDidMount**

> 在此方法中可进行

- 与其他 `JavaScript` 框架集成，如初始化 `jQuery` 插件；
- 使用 `setTimeout`/`setInterval` 设置定时器；
- 通过 `Ajax`/`Fetch` 获取数据；
- 绑定 `DOM` 事件


### 6.3 总结

- React组件渲染包含三个流程：挂载流程、更新流程、卸载流程
- 各个生命周期函数会在特定的时刻触发并适用于不同的使用场景
- 通过使用生命周期函数我们可以对应用进行更精准的控制
- 如果你需要发起网络请求，将其安排在合适的生命周期函数中是值得推荐的做法
- 了解掌握`React`组件渲染的流程和原理对我们更深入掌握`React`非常有帮助


## 七、表单及事件处理


### 7.1 表单

> 受控与非受控组件就是专门适用于React当中的表单元素的

- 只要是有表单出现的地方，就会有用户输入，就会有表单事件触发，就会涉及的数据处理
- 在我们用`React`开发应用时，为了更好地管理应用中的数据，响应用户的输入，编写组件的时候呢，我们就会运用到受控组件与非受控组件这两个概念。


### 7.2 表单元素


> 我们在组件中声明表单元素时，一般都要为表单元素传入应用状态中的值，可以通过`state`也可以通过`props`传递，之后需要为其绑定相关事件，例如表单提交，输入改变等。在相关事件触发的处理函数中，我们需要根据表单元素中用户的输入，对应用数据进行相应的操作和改变

```javascript
class ControlledInput extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      value: ""
    }
  }

  handleChange(event) {
    this.setState({
      value: event.target.value
      })
  }

  render() {
    return <input 
              type="text" 
              value={this.state.value} 
              onChange={() => this.handleChange()} 
            />
  }
}
```

> 受控组件的输入数据是一直和我们的应用状态绑定的，事件处理函数中一定要有关`state`的更新操作，这样表单组件才能及时正确响应用户的输入


**textarea**

```html
<!--HTML-->
<textarea>
  Hello there, this is some text in a text area
</textarea>

<!--jsx-->
<textarea value={this.state.value} onChange={this.handleChange} />
```

**select**

```html
<!--HTML-->
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>

<!--jsx-->
<select value={this.state.value} onChange={this.handleChange}>
    <option value="grapefruit">Grapefruit</option>
    <option value="lime">Lime</option>
    <option value="coconut">Coconut</option>
    <option value="mango">Mango</option>
</select>
```


### 7.3 事件

```html
<!--HTML-->
<button onclick="activateLasers()">
  Activate Lasers
</button>

<!--jsx-->
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

## 八、redux-router
![react-router](http://upload-images.jianshu.io/upload_images/1480597-cae1c4d6de6642de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 九、redux


### 9.1 redux入门


### 9.2 中间件和异步处理



## 十、思维导图总结

![image.png](http://upload-images.jianshu.io/upload_images/1480597-d20b7699e8d0624c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
