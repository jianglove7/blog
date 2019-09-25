---
title: React——React Hooks笔记
date: 2019-09-25 18:20:39
tags: 
- React
categories:
- javascript
---

>总结：用`react-router-dom`组织路由是由`<Link>`、`<Route>`两部分组成
>
>`<Link>`：负责链接跳转
>
>`Route`：负责匹配对应url并输出对应的组件
>
>生命周期`componentDidMount`中可以用`this.props.match`获得路由的状态
>
>

# 1.安装

```text
npm install --save react-router-dom
```





# 2.简单应用

## 1.配置及使用

### 1.编写Index组件

先在`/src`目录下建立一个文件夹，我这里起名叫做`Pages`（你可以起任何名字），然后建立一个组件文件`Index.js`。这里边我们就完全安装工作中的模式来写，只是没有什么业务逻辑，UI也制作的相当加简单。代码如下：

```js
import React, { Component } from 'react';

class Index extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return (  <h2>JSPang.com</h2> );
    }
}
 
export default Index;
```

### 2.编写List组件

编写完`Index`组件以后，继续编写`List`组件。其实这个组件和`Index`基本一样。代码如下:

```js
import React, { Component } from 'react';

class List extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return (  <h2>List Page</h2> );
    }
}
 
export default List;
```

### 3.修改`AppRouter.js`文件

两个组件制作完成后，我们把它引入路由配置文件，然后进行路由的配置就可以了，代码如下：

```js
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";
import Index from './Pages/Index'
import List from './Pages/List'

function AppRouter() {
  return (
    <Router>
        <ul>
            <li> <Link to="/">首页</Link> </li>
            <li><Link to="/list/">列表</Link> </li>
        </ul>
        <Route path="/" exact component={Index} />
        <Route path="/list/" component={List} />
    </Router>
  );
}

export default AppRouter;
```



### 4.`exact`精准匹配的意思

就是你的路径信息要完全匹配成功，才可以实现跳转，匹配一部分是不行的。





### 5.在Route上设置允许动态传值
**1.在Route上设置允许动态传值**

```js
<Route path="/list/:id" component={List} />
```

**2.Link上传递值**

```js
<li><Link to="/list/123">列表</Link> </li>
```

**3.在List组件上接收并显示传递值**

组件接收传递过来的值的时候，可以在生命周期`componentDidMount`中进行，传递的值在`this.props.match`中。我们可以先打印出来,代码如下。

```js
import React, { Component } from 'react';

class List extends Component {
    constructor(props) {
        super(props);
        this.state = {  }
    }
    render() { 
        return (  <h2>List Page</h2> );
    }
    //-关键代码---------start
    componentDidMount(){
        console.log(this.props.match)
    }
    //-关键艾玛---------end
}
 
export default List;
```

然后在浏览器的控制台中可以看到打印出的对象，对象包括三个部分:

- patch:自己定义的路由规则，可以清楚的看到是可以产地id参数的。
- url: 真实的访问路径，这上面可以清楚的看到传递过来的参数是什么。
- params：传递过来的参数，`key`和`value`值。





## 2.ReactRouter重定向-Redirect使用

- 标签式重定向:就是利用`<Redirect>`标签来进行重定向，业务逻辑不复杂时建议使用这种。
- 编程式重定向:这种是利用编程的方式，一般用于业务逻辑当中，比如登录成功挑战到会员中心页面。

重定向和跳转有一个重要的区别，就是跳转式可以用浏览器的回退按钮返回上一级的，而重定向是不可以的。

### 标签式重定向

1.先引入`Redirect`

```js
import { Link , Redirect } from "react-router-dom";
```

2.引入`Redirect`后，直接在`render`函数里使用就可以了。

```js
 <Redirect to="/home/" />
```

### 编程式重定向





## 3.ReactRouter嵌套路由

路由只在页面里发生`<Route>`标签里生效，所以允许多级路由的嵌套





## 4.后台动态获取路由进行配置

### 1.模拟后台得到的JSON数据

我们现在`AppRouter.js`文件里，模拟从后台得到了JSON字符串，并转换为了对象（我们只是模拟，就不真的去远端请求数据了）。模拟的代码如下:

```js
let routeConfig =[
    {path:'/',title:'博客首页',exact:true,component:Index},
    {path:'/video/',title:'视频教程',exact:false,component:Video},
    {path:'/workplace/',title:'职场技能',exact:false,component:Workplace}
]
```

### 2.循环出Link区域

这时候一级导航就不能是写死了，需要根据得到的数据进行循环出来。直接使用`map`循环就可以。代码如下：

```js
<ul>
    {
        routeConfig.map((item,index)=>{
            return (<li key={index}> <Link to={item.path}>{item.title}</Link> </li>)
        })
    }
</ul>
```

### 3.循环出路由配置

按照上面的逻辑把`Route`的配置循环出来。代码如下:

```js
{
    routeConfig.map((item,index)=>{
        return (<Route key={index} exact={item.exact} path={item.path}  component={item.component} />)
    })
}
```



### 4.完整代码

```js
import React from "react";
import { BrowserRouter as Router, Route, Link  } from "react-router-dom";
import Index from './Pages/Index'
import Video from './Pages/Video'
import Workplace from './Pages/Workplace'
import './index.css'

function AppRouter() {
    let routeConfig =[
      {path:'/',title:'博客首页',exact:true,component:Index},
      {path:'/video/',title:'视频教程',exact:false,component:Video},
      {path:'/workplace/',title:'职场技能',exact:false,component:Workplace}
    ]
    return (
      <Router>
          <div className="mainDiv">
            <div className="leftNav">
                <h3>一级导航</h3>
                <ul>
                    {
                      routeConfig.map((item,index)=>{
                          return (<li key={index}> <Link to={item.path}>{item.title}</Link> </li>)
                      })
                    }
                </ul>
            </div>
            
            <div className="rightMain">
                    {
                      routeConfig.map((item,index)=>{
                          return (<Route key={index} exact={item.exact} path={item.path}  component={item.component} />)
                      })
                    }
                
            </div>
          </div>
      </Router>
    );
  }
  
  
  export default AppRouter;
```