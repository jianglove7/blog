---
title: React——进阶
date: 2019-09-24 09:20:39
tags: 
- React
categories:
- javascript
---
# 1.组件UI和业务逻辑的拆分方法

将ui组件打包成业务组件，并暴露需要修改的参数

```js
render() { 
    return ( 
        <TodoListUI 
            inputValue={this.state.inputValue}
            list={this.state.list}
            changeInputValue={this.changeInputValue}
            clickBtn={this.clickBtn}
            deleteItem={this.deleteItem}
        />
    );
}
```





# 2.无状态组件

无状态组件其实就是一个函数，它不用再继承任何的类（class），当然如名字所一样，也不存在`state`（状态）。因为无状态组件其实就是一个函数（方法）,所以它的性能也比普通的`React`组件要好。



### 无状态组件的改写

把UI组件改成无状态组件可以提高程序性能，具体来看一下如何编写。

1. 首先我们不在需要引入React中的`{ Component }`，删除就好。
2. 然后些一个`TodoListUI`函数,里边只返回`JSX`的部分就好，这步可以复制。
3. 函数传递一个`props`参数，之后修改里边的所有`props`，去掉`this`。

这里给出最后修改好以后的无状态组件代码，这样的效率要高于以前写的普通`react`组件。

```js
import React from 'react';
import 'antd/dist/antd.css'
import { Input , Button , List } from 'antd'

const TodoListUi = (props)=>{
    return(
        <div style={{margin:'10px'}}>
            <div>
                <Input  
                    style={{ width:'250px', marginRight:'10px'}}
                    onChange={props.changeInputValue}
                    value={props.inputValue}
                />
                <Button 
                    type="primary"
                    onClick={props.clickBtn}
                >增加</Button>
            </div>
            <div style={{margin:'10px',width:'300px'}}>
                <List
                    bordered
                    dataSource={props.list}
                    renderItem={
                        (item,index)=>(
                            <List.Item onClick={()=>{props.deleteItem(index)}}>
                                {item}
                            </List.Item>
                        )
                    }
                />    
            </div>
        </div>
    )
   
}

export default TodoListUi;
```



总结:这节课主要学习了`React`中的无状态组件，如果是以前没有`Redux`的时候，实现分离是比较困难的，但是现在我们作项目，一定想着找个组件是否可以作成无状态组件。如果能做成无状态组件就尽量作成无状态组件，毕竟性能要高很多





# 3.Axios异步获取数据并和Redux结合

总结：将异步部分写在组件部分，在then里面进行store.dispatch()





# 4.Redux-thunk中间件

1. `Redux-thunk`这个Redux最常用的插件。

2. 状态管理中心，承接业务部分的功能就可以封装在这里。

3. 和vuex中的action意义一样。



### 1.安装`Redux-thunk`组件

```text
npm install --save redux-thunk
```

### 2.配置`Redux-thunk`组件

1.引入`applyMiddleware`,如果你要使用中间件，就必须在redux中引入`applyMiddleware`.

```js
import { createStore , applyMiddleware } from 'redux' 
```

2.引入`redux-thunk`库

```js
import thunk from 'redux-thunk'
```



如果你按照官方文档来写，你直接把thunk放到`createStore`里的第二个参数就可以了，但以前我们配置了`Redux Dev Tools`，已经占用了第二个参数。

这样写是完全没有问题的，但是我们的`Redux Dev Tools`插件就不能使用了，如果想两个同时使用，需要使用**增强函数**。使用增加函数前需要先引入`compose`。

```js
import { createStore , applyMiddleware ,compose } from 'redux' 
```

```js
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'    
import thunk from 'redux-thunk'

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose

const enhancer = composeEnhancers(applyMiddleware(thunk))

const store = createStore( reducer, enhancer) // 创建数据存储仓库
export default store   //暴露出去
```





### 3.在`actionCreators.js`里编写业务逻辑

```js
export const getTodoList = () =>{
    return (dispatch)=>{
        axios.get('https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList').then((res)=>{
            const data = res.data
            const action = getListAction(data)
            dispatch(action)
            
        })
    }
}
```

这个函数可以直接传递`dispatch`进去，这是自动的，然后我们直接用`dispatch(action)`传递就好了。现在我们就可以打开浏览器进行测试了。





# 5.Redux-saga

和Redux-thunk类似的中间件

### redux-saga的安装

```text
npm install --save redux-saga
```

这里给出github地址，方便你更好的学习。

> https://github.com/redux-saga/redux-saga

### 引入并创建`Redux-saga`

```js
import { createStore , applyMiddleware ,compose } from 'redux'  //  引入createStore方法
import reducer from './reducer'   
import createSagaMiddleware from 'redux-saga' 
import mySagas from './sagas' 

const sagaMiddleware = createSagaMiddleware();

const composeEnhancers =   window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}):compose
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware))
const store = createStore( reducer, enhancer) 
sagaMiddleware.run(mySagas)


export default store  
```







# 6.React-Redux

`React-Redux`这是一个React生态中常用组件，它可以简化`Redux`流程

```js
npm install --save react-redux
```



## 1.获取数据

### `<Provider>`提供器讲解



`<Provider>`是一个提供器，只要使用了这个组件，组件里边的其它所有组件都可以使用`store`了，这也是`React-redux`的核心组件了。有了`<Provider>`就可以把`/src/index.js`改写成下面的代码样式，具体解释在视频中介绍了。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import TodoList from './TodoList'
//---------关键代码--------start
import { Provider } from 'react-redux'
import store from './store'
//声明一个App组件，然后这个组件用Provider进行包裹。
const App = (
   <Provider store={store}>
       <TodoList />
   </Provider>
)
//---------关键代码--------end
ReactDOM.render(App, document.getElementById('root'));
```

写完这个，我们再去浏览器中进行查看，发现代码也是可以完美运行的。需要注意的是，现在还是用传统方法获取的`store`中的数据。有了`Provider`再获取数据就没有那么麻烦了。



### `connect`连接器的使用

现在如何简单的获取`store`中数据那？先打开`TodoList.js`文件，引入`connect`，它是一个连接器（其实它就是一个方法），有了这个连接器就可以很容易的获得数据了。

### 映射关系的制作

映射关系就是把原来的state映射成组件中的`props`属性，比如我们想映射`inputValue`就可以写成如下代码。

```js
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}
```

这时候再把`xxx`改为`stateToProps`

```js
export default connect(stateToProps,null)(TodoList)
```

js文件的所有代码。

```js
import React, { Component } from 'react';
import store from './store'
import {connect} from 'react-redux'

class TodoList extends Component {
    constructor(props){
        super(props)
        this.state = store.getState()
    }
    render() { 
        return (
            <div>
                <div>
                    <input value={this.props.inputValue} />
                    <button>提交</button>
                </div>
                <ul>
                    <li>JSPang</li>
                </ul>
            </div>
            );
    }
}

const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}
 
export default connect(stateToProps,null)(TodoList);
```



## 2.修改数据

### 编写`DispatchToProps`

要使用`react-redux`，我们可以编写另一个映射`DispatchToProps`,先看下面这段代码，你会发现有两个参数，第二个参数我们用的是`null`。

```js
export default connect(stateToProps,null)(TodoList);
```

`DispatchToProps`就是要传递的第二个参数，通过这个参数才能改变`store`中的值。

```js
import React, { Component } from 'react';
import store from './store'
import {connect} from 'react-redux'

class TodoList extends Component {
    constructor(props){
        super(props)
        this.state = store.getState()
    }
    render() { 
        return (
            <div>
                <div>
                    <input value={this.props.inputValue} onChange={this.props.inputChange} />
                    <button>提交</button>
                </div>
                <ul>
                    <li>JSPang</li>
                </ul>
            </div>
            );
    }
}
const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue
    }
}

const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        }
    }
}
 
export default connect(stateToProps,dispatchToProps)(TodoList);
```



# 7.React-redux程序优化

```js
import React from 'react';
import {connect} from 'react-redux'


const TodoList =(props)=>{ //1.无状态组件
    let {inputValue ,inputChange,clickButton,list} = props; // 2.结构
    return (
        <div>
            <div>
                <input value={inputValue} onChange={inputChange} />
                <button onClick={clickButton}>提交</button>
            </div>
            <ul>
                {
                    list.map((item,index)=>{
                        return (<li key={index}>{item}</li>)
                    })
                }
            </ul>
        </div>
    );
}



const stateToProps = (state)=>{
    return {
        inputValue : state.inputValue,
        list:state.list
    }
}

const dispatchToProps = (dispatch) =>{
    return {
        inputChange(e){
            let action = {
                type:'change_input',
                value:e.target.value
            }
            dispatch(action)
        },
        clickButton(){
            let action = {
                type:'add_item'
            }
            dispatch(action)
        }
    }
}
export default connect(stateToProps,dispatchToProps)(TodoList); //3.映射
```