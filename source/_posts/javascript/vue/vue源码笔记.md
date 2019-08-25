---
title: vue源码笔记
date: 2019-08-25 21:22:11
tags: 
- vue
categories:
- javascript
---

# 一.目录结构

目录结构

```
├── build --------------------------------- 构建相关的文件
├── dist ---------------------------------- 构建后文件的输出目录
├── examples ------------------------------ 存放使用Vue开发的的例子
├── flow ---------------------------------- 类型声明，使用开源项目 [Flow](https://flowtype.org/)
├── package.json -------------------------- 项目依赖
├── test ---------------------------------- 包含所有测试文件
├── src ----------------------------------- 这个是我们最应该关注的目录，包含了源码
│   ├──platforms --------------------------- 包含平台相关的代码
│   │   ├──web -----------------------------  包含了不同构建的包的入口文件
│   │   |   ├──entry-runtime.js ---------------- 运行时构建的入口，输出 dist/vue.common.js 文件，不包含模板(template)到render函数的编译器，所以不支持 `template` 选项，我们使用vue默认导出的就是这个运行时的版本。大家使用的时候要注意
│   │   |   ├── entry-runtime-with-compiler.js -- 独立构建版本的入口，输出 dist/vue.js，它包含模板(template)到render函数的编译器
│   ├── compiler -------------------------- 编译器代码的存放目录，将 template 编译为 render 函数
│   │   ├── parser ------------------------ 存放将模板字符串转换成元素抽象语法树的代码
│   │   ├── codegen ----------------------- 存放从抽象语法树(AST)生成render函数的代码
│   │   ├── optimizer.js ------------------ 分析静态树，优化vdom渲染
│   ├── core ------------------------------ 存放通用的，平台无关的代码
│   │   ├── observer ---------------------- 反应系统，包含数据观测的核心代码
│   │   ├── vdom -------------------------- 包含虚拟DOM创建(creation)和打补丁(patching)的代码
│   │   ├── instance ---------------------- 包含Vue构造函数设计相关的代码
│   │   ├── global-api -------------------- 包含给Vue构造函数挂载全局方法(静态方法)或属性的代码
│   │   ├── components -------------------- 包含抽象出来的通用组件
│   ├── server ---------------------------- 包含服务端渲染(server-side rendering)的相关代码
│   ├── sfc ------------------------------- 包含单文件组件(.vue文件)的解析逻辑，用于vue-template-compiler包
│   ├── shared ---------------------------- 包含整个代码库通用的代码
```



# 二.内容

该章节是从打包文件`vue.runtime.common.dev.js`中查看源码内容。

## 1.入口

从打包文件中看vue源码做了什么：

1.`package.json`文件

里的main选项说明了入口在`dist/vue.runtime.common.js`：

```
"main": "dist/vue.runtime.common.js",
```

dev环境中引用vue实际是引用该文件`vue.runtime.common.dev.js`

## 2.初始化

### 1.消除浏览器差异

为了抹平浏览器之间的差异，做了大量polyfill操作，例如ES6中的Set

```javascript
if (typeof Set !== 'undefined' && isNative(Set)) {
  // use native Set when available.
  _Set = Set;
} else {
  // a non-standard Set polyfill that only works with primitive keys.
  _Set = /*@__PURE__*/(function () {
    function Set () {
      this.set = Object.create(null);
    }
    Set.prototype.has = function has (key) {
      return this.set[key] === true
    };
    Set.prototype.add = function add (key) {
      this.set[key] = true;
    };
    Set.prototype.clear = function clear () {
      this.set = Object.create(null);
    };

    return Set;
  }());
}
...
```



### 2. 通用函数

vue中定义了大量通用函数，如下只是一部分，平常需要找一些通用函数也可以在这里找到例子

```javascript
function isUndef (v) {
  return v === undefined || v === null
}

function isDef (v) {
  return v !== undefined && v !== null
}

function isTrue (v) {
  return v === true
}

function isFalse (v) {
  return v === false
}
```

### 3.浏览器嗅探

```javascript
var inBrowser = typeof window !== 'undefined';
var inWeex = typeof WXEnvironment !== 'undefined' && !!WXEnvironment.platform;
var weexPlatform = inWeex && WXEnvironment.platform.toLowerCase();
var UA = inBrowser && window.navigator.userAgent.toLowerCase();
var isIE = UA && /msie|trident/.test(UA);
var isIE9 = UA && UA.indexOf('msie 9.0') > 0;
var isEdge = UA && UA.indexOf('edge/') > 0;
var isAndroid = (UA && UA.indexOf('android') > 0) || (weexPlatform === 'android');
var isIOS = (UA && /iphone|ipad|ipod|ios/.test(UA)) || (weexPlatform === 'ios');
var isChrome = UA && /chrome\/\d+/.test(UA) && !isEdge;
var isPhantomJS = UA && /phantomjs/.test(UA);
var isFF = UA && UA.match(/firefox\/(\d+)/);
```

### 4.常量

定义常量，资源类型，生命周期钩子等：

```
var SSR_ATTR = 'data-server-rendered';

var ASSET_TYPES = [
  'component',
  'directive',
  'filter'
];

var LIFECYCLE_HOOKS = [
  'beforeCreate',
  'created',
  'beforeMount',
  'mounted',
  'beforeUpdate',
  'updated',
  'beforeDestroy',
  'destroyed',
  'activated',
  'deactivated',
  'errorCaptured',
  'serverPrefetch'
];
```

### 5.服务器渲染

vue加载模式分为是否服务端渲染，从`process.env.VUE_ENV`区分





## 3.Vue构造函数

### 1.定义构造函数

1. 构造函数的定义，默认调用实例方法`_init`

```javascript
function Vue (options) {
  if (!(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword');
  }
  this._init(options);
}
```

**注意：创建实例的时候，调用`this._init(options)`才是真正的开始。。。**



2. 给构造函数插入各种实例方法：

```javascript
initMixin(Vue);   //初始化相关，beforeCreate，created钩子在这里体现
stateMixin(Vue);	//状态相关实例方法定义
eventsMixin(Vue);	
lifecycleMixin(Vue);
renderMixin(Vue);
//...
initGlobalAPI(Vue);

```



### 2.`initMixin`函数

作用：

- 添加内部调用方法`_init`



**`_init`方法：**

```javascript
  Vue.prototype._init = function (options) {
      //动态组件优化 initInternalComponent
      //用proxy代理事件
      ...
      
      //设置好各个实例方法，生命周期、事件、渲染
      //注意钩子触发位置
    vm._self = vm;
    initLifecycle(vm);
    initEvents(vm);
    initRender(vm);
    callHook(vm, 'beforeCreate');
    initInjections(vm); // resolve injections before data/props
    initState(vm);
    initProvide(vm); // resolve provide after data/props
    callHook(vm, 'created');
    
    //进入挂载流程
    if (vm.$options.el) {
      vm.$mount(vm.$options.el);
    }
  }
```

注意钩子触发位置：

1. 先注册生命周期，事件，`initRender`创建节点

2. 执行`beforeCreate`钩子

3. 将选项注册为实例方法，实例属性，用`observe`将data里的属性注册到观察者模式

4. 执行`created`钩子

5. 进入挂载流程`vm.$mount(vm.$options.el);`



### 3.`stateMixin`函数

添加属性相关实例属性、实例方法

- `$data`属性
- `$props`属性
- `$set`方法
- `$delete`方法
- `$watch`方法：将属性加入观察者模式



定义属性相关的实例方法：

```javascript
  Vue.prototype.$set = set;
  Vue.prototype.$delete = del;

//观察者模式的触发器
  Vue.prototype.$watch = function (
    expOrFn,
    cb,
    options
  ) {
  ...
  }
```



### 3.`eventsMixin`函数

1.添加事件相关实例函数：

- `$on`注册事件
- `$once`注册只使用一次事件的方法
- `$off`注销事件方法
- `$emit`触发事件方法



2.事件注册，实质是在on指令用`hook:event`的格式注册到观察者模式中；

```javascript
var hookRE = /^hook:/;
Vue.prototype.$on = function (event, fn) {
    //...省略数组式添加事件
    (vm._events[event] || (vm._events[event] = [])).push(fn);
      if (hookRE.test(event)) {
        vm._hasHookEvent = true;
      }
}
```







### 4.`lifecycleMixin`函数

生命周期混入，主要是更新，销毁；`create`，`mount`是在init实例方法里触发。

1.添加实例函数

- `_update`更新（内部使用）
- `$forceUpdate`
- `$destroy`

```javascript
 Vue.prototype.$forceUpdate = function () {
    var vm = this;
    if (vm._watcher) {
      vm._watcher.update();
    }
  };

  Vue.prototype.$destroy = function () {
      
  }
```



### 5.`renderMixin`函数

1.添加渲染辅助实例函数

- `$nextTick `
- `_render` 渲染（内部使用）

2.添加`vm.VNode`属性

3.给`vnode.parent`赋值确定组件的父子层级



### 6.`initGlobalAPI`函数

给Vue构造函数定义静态方法、属性：

```javascript
function initGlobalAPI (Vue) {
  // config
  var configDef = {};
  configDef.get = function () { return config; };
  {
    configDef.set = function () {
      ...
    };
  }
  Object.defineProperty(Vue, 'config', configDef);
  //工具方法
  Vue.util = {
    warn: warn,
    extend: extend,
    mergeOptions: mergeOptions,
    defineReactive: defineReactive$$1
  };
	//设置属性，删除属性，nextTick
  Vue.set = set;
  Vue.delete = del;
  Vue.nextTick = nextTick;
	
  //添加对象到observe
  Vue.observable = function (obj) {
    observe(obj);
    return obj
  };
	
  Vue.options = Object.create(null);
  ASSET_TYPES.forEach(function (type) {
    Vue.options[type + 's'] = Object.create(null);
  });
	
  Vue.options._base = Vue;

  extend(Vue.options.components, builtInComponents);

  initUse(Vue);  //添加use方法
  initMixin$1(Vue);	//添加全局mixin方法
  initExtend(Vue);	//全局继承
  initAssetRegisters(Vue);	//全局资源方法：components、directives、filters注册方法
}
```





### 7.`$mount`实例方法

在initMixin函数中，`_init`实例方法中用到的`$mount`实例方法，是进入挂载模板的入口:

```javascript
//进入挂载流程
if (vm.$options.el) {
	vm.$mount(vm.$options.el);
}
```

```
// public mount method
Vue.prototype.$mount = function (
  el,
  hydrating
) {
  el = el && inBrowser ? query(el) : undefined;
  return mountComponent(this, el, hydrating)
};
```

最终找到函数`mountComponent`

```javascript
function mountComponent (
  vm,
  el,
  hydrating
) {
    //1.检查render选项、template选项、el选项看是否有可用的模板
    //...
    callHook(vm, 'beforeMount');
   	//...
      
    //2.定义updateComponent方法
    updateComponent = function () {
      vm._update(vm._render(), hydrating);
    };
      
    //3.vm注册到观察者模式中
    new Watcher(vm, updateComponent, noop, {
        before: function before () {
            if (vm._isMounted && !vm._isDestroyed) {
                callHook(vm, 'beforeUpdate');
            }
        }
    }, true /* isRenderWatcher */);
      
    if (vm.$vnode == null) {
        vm._isMounted = true;
        callHook(vm, 'mounted');
     }
  }
```

所以，模板的渲染和更新是靠观察者模式触发的



# 三.Vue中的双向数据绑定

Vue实例为它的每一个data都实现了`getter/setter`方法，这是实现响应式的基础。关于`getter/setter`可查看[MDN web docs](https://link.juejin.im?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FFunctions%2Fget)。 简单来说，就是在取值`this.counter`的时候，可以自定义一些操作，再返回counter的值；在修改值`this.counter = 10`的时候，也可以在设置值的时候自定义一些操作。`initData(vm)`的实现在源码中的[`instance/state.js`](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fvuejs%2Fvue%2Fblob%2Fdev%2Fsrc%2Fcore%2Finstance%2Fstate.js%23L110)。



## 订阅中心Dep

```javascript
Dep.prototype.addSub = function addSub (sub) {
  this.subs.push(sub);
};

Dep.prototype.removeSub = function removeSub (sub) {
  remove(this.subs, sub);
};
//getter中使用 收集依赖
Dep.prototype.depend = function depend () {
  if (Dep.target) {
    Dep.target.addDep(this);
  }
};
//setter中使用 通知更新
Dep.prototype.notify = function notify () {
  // stabilize the subscriber list first
  var subs = this.subs.slice();
  if (!config.async) {
    // subs aren't sorted in scheduler if not running async
    // we need to sort them now to make sure they fire in correct
    // order
    subs.sort(function (a, b) { return a.id - b.id; });
  }
  for (var i = 0, l = subs.length; i < l; i++) {
    subs[i].update();
  }
};
```





## 触发器Observer

Observer Class将每个目标对象的键值（即data中的数据）转换成`getter/setter`形式，用于进行依赖收集和通过依赖通知更新。

```javascript
var Observer = function Observer (value) {
  this.value = value;
  this.dep = new Dep();
  this.vmCount = 0;
  def(value, '__ob__', this);
  if (Array.isArray(value)) {
    if (hasProto) {
      protoAugment(value, arrayMethods);
    } else {
      copyAugment(value, arrayMethods, arrayKeys);
    }
    this.observeArray(value);
  } else {
    this.walk(value);
  }
};


```

继续看`walk()`方法，注释中已说明`walk()`做的是遍历data对象中的每一设置的数据，将其转为`setter/getter`。

```javascript
/**
 * Walk through all properties and convert them into
 * getter/setters. This method should only be called when
 * value type is Object.
 */
Observer.prototype.walk = function walk (obj) {
  var keys = Object.keys(obj);
  for (var i = 0; i < keys.length; i++) {
    defineReactive$$1(obj, keys[i]);
  }
};

```

那么最终将对应数据转为`getter/setter`的方法就是`defineReactive()`方法。从方法命名上也容易知道该方法是定义为可响应的，结合最开始的例子，这里调用就是`defineReactive(...)`如图所示：

```javascript
/**
 * Define a reactive property on an Object.
 */
function defineReactive$$1 (
  obj,
  key,
  val,
  customSetter,
  shallow
) {
  var dep = new Dep();

  var property = Object.getOwnPropertyDescriptor(obj, key);
  if (property && property.configurable === false) {
    return
  }

  // cater for pre-defined getter/setters
  var getter = property && property.get;
  var setter = property && property.set;
  if ((!getter || setter) && arguments.length === 2) {
    val = obj[key];
  }

  var childOb = !shallow && observe(val);
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      var value = getter ? getter.call(obj) : val;
      if (Dep.target) {
        dep.depend();
        if (childOb) {
          childOb.dep.depend();
          if (Array.isArray(value)) {
            dependArray(value);
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      var value = getter ? getter.call(obj) : val;
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      /* eslint-enable no-self-compare */
      if (customSetter) {
        customSetter();
      }
      // #7981: for accessor properties without setter
      if (getter && !setter) { return }
      if (setter) {
        setter.call(obj, newVal);
      } else {
        val = newVal;
      }
      childOb = !shallow && observe(newVal);
      dep.notify();
    }
  });
}
```





## 监听器watcher

```javascript
var uid$2 = 0;

/**
 * A watcher parses an expression, collects dependencies,
 * and fires callback when the expression value changes.
 * This is used for both the $watch() api and directives.
 */
var Watcher = function Watcher (
  vm,
  expOrFn,
  cb,
  options,
  isRenderWatcher
) {
  this.vm = vm;
  if (isRenderWatcher) {
    vm._watcher = this;
  }
  vm._watchers.push(this);
  // options
  if (options) {
    this.deep = !!options.deep;
    this.user = !!options.user;
    this.lazy = !!options.lazy;
    this.sync = !!options.sync;
    this.before = options.before;
  } else {
    this.deep = this.user = this.lazy = this.sync = false;
  }
  this.cb = cb;
  this.id = ++uid$2; // uid for batching
  this.active = true;
  this.dirty = this.lazy; // for lazy watchers
  this.deps = [];
  this.newDeps = [];
  this.depIds = new _Set();
  this.newDepIds = new _Set();
  this.expression = expOrFn.toString();
  // parse expression for getter
  if (typeof expOrFn === 'function') {
    this.getter = expOrFn;
  } else {
    this.getter = parsePath(expOrFn);
    if (!this.getter) {
      this.getter = noop;
      warn(
        "Failed watching path: \"" + expOrFn + "\" " +
        'Watcher only accepts simple dot-delimited paths. ' +
        'For full control, use a function instead.',
        vm
      );
    }
  }
  this.value = this.lazy
    ? undefined
    : this.get();
};
```



## 总结

1.监听器Watcher里{对象，键值，回调函数}这样一个数据结构的实例通过实例方法`Watcher.prototype.addDep`添加到订阅中心Dep，记录在里面的静态属性subs里。

2.触发器Observer负责在触发getter/setter时候添加依赖depend/发送通知通知noticy

3.订阅中心负责处理粗发器发过来的信息（添加依赖depend/发送通知通知noticy）循环调用静态属性subs里的watcher实例，符合的实例会调用对应的回调函数



下图来自官方：![data](https://cn.vuejs.org/images/data.png)

这是触发组件更新的图例，省略了Dep部分





# 四.属性说明

## 1.选项

### el

可以是query函数能解析的字符串/HTMLElement 实例

提供一个在页面上已存在的 DOM 元素作为 Vue 实例的挂载目标。可以是 CSS 选择器，也可以是一个 HTMLElement 实例。

在实例挂载之后，元素可以用 `vm.$el` 访问。

## 2.实例属性

### 1.$data

Vue 实例观察的数据对象。Vue 实例代理了对其 data 对象属性的访问。

### 2.$props

当前组件接收到的 props 对象。Vue 实例代理了对其 props 对象属性的访问。

### 3.$el

Vue 实例使用的根 DOM 元素。

### 4.$options

vue实例的属性初始化后的配置集合

```
vm.$options = mergeOptions(
    resolveConstructorOptions(vm.constructor),
    options || {},
    vm
);
```

### 5.$root

当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。

### 6.$slots

用来访问被[插槽分发](https://cn.vuejs.org/v2/guide/components.html#通过插槽分发内容)的内容。每个[具名插槽](https://cn.vuejs.org/v2/guide/components-slots.html#具名插槽) 有其相应的属性 (例如：`v-slot:foo` 中的内容将会在 `vm.$slots.foo` 中被找到)。`default` 属性包括了所有没有被包含在具名插槽中的节点，或 `v-slot:default` 的内容。

### 7.$scopedSlots

1. 作用域插槽函数现在保证返回一个 VNode 数组，除非在返回值无效的情况下返回 `undefined`。
2. 所有的 `$slots` 现在都会作为函数暴露在 `$scopedSlots` 中。如果你在使用渲染函数，不论当前插槽是否带有作用域，我们都推荐始终通过 `$scopedSlots` 访问它们。这不仅仅使得在未来添加作用域变得简单，也可以让你最终轻松迁移到所有插槽都是函数的 Vue 3。

### 8.$attrs

包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (`class` 和 `style` 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

### 9.$listeners

包含了父作用域中的 (不含 `.native` 修饰器的) `v-on` 事件监听器。它可以通过 `v-on="$listeners"` 传入内部组件——在创建更高层次的组件时非常有用。









