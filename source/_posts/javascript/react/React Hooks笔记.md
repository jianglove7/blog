---
title: React——React Hooks笔记
date: 2019-09-26 00:20:39
tags: 
- React
categories:
- javascript
---
## 1.React Hooks介绍和环境搭建

### React Hooks 简介

`React Hooks`就是用函数的形式代替原来的继承类的形式，并且使用预函数的形式管理`state`，有Hooks可以不再使用类的形式定义组件了。这时候你的认知也要发生变化了，原来把组件分为有状态组件和无状态组件，有状态组件用类的形式声明，无状态组件用函数的形式声明。那现在所有的组件都可以用函数来声明了。



### React Hooks 编写形式对比

原始写法:

```js
import React, { Component } from 'react';

class Example extends Component {
    constructor(props) {
        super(props);
        this.state = { count:0 }
    }
    render() { 
        return (
            <div>
                <p>You clicked {this.state.count} times</p>
                <button onClick={this.addCount.bind(this)}>Chlick me</button>
            </div>
        );
    }
    addCount(){
        this.setState({count:this.state.count+1})
    }
}
 
export default Example;
```

React Hooks 写法：

```js
import React, { useState } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```





## 2.useState 的介绍和多状态声明

### useState的介绍

> `useState`是react自带的一个hook函数，它的作用是用来声明状态变量。

那我们从三个方面来看`useState`的用法，分别是声明、读取、使用（修改）。这三个方面掌握了，你基本也就会使用`useState`了.

声明：

```js
const [ count , setCount ] = useState(0);
```

1. `useState`这个函数接收的参数是状态的初始值(Initial state)

2. `useState()`其返回一个长度为2的元组，第一项为当前状态，第二项为更新函数。(这里利用es6语法来赋值)



### 多状态声明的注意事项

```js
import React, { useState } from 'react';
function Example2(){
    const [ age , setAge ] = useState(18)
    const [ sex , setSex ] = useState('男')
    const [ work , setWork ] = useState('前端程序员')
    return (
        <div>
            <p>JSPang 今年:{age}岁</p>
            <p>性别:{sex}</p>
            <p>工作是:{work}</p>
            
        </div>
    )
}
export default Example2;
```

**React是根据useState出现的顺序来确定的**

**React Hooks不能出现在条件判断语句中，因为它必须有完全一样的渲染顺序**。



## 3.useEffect代替常用生命周期函数

在用`Class`制作组件时，经常会用生命周期函数，来处理一些额外的事情.

在`React Hooks`中也需要这样类似的生命周期函数，比如在每次状态（State）更新时执行，它为我们准备了`useEffect`。



### 1.用`useEffect`函数来代替生命周期函数

记得要先引入`useEffect`后，才可以正常使用。

```js
import React, { useState , useEffect } from 'react';
function Example(){
    const [ count , setCount ] = useState(0);
    //---关键代码---------start-------
    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)
    })
    //---关键代码---------end-------

    return (
        <div>
            <p>You clicked {count} times</p>
            <button onClick={()=>{setCount(count+1)}}>click me</button>
        </div>
    )
}
export default Example;
```



### 2.useEffect两个注意点

1. **React首次渲染和之后的每次渲染都会调用一遍`useEffect`函数**，而之前我们要用两个生命周期函数分别表示首次渲染(componentDidMonut)和更新导致的重新渲染(componentDidUpdate)。
2. useEffect中定义的函数的执行不会阻碍浏览器更新视图，也就是说这些函数时**异步执行**的，而`componentDidMonut`和`componentDidUpdate`中的代码都是**同步执行**的。个人认为这个有好处也有坏处吧，比如我们要根据页面的大小，然后绘制当前弹出窗口的大小，如果时异步的就不好操作了。









## 4.useEffect 实现 componentWillUnmount生命周期函数

在写React应用的时候，在组件中经常用到`componentWillUnmount`生命周期函数（组件将要被卸载时执行）。比如我们的定时器要清空，避免发生内存泄漏;比如登录状态要取消掉，避免下次进入信息出错。所以这个生命周期函数也是必不可少的。



### useEffect的第二个参数

那到底要如何实现类似`componentWillUnmount`的效果那?这就需要请出`useEffect`的第二个参数，它是一个数组，数组中可以写入很多状态对应的变量，意思是当状态值发生变化时，我们才进行解绑。但是当传空数组`[]`时，就是当组件将被销毁时才进行解绑，这也就实现了`componentWillUnmount`的生命周期函数。

```js
function Index() {
    useEffect(()=>{
        console.log('useEffect=>老弟你来了！Index页面')
        return ()=>{
            console.log('老弟，你走了!Index页面')
        }
    },[])
    return <h2>JSPang.com</h2>;
}
```

在第二个参数数组填上count

```js
 const [ count , setCount ] = useState(0);

    useEffect(()=>{
        console.log(`useEffect=>You clicked ${count} times`)

        return ()=>{
            console.log('====================')
        }
    },[count]) //eslint会自动补全count，当前面有count状态时？
```



这时候只要`count`状态发生变化，都会执行解绑副作用函数，浏览器的控制台也就打印出了一串









## 5.useContext 让父子组件传值更简单

在用类声明组件时，父子组件的传值是通过组件属性和`props`进行的，那现在使用方法(Function)来声明组件，已经没有了`constructor`构造函数也就没有了props的接收，那父子组件的传值就成了一个问题。`React Hooks` 为我们准备了`useContext`。

```js
import React, {
    useState,
    useContext,
    createContext
} from 'react';
const CountContext = createContext()  //用createContext函数创建组件

function Container(){
    let count = useContext(CountContext);
    return (<div>{count}</div>)
}

function App(){
     const [count, setCount] = useState(0);
    return (
        <div>
            <input value={count} onChange={(e)=>{setCount(e.target.value)}}></input>
            <div>
        		<!-- 用<CountContext.Provider>标签包裹组件进行传参 -->
                <CountContext.Provider value={count}>
                    <Container/>
                </CountContext.Provider>
            </div>
        </div>
    )
}

export default App;
```



## 6.useReducer介绍和简单使用

那这节课开始学习一下`useReducer`，因为他们两个很像，并且合作可以完成类似的Redux库的操作。在开发中使用`useReducer`可以让代码具有更好的可读性和可维护性，并且会给测试提供方便。

```js
function countReducer(state, action) {
    switch(action.type) {
        case 'add':
            return state + 1;
        case 'sub':
            return state - 1;
        default: 
            return state;
    }
}
```

上面的代码就是Reducer，你主要理解的就是这种形式和两个参数的作用，一个参数是状态，一个参数是如何控制状态。

### useReducer的使用

```js
import React, { useReducer } from 'react';

function ReducerDemo(){
    const [ count , dispatch ] =useReducer((state,action)=>{
        switch(action){
            case 'add':
                return state+1
            case 'sub':
                return state-1
            default:
                return state
        }
    },0)
    return (
       <div>
           <h2>现在的分数是{count}</h2>
           <button onClick={()=>dispatch('add')}>Increment</button>
           <button onClick={()=>dispatch('sub')}>Decrement</button>
       </div>
    )

}

export default ReducerDemo
```



## 7.useMemo优化React Hooks程序性能

`useMemo`主要用来解决使用React hooks产生的无用渲染的性能问题。使用function的形式来声明组件，失去了`shouldCompnentUpdate`（在组件更新之前）这个生命周期，也就是说我们没有办法通过组件更新前条件来决定组件是否更新。

`useMemo`和`useCallback`都是解决上述性能问题的 



### seMemo 优化性能

其实只要使用`useMemo`，然后给她传递第二个参数，参数匹配成功，才会执行。代码如下：

```js
function ChildComponent({name,children}){
    function changeXiaohong(name){
        console.log('她来了，她来了。小红向我们走来了')
        return name+',小红向我们走来了'
    }

    const actionXiaohong = useMemo(()=>changeXiaohong(name),[name]) 
    return (
        <>
            <div>{actionXiaohong}</div>
            <div>{children}</div>
        </>
    )
}
```



## 8.useRef获取DOM元素和保存变量

`useRef`在工作中虽然用的不多，但是也不能缺少。它有两个主要的作用:

- 用`useRef`获取React JSX中的DOM元素，获取后你就可以控制DOM的任何东西了。但是一般不建议这样来作，React界面的变化可以通过状态来控制。

```js
import React, { useRef ,useState,useEffect } from 'react';
function Example8(){
    const inputEl = useRef(null)
    const onButtonClick=()=>{ 
        inputEl.current.value="Hello ,useRef"
        console.log(inputEl)
    }
    //-----------关键代码--------start
    const [text, setText] = useState('jspang')
    const textRef = useRef()

    useEffect(()=>{
        textRef.current = text;
        console.log('textRef.current:', textRef.current)
    })
    //----------关键代码--------------end
    return (
        <>
            {/*保存input的ref到inputEl */}
            <input ref={inputEl} type="text"/>
            <button onClick = {onButtonClick}>在input上展示文字</button>
            <br/>
            <br/>
            <input value={text} onChange={(e)=>{setText(e.target.value)}} />
        </>
    )
}

export default Example8
```

这时候就可以实现每次状态修改，同时保存到`useRef`中了。也就是我们说的保存变量的功能。那`useRef`的主要功能就是获得DOM和变量保存。



## 9.自定义Hooks函数获取窗口大小

```js
function useWinSize(){
    const [ size , setSize] = useState({
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
    })

    const onResize = useCallback(()=>{
        setSize({
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        })
    },[]) 
    useEffect(()=>{
        window.addEventListener('resize',onResize)
        return ()=>{
            window.removeEventListener('resize',onResize)
        }
    },[])

    return size;

}
```

使用

```js
function Example9(){

    const size = useWinSize()
    return (
        <div>页面Size:{size.width}x{size.height}</div>
    )
}

export default Example9 
```