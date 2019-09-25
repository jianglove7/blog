---
title: React——基础
date: 2019-09-22 09:20:39
tags: 
- React
categories:
- javascript
---
# 一.基础

## 1.介绍

### React和Vue的对比

这是前端最火的两个框架，虽然说React是世界使用人数最多的框架，但是就在国内而言Vue的使用者很有可能超过React。两个框架都是非常优秀的，所以他们在技术和先进性上不相上下。

那个人而言在接到一个项目时，我是如何选择的那？`React.js`相对于`Vue.js`它的灵活性和协作性更好一点，所以我在处理复杂项目或公司核心项目时，React都是我的第一选择。而`Vue.js`有着丰富的API，实现起来更简单快速，所以当团队不大，沟通紧密时，我会选择Vue，因为它更快速更易用。



## 2.安装

### 1.脚手架的安装

```text
npm install -g create-react-app
```

### 2.创建第一个React项目

```text
D:  //进入D盘
mkdir ReactDemo  //创建ReactDemo文件夹
create-react-app demo01   //用脚手架创建React项目
cd demo01   //等创建完成后，进入项目目录
npm start   //预览项目，如果能正常打开，说明项目创建成功
```





## 3.入口&组件

### 1.入口文件的编写

在`src目录`下，新建一个文件`index.js`

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
ReactDOM.render(<App />,document.getElementById('root'))  //挂载到哪里
```



### 2.app组件的编写

现在写一下App组件，这里我们采用最简单的写法，就输出一个`Hello JSPang`,就可以了。

```javascript
import React, {Component} from 'react'

class App extends Component{
    render(){
        return (
            <div>
                Hello JSPang
            </div>
        )
    }
}
export default App;
```





## 4.JSX注意点

### 1.组件包裹原则

只可以有一个根节点，要是没有包裹会报错
```javascript
import React,{Component} from 'react'

class Xiaojiejie extends Component{
    render(){
        return  (
               <div><input /> <button> 增加服务 </button></div>
               <ul>
                   <li>头部按摩</li>
                   <li>精油推背</li>
               </ul> 
        )
    }
}
export default Xiaojiejie 
```



### 2.Fragment标签讲解

加上最外层的DIV，组件就是完全正常的，但是你的布局就偏不需要这个最外层的标签怎么办?比如我们在作`Flex`布局的时候,外层还真的不能有包裹元素。这种矛盾其实React16已经有所考虑了，为我们准备了`<Fragment>`标签。

要想使用`<Fragment>`，需要先进行引入。

```javascript
import React,{Component,Fragment } from 'react'
```





### 3.JSX代码注释

jsx注释不能使用 `//`

```jsx
	<Fragment>
    {/* 正确注释的写法 */}
    {
        //正确注释的写法 
    }
    <div>
        <input value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
        <button onClick={this.addList.bind(this)}> 增加服务 </button>
    </div>
```

如果你记不住，有个简单的方法，就是用`VSCode`的快捷键，直接按`Ctrl+/`，就会自动生成正确的注释了。



### 4.JSX中的class陷阱

添加class 时候需要用className=“”而不是class=“”，它是防止和`js`中的`class`类名 冲突，所以要求换掉。这也算是一个小坑吧。

```jsx
<input className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
```



### 5.JSX中的`html`解析问题

如果想在文本框里输入一个`<h1>`标签，并进行渲染。默认是不会生效的，只会把`<h1>`标签打印到页面上，这并不是我想要的。如果工作中有这种需求，可以使用`dangerouslySetInnerHTML`属性解决。

```jsx
<ul>
    {
        this.state.list.map((item,index)=>{
            return (
                <li 
                    key={index+item}
                    onClick={this.deleteItem.bind(this,index)}
                    dangerouslySetInnerHTML={{__html:item}}
                >
                </li>
            )
        })
    }
</ul> 
```



### 6.JSX中`<label>`标签的坑

不能使用`for`.它容易和javascript里的for循环混淆，会提示你使用`htmlfor`。

```jsx
<div>
    <label htmlFor="jspang">加入服务：</label>
    <input id="jspang" className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
    <button onClick={this.addList.bind(this)}> 增加服务 </button>
</div>
```









## 5.响应式设计

### 1.数据的绑定

`React`不建议你直接操作`DOM`元素,而是要通过数据进行驱动，改变界面中的效果。React会根据数据的变化，自动的帮助你完成界面的改变。所以在写React代码时，你无需关注DOM相关的操作，只需要关注数据的操作就可以了（这也是React如此受欢迎的主要原因，大大加快了我们的开发速度）。

```html
<input value={this.state.inputValue} /> 
```



### 2.绑定事件

这时候你到界面的文本框中去输入值，是没有任何变化的，这是因为我们强制绑定了`inputValue`的值。如果要想改变，需要绑定**响应事件**，改变`inputValue`的值。比如绑定一个改变事件，这个事件执行`inputChange()`(当然这个方法还没有)方法。

```html
 <input value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
```

需要注意，方法中的this需要用bind指定到当前类



### 3.修改状态

react中修改状态需要用`setState`方法而不是直接赋值

```javascript

// this.state.inputValue=e.target.value;
this.setState({
    inputValue:e.target.value
})
```



### 4.setState方法

更新虚拟dom的状态，这是一个异步方法

参数：

参数1是要修改的属性的对象集合

参数2是回调函数







### 4.让列表数据化

现在的列表还是写死的两个`<li>`标签，那要变成动态显示的，就需要把这个列表先进行数据化，然后再用`javascript`代码，循环在页面上。

```jsx
render(){
    return  (
        <Fragment>
            <div>
                <input value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
                <button> 增加服务 </button>
            </div>
            <ul>
                {
                    this.state.list.map((item,index)=>{
                        return <li key={index+item}>{item}</li>
                    })
                }
            </ul>  
 
        </Fragment>
    )
}
```

注意：循环列表需要带key属性，否则报警告



### 5.删除数据需要注意

获得了数据下标后,删除数据就变的容易起来.先声明一个局部变量,然后利用传递过来的下标,删除数组中的值.删除后用`setState`更新数据就可以了.

```javascript
//删除单项服务
deleteItem(index){
    let list = this.state.list
    list.splice(index,1)
    this.setState({
        list:list
    })
    
}
```

其实这里边是有一个坑的,有的小伙伴肯定会认为下面的代码也是正确的.

```javascript
//删除单项服务
deleteItem(index){
    this.state.list.splice(index,1)
    this.setState({
        list:this.state.list
    }) 
}
```

记住React是禁止直接操作state的,虽然上面的方法也管用,但是在后期的性能优化上会有很多麻烦,所以一定不要这样操作.这也算是我`React`初期踩的比较深的一个坑,希望小伙伴们可以跳坑







# 二.组件

## 1.例子

XiaojiejieItem.js

```javascript
import React, { Component } from 'react'; //imrc
class XiaojiejieItem  extends Component { //cc
   
    render() { 
        return ( 
            <div>小姐姐</div>
         );
    }
}
export default XiaojiejieItem;
```



app.js

```javascript
import React,{Component,Fragment } from 'react'
import './style.css'
import XiaojiejieItem from './XiaojiejieItem'

class Xiaojiejie extends Component{
//js的构造函数，由于其他任何函数执行
constructor(props){
    super(props) //调用父类的构造函数，固定写法
    this.state={
        inputValue:'' , // input中的值
        list:['基础按摩','精油推背']    //服务列表
    }
}

render(){
    return  (
        <Fragment>
            {/* 正确注释的写法 */}
<div>
    <label htmlFor="jspang">加入服务：</label>
    <input id="jspang" className="input" value={this.state.inputValue} onChange={this.inputChange.bind(this)} />
    <button onClick={this.addList.bind(this)}> 增加服务 </button>
</div>
            <ul>
                {
                    this.state.list.map((item,index)=>{
                        return (
                            //----------------关键修改代码----start
                            <div>
                                <XiaojiejieItem />
                            </div>
                            //----------------关键修改代码----end
                           
                        )
                    })
                }
            </ul>  
        </Fragment>
    )
}

    inputChange(e){
        // console.log(e.target.value);
        // this.state.inputValue=e.target.value;
        this.setState({
            inputValue:e.target.value
        })
    }
    //增加服务的按钮响应方法
    addList(){
        this.setState({
            list:[...this.state.list,this.state.inputValue],
            inputValue:''
        })

    }
//删除单项服务
deleteItem(index){
    let list = this.state.list
    list.splice(index,1)
    this.setState({
        list:list
    })
    
}

}
export default Xiaojiejie 
```





## 2.父子组件的传值

### 1.父组件向子组件传值
在组件里写入属性进行传值
```jsx
<XiaojiejieItem content={item} />
```

用`this.props.content`接受

```javascript
import React, { Component } from 'react'; //imrc
class XiaojiejieItem  extends Component { //cc
   
    render() { 
        return ( 
            <div>{this.props.content}</div>
         );
    }
}
 
export default XiaojiejieItem;
```

**父组件向子组件传递内容，靠属性的形式传递。**





### 2.子组件向父组件传递数据

React有明确规定，子组件时不能操作父组件里的数据的，所以需要借助一个父组件的方法，来修改父组件的内容。

将父组件的方法作为属性传给子组件

```jsx
<ul>
    {
        this.state.list.map((item,index)=>{
            return (
                <XiaojiejieItem 
                key={index+item}  
                content={item}
                index={index}
                //关键代码-------------------start
                deleteItem={this.deleteItem.bind(this)}
                //关键代码-------------------end
                />
            )
        })
    }
</ul>  
```

注意要将this绑定到父组件自身

传递后，在子组件里直接调用就可以了，代码如下：

```javascript
handleClick(){
    this.props.deleteItem(this.props.index)
}
```



### 3.constructor里面使用bind

另外，的bind写法：

在constructor里面绑定

```javascript
import React, { Component } from 'react'; //imrc
class XiaojiejieItem  extends Component { //cc
   //--------------主要代码--------start
   constructor(props){
       super(props)
       this.handleClick=this.handleClick.bind(this)
   }
   //--------------主要代码--------end
    render() { 
        return ( 
            <div onClick={this.handleClick}>
                {this.props.content}
            </div>
        );
    }
    handleClick(){
        console.log(this.props.index)
    }
}
 
export default XiaojiejieItem;
```





## 3.单向数据流

例如：

```jsx
<ul>
    {
        this.state.list.map((item,index)=>{
            return (
                <XiaojiejieItem 
                key={index+item}  
                content={item}
                index={index}
                list={this.state.list}
                deleteItem={this.deleteItem.bind(this)}
                />
            )
        })
    }
</ul> 
```

```js
handleClick(){
    //关键代码——---------start
    this.props.list=[]
    //关键代码-----------end
    this.props.deleteItem(this.props.index)
}
```

会报错

```js
TypeError: Cannot assign to read only property 'list' of object '#<Object>'
```





## 4.函数式编程

在面试React时，经常会问道的一个问题是：函数式编程的好处是什么？

1. 函数式编程让我们的代码更清晰，每个功能都是一个函数。
2. 函数式编程为我们的代码测试代理了极大的方便，更容易实现前端自动化测试。

React框架也是函数式编程，所以说优势在大型多人开发的项目中会更加明显，让配合和交流都得心应手。

总结:这节课虽然都是些理论知识，这些知识在面试中经常被问到，所以也是必须掌握的内容。





## 5.PropTypes校验传递值

在父组件向子组件传递数据时，使用了属性的方式，也就是props，但对传值的类型没有任何的限制。这在工作中是完全不允许的，因为大型项目，如果你不校验，后期会变的异常混乱，业务逻辑也没办法保证。

### 1.PropTypes的简单应用

```js
import PropTypes from 'prop-types'
```

```js
import React, { Component } from 'react'; //imrc
import PropTypes from 'prop-types'

class XiaojiejieItem  extends Component { //cc
   
   constructor(props){
       super(props)
       this.handleClick=this.handleClick.bind(this)
   }
   
    render() { 
        return ( 
            <div onClick={this.handleClick}>
                {this.props.content}
            </div>
        );
    }

    handleClick(){
        
        this.props.deleteItem(this.props.index)
    }
    
}
 //--------------主要代码--------start
XiaojiejieItem.propTypes={
    content:PropTypes.string,
    deleteItem:PropTypes.func,
    index:PropTypes.number
}
 //--------------主要代码--------end
export default XiaojiejieItem;
```

限制必填

```js
avname:PropTypes.string.isRequired
```





### 2.使用默认值`defaultProps`

```js
XiaojiejieItem.propTypes={
    content:PropTypes.string,
    deleteItem:PropTypes.func,
    index:PropTypes.number
}
XiaojiejieItem.defaultProps = {
    avname:'松岛枫'
}
```





## 6.ref的使用方法

以前的案例中，我们写了下面的代码，使用了`e.target`，这并不直观，也不好看。这种情况我们可以使用`ref`来进行解决。

```javascript
inputChange(e){
    
    this.setState({
        inputValue:e.target.value
    })
}
```

如果要使用`ref`来进行，需要现在`JSX`中进行绑定， 绑定时最好使用ES6语法中的箭头函数，这样可以简洁明了的绑定DOM元素。

```jsx
<input 
    id="jspang" 
    className="input" 
    value={this.state.inputValue} 
    onChange={this.inputChange.bind(this)}
    //关键代码——----------start
    ref={(input)=>{this.input=input}}
    //关键代码------------end
    />
```

绑定后可以把上边的类改写成如下代码:

```js
inputChange(){
    this.setState({
        inputValue:this.input.value
    })
}
```





## 7.生命周期

### 1.initialization：

在类里的constructor方法里进行



### 2.mounting：

`componentWillMount()`挂载之前

`render()`

`componentDidMount()`挂载之后



### 3.update：

`componentWillReceiveProps`

组件第一次存在于dom中，函数是不会被执行

如果已经存在Dom中，函数才会被执行



`shouldComponentUpdate`,返回true|false决定是否往后执行 

```javascript
shouldComponentUpdate(){
return [true|false]
}
```

`componentWillUpdate`更新之前

`render()`

`componentDidUpdate`更新之后



### 4.unmounting：

`componentWillUnmount`移除之前，





### 5.拓展：性能优化

父组件改变会触发子组件的更新，这会导致性能问题，这时可以使用下面的方法适当阻止更新：

```javascript
shouldComponentUpdate(nextProps,nextState){
    return nextProps.content!==this.props.content
}
```





# 三.动画

## 1.css过渡

```jsx
<div className={this.state.isShow ? 'show' : 'hide'}>BOSS级人物-孙悟空</div>
```

```css
.show{ opacity: 1; transition:all 1.5s ease-in;}
.hide{opacity: 0; transition:all 1.5s ease-in;}
```



## 2.css3动画

### 1.keyframes动画介绍

此属性与`animation`属性是密切相关的，`keyframes`译成中文就是关键帧，我最早接触这个关键帧的概念是字flash中，现在Flash已经退出历史舞台了。他和`transition`比的优势是它可以更加细化的定义动画效果。比如我们设置上节课的按钮隐藏动画，不仅可以设置透明度，还可以设置颜色。

```css
@keyframes hide-item{
    0% {
        opacity:1;
        color:yellow;
    }
    50%{
        opacity: 0.5 ;
        color:red;
    }
    100%{
        opacity:0;
        color: green;
    }
}
```


### 2.使用动画

使用动画的关键词是`animation`，然后后边跟上你的制作的动画名称，如下面这段代码。

```css
.hide{ animation:hide-item 2s ease-in ; }
```

但是你会发现，动画执行一遍后又恢复了原状，这个是因为没设置`forwards`属性，它是用来控制停止到最后一帧的。 我们把代码改写成下面的样子。

```css
.hide{ animation:hide-item 2s ease-in forwards; }
```



## 3.react-transition-group

`react-transition-group`是react官方提供的动画过渡库，有着完善的API文档

### 1.安装

```text
npm install react-transition-group --save
```

安装好后，你可以先去github上来看一下文档，他是有着三个核心库（或者叫组件）。

- Transition
- CSSTransition
- TransitionGroup



### 2.CSSTransition的使用

`<CSSTransition>`，而且不再需要管理`className`了，这部分由`CSSTransition`进行管理。修改上节课写的`Boss.js`文件里的`render`区域。

```jsx
render() { 
    return ( 
        <div>
            <CSSTransition 
                in={this.state.isShow}   //用于判断是否出现的状态
                timeout={2000}           //动画持续时间
                classNames="boss-text"   //className值，防止重复
            >
                <div>BOSS级人物-孙悟空</div>
            </CSSTransition>
            <div><button onClick={this.toToggole}>召唤Boss</button></div>
        </div>
        );
}
```

需要注意的是`classNames`这个属性是由`s`的，如果你忘记写，会和原来的`ClassName`混淆出错，这个一定要注意。

我们把上节课的代码进行了改造，然后你就可以到CSS中改写`style`了。在修改样式之前，有那些类名。

- xxx-enter: 进入（入场）前的CSS样式；
- xxx-enter-active:进入动画直到完成时之前的CSS样式;
- xxx-enter-done:进入完成时的CSS样式;
- xxx-exit:退出（出场）前的CSS样式;
- xxx-exit-active:退出动画知道完成时之前的的CSS样式。
- xxx-exit-done:退出完成时的CSS样式。

知道了这些要设置的CSS，就可以删除原来写的CSS了，把下面的代码写上：

```css
.input {border:3px solid #ae7000}

.boss-text-enter{
    opacity: 0;
}
.boss-text-enter-active{
    opacity: 1;
    transition: opacity 2000ms;

}
.boss-text-enter-done{
    opacity: 1;
}
.boss-text-exit{
    opacity: 1;
}
.boss-text-exit-active{
    opacity: 0;
    transition: opacity 2000ms;

}
.boss-text-exit-done{
    opacity: 0;
}
```

### 1.unmountOnExit 属性

学到这里，会感觉这样写也没有简化多少，更没特殊的效果，技术胖你又玩我。

其实不是的，比如我们给`<CSSTransition>`加上`unmountOnExit`,加上这个的意思是在元素退场时，自动把DOM也删除，这是以前用CSS动画没办法做到的。

比如我们把代码写成这个样子：

```js
render() { 
    return ( 
        <div>
            <CSSTransition 
                in={this.state.isShow}   //用于判断是否出现的状态
                timeout={2000}           //动画持续时间
                classNames="boss-text"   //className值，防止重复
                unmountOnExit
            >
                <div>BOSS级人物-孙悟空</div>
            </CSSTransition>
            <div><button onClick={this.toToggole}>召唤Boss</button></div>
        </div>
        );
}
```



# 工具

## 1.React进阶-Simple React Snippets

**1.介绍**

在工作中你经常会看到程序老司机写代码是非常快的，甚至让你烟花缭乱，那他们真的是单身那么多年，练就了超级快手吗?当然不是，只是他们使用了快速生成插件，这节课我就向大家介绍一个`vscode`中的`Simple React Snippets`，有了这个插件，稍加练习，你也可以像老司机一样，拥有加藤鹰的圣手(如果不懂请自行搜索吧)。



**2.命令**

直接在`vscode`中输入`imrc`，就会快速生成最常用的import代码。

```javascript
import React, { Component } from 'react';
```



快捷键`cc`.插件就会快速帮我们生成如下代码：

```javascript
class  extends Component {
    state = {  }
    render() { 
        return (  );
    }
}
 
export default ;
```





## 2.React高级-调试工具的安装及使用

其实React在浏览器端是有一个调试工具的，这就是`React developer tools`，这个是React人必下的一个调试工具。这节课就主要学习一下`React developer tools`的下载和简单使用。

**React developer tools的三种状态**

`React developer tools`有三种颜色，三种颜色代表三种状态：

1. 灰色： 这种就是不可以使用，说明页面不是又React编写的。
2. 黑色: 说明页面是用React编写的，并且处于生成环境当中。
3. 红色： 说明页面是用React编写的，并且处于调试环境当中。

**React developer tools使用**

打开浏览器，然后按`F12`,打开开发者工具，然后在面板的最后一个，你会返现一个`React`,这个就是安装的插件了。





## 3.axios使用

推荐初始化数据在`componentDidMount`里进行



## 4.easyMock

远程mock网站 http://easy-mock.com