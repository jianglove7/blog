---
title: React——Redux基础
date: 2019-09-23 09:20:39
tags: 
- React
categories:
- javascript
---
# 1.Redux初识

## 1.为什么需要Redux

单单使用react只能做一些简单的项目?

因为React就是一个简单的轻量级的视图层框架。

React当中的组件通信和状态管理是特别繁琐的，比如子组件和父组件通信改变值，要通过父组件的方法。

需要视图层框架+数据层框架，两个相互结合，就可以实现大型的开发项目了。

### 2.redux官方图片

![Redux简化图](https://jspang.com/images/redux_flow.png)





# 2.使用Redux

## 1.创建store、reducer

安装Redux

```text
npm install --save redux
```

装好`redux`之后，在`src`目录下创建一个`store`文件夹,然后在文件夹下创建一个`index.js`文件。

`index.js`就是整个项目的`store`文件，打开文件，编写下面的代码。

```js
import { createStore } from 'redux'  // 引入createStore方法
const store = createStore()          // 创建数据存储仓库
export default store                 //暴露出去
```

这样虽然已经建立好了仓库，但是这个仓库很混乱，这时候就需要一个有管理能力的模块出现，这就是`Reducers`。这两个一定要一起创建出来，这样仓库才不会出现互怼现象。在`store`文件夹下，新建一个文件`reducer.js`,然后写入下面的代码。

```js
const defaultState = {
    inputValue : 'Write Something',
    list:[
        '早上4点起床，锻炼身体',
        '中午下班游泳一小时'
    ]
}
export default (state = defaultState,action)=>{
    return state
}
```

这样`reducer`就建立好了，把reducer引入到`store`中,再创建store时，以参数的形式传递给store。

```js
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(reducer) // 创建数据存储仓库
export default store   //暴露出去
```



## 2.组件获得`store`中的数据

引入store

```js
import store from './store/index'
import store from './store' //缩写
```



调用state

```js
constructor(props){
    super(props)
    //关键代码-----------start
    this.state=store.getState();
    //关键代码-----------end
    console.log(this.state)
}
```

`getState`是redux实例里面的方法



## 3.修改state

### 1.组件里

```js
changeInputValue(e){
    const action ={
        type:'changeInput',
        value:e.target.value
    }
    store.dispatch(action)
}
```

拼装action参数，并调用store,dispatch方法让store派遣到对应的reducer



### 2.reducer里

```js
export default (state = defaultState,action)=>{
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    return state
}
```

reducer里的两个参数：

- **state**: 指的是原始仓库里的状态（里只能接收state，不能改变state）。
- **action**: 指的是action新传递的状态。

通过打印你可以知道，`Reducer`已经拿到了原来的数据和新传递过来的数据，现在要作的就是改变store里的值。我们先判断`type`是不是正确的，如果正确，我们需要从新声明一个变量`newState`。（**记住：Reducer里只能接收state，不能改变state。**）,所以我们声明了一个新变量，然后再次用`return`返回回去。



## 3.让组件发生更新

现在store里的数据已经更新了，但是组件还没有进行更新，我们需要打开组件文件`TodoList.js`，在`constructor`，写入下面的代码。

```js
constructor(props){
    super(props)
    this.state=store.getState();
    this.changeInputValue= this.changeInputValue.bind(this)
    //----------关键代码-----------start
    this.storeChange = this.storeChange.bind(this)  //转变this指向
    store.subscribe(this.storeChange) //订阅Redux的状态
    //----------关键代码-----------end
}
```

```js
 storeChange(){
     this.setState(store.getState())
 }
```

## 4.总结

redux是通过判断消息类型进行对应操作的模式对state进行修改

```js
export default (state = defaultState,action)=>{
    if(action.type === 'changeInput'){
        let newState = JSON.parse(JSON.stringify(state)) //深度拷贝state
        newState.inputValue = action.value
        return newState
    }
    //关键代码------------------start----------
    //state值只能传递，不能使用
    if(action.type === 'addItem' ){ //根据type值，编写业务逻辑
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.push(newState.inputValue)  //push新的内容到列表中去
        newState.inputValue = ''
        return newState
    }
    
    if(action.type === 'deleteItem' ){ 
        let newState = JSON.parse(JSON.stringify(state)) 
        newState.list.splice(action.index,1)  //删除数组中对应的值
        return newState
    }
    
    
     //关键代码------------------end----------
    return state
}
```





# 3.Redux技巧

## 1.制作映射文件

写`Redux Action`的时候，我们写了很多Action的派发，产生了很多`Action Types`，如果需要`Action`的地方我们就自己命名一个`Type`,会出现两个基本问题：

- 这些Types如果不统一管理，不利于大型项目的服用，设置会长生冗余代码。
- 因为`Action`里的`Type`，一定要和`Reducer`里的`type`一一对应在，所以这部分代码或字母写错后，浏览器里并没有明确的报错，这给调试带来了极大的困难。

那我司中会把`Action Type`单独拆分出一个文件。在`src/store`文件夹下面，新建立一个`actionTypes.js`文件，然后把Type集中放到文件中进行管理。

```js
export const  CHANGE_INPUT = 'changeInput'
export const  ADD_ITEM = 'addItem'
export const  DELETE_ITEM = 'deleteItem'
```



2.把所有的`Redux Action`放到一个文件里进行管理。

## 2.编写`actionCreators.js`文件

### 编写`actionCreators.js`文件

有了文件，就可以把`actionCreatores.js`引入到`TodoLisit`中。

```js
import {changeInputAction} from './store/actionCreatores'
```

引入后，可以把`changeInputValue()`方法，修改为下面的样子。

```js
changeInputValue(e){
        const action = changeInputAction(e.target.value)
        store.dispatch(action)
    }
```

在`/src/store`文件夹下面，建立一个心的文件`actionCreators.js`，先在文件中引入上节课编写`actionTypes.js`文件。

```js
import {CHANGE_INPUT}  from './actionTypes'
```

引入后可以用`const`声明一个`changeInputAction`变量，变量是一个箭头函数，代码如下：

```js
import {CHANGE_INPUT}  from './actionTypes'

export const changeInputAction = (value)=>({
    type:CHANGE_INPUT,
    value
})
```

### 修改todoList中的代码

有了文件，就可以把`actionCreatores.js`引入到`TodoLisit`中。

```js
import {changeInputAction} from './store/actionCreatores'
```

引入后，可以把`changeInputValue()`方法，修改为下面的样子。

```js
changeInputValue(e){
        const action = changeInputAction(e.target.value)
        store.dispatch(action)
    }
```



# 4.Redux的三个坑

- `store`必须是唯一的，多个`store`是坚决不允许，只能有一个`store`空间
- 只有`store`能改变自己的内容，`Reducer`不能改变
- `Reducer`必须是纯函数



### 1.`Store`必须是唯一的

现在看`TodoList.js`的代码，就可以看到，这里有一个`/store/index.js`文件，只在这个文件中用`createStore()`方法，声明了一个store，之后整个应用都在使用这个`store`。 下面我给出了`index.js`内容，可以帮助你更好的回顾。

```js
import { createStore } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
const store = createStore(
    reducer,
    window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()) // 创建数据存储仓库
export default store   //暴露出去
```



### 2.只有`store`能改变自己的内容，`Reducer`不能改变

很多新手小伙伴会认为把业务逻辑写在了`Reducer`中，那改变state值的一定是`Reducer`，其实不然，在Reducer中我们只是作了一个返回，返回到了`store`中，并没有作任何改变。我这个在上边的课程中也着重进行了说明。我们再来复习一下Reducer的代码，来加深印象。

`Reudcer`只是返回了更改的数据，但是并没有更改`store`中的数据，`store`拿到了`Reducer`的数据，自己对自己进行了更新。





### 3.`Reducer`必须是纯函数

先来看什么是纯函数，纯函数定义：

> 如果函数的调用参数相同，则永远返回相同的结果。它不依赖于程序执行期间函数外部任何状态或数据的变化，必须只依赖于其输入参数。

其实你可以简单的理解为返回的结果是由传入的值决定的，而不是其它的东西决定的。比如下面这段`Reducer`代码。







