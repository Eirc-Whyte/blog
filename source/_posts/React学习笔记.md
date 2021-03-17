---
title: React学习笔记
date: 2021-01-22 17:00:51
tags:
- react 
- 前端
categories:
- 前端学习
---

### React 中的响应式设计思想

只需要关注数据变化，而不用关注dom变化；

react自动感知数据变化，自动修改dom（自动**响应**（React））；

数据需要定义在组件的状态中；

<!--more-->

#### 数据绑定

组件`state`中变量与`input`标签的`value`绑定

双向绑定 = 单向绑定 + UI事件监听

#### 事件绑定

`onChange={this.handler}`

事件`e.target`属性代表绑定的dom

`handler`的本身`this`是`undefined`（因为直接调用了该函数），可以用`.bind(this)`在constructor中绑定本组件的`this`以节省性能

`this.state`需要使用`this.setState`传入对象修改才会将修改更新到dom

#### 视图层框架

只负责数据和页面渲染，需要配合数据层框架来解决传值问题，因此react定位为视图层框架

#### props，state，render

组件的props/state发生改变时，render会重新执行

父组件执行render，子组件render也会被执行

### 组件

#### 意义

页面大且复杂，拆成小的部分逻辑更简单

#### 拆分

组件过大时进行拆分

**父组件向子组件传值：**

- 直接传递props

**子组件向父组件传值：**

- 父组件向子组件传递方法

- 子组件进行调用

- 需要绑定函数this指向父组件

#### 单向数据流

子组件**仅使用而不能直接修改**父组件中数据，方便定位数据调试

#### PropTypes&DefaultProps

- PropTypes用于校验从父组件传入的值的类型

- defaultProps用于设置组件默认props

### Virtual DOM

VDOM：用JS对象描述的真实DOM

根据VDOM生成真实DOM

state发生变化时生成新的VDOM

比较新旧VDOM的区别，找到发生变化的内容，**操作JS对象之间效率比直接操作对比真实DOM高得多**

直接操作DOM，更新span中内容

`JSX -> JS obj -> real DOM`

createElement(标签，属性，内容)

#### diff算法

- setState是异步函数，连续调用会合成一个diff并更新

- 同层比对，只要发现不同即重新渲染整个子树，减少算法比对性能消耗

- 通过**选取前后不变的key**可以增加重新渲染效率，**不要用index做key**，因为重新渲染会改变原本index

### ref的使用

`<input ref={(input)=>{this.input = input}}/>`

相当于在组件中添加input成员作为input标签的引用，可以替代`e.target`

回调函数——**函数执行完毕返回时调用的函数**

### React生命周期函数

![image-20210124185901599](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210124185901599.png)

生命周期函数指在某一特定时刻**组件**自动调用的函数。

- `componentWillMount()`：在组件挂载到页面前执行

- `componentDidMount()`：在组件挂载到页面之后执行，**where commonly placed ajax**

- `componentWillReceiveProps()`：在组件接受到父组件的props，且父组件重新执行render

- `shouldComponentUpdate(nextProps,nextState)`：参数中包含新的props和states，通过设置返回false or true来控制子组件是否需要随父组件更新

### Redux 

#### 思想

Store负责管理、分配组件需要使用的数据

Redux = **Red**ucer + Fl**ux**

#### workflow

<img src="https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210125115243186.png" alt="image-20210125115243186" style="zoom:50%;" />

**dispatcher通过将不同的action传入reducer，reducer根据oldState返回给store新的state，store进行数据更新**

```
 // store/index.js
 // 创建store并绑定对应的reducer
 store = createStore(reducer);

 // store/reducer.js
  reducer(state,action){
  	if(action.type === 'xxx')
  	// 不可以修改state 只能使用state的值
  	// 复制一份 newState = JSON.parse(JSON.stringify(state));
  	return newState;
  
  }
  // in component
  constructor(props){
  	super(props);
  	...
  	store.subscribe(this.handleStoreChange);
  	// 订阅
  }
  handleInputChange = (e) =>{
  	  const action = {
        type=''; // 必须 可以拆分到store/actionTypes.js方便调试
        value=''; // 非必须
      }
      store.dispatch(action);
      // 用action更新store
  }
  handleStoreChange = (e)=>{
  	// 订阅之后state发生变化时将自动执行
  	this.setState(store.getState());
  	// 重新从store中取数据替换组件当前数据
  }
```

  

- 使用actionCreator统一管理创建action:

  ```
  // store/actionCreators.js
  getInputChangeAction = (value)=>{
  	type: CHANGE_INPUT_VALUE,
  	value
  }
  // in component
  handler(e) {
  	store.dispatch(getInputChangeAction(e.target.value));
  }
  ```

#### Redux原则：

- store是唯一的
- 只能store自己改变自己的内容

- reducer必须是**纯函数**（有固定输入就有固定输出，且不会有side effect）

### UI组件、容器组件、无状态组件

UI组件处理页面渲染，容器组件处理页面逻辑

无状态组件性能高，仅通过一个包含props的函数就可生成

UI组件可以通过无状态组件实现

### React-thunk中间件

#### 中间件

<img src="https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210128141737766.png" alt="image-20210128141737766" style="zoom:33%;" />

#### 方式

异步请求移到ActionCreator

创建Store时使用applyMiddleware

```
createStore(reducer, applyMiddleware(thunk));
```

使用时

```javascript
//store/actionCreators.js
const initial = ()=>{
	return (dispatch)=>{
		axio.get(...).then((res)=>{
			const data = res.data;
			const action = ...
			dispatch(action);
		})
	}
}
//index.js
componentDidMount(){
    const init = initial();
    store.dispatch(init);
}
```

- 只有import了thunk，`store.dispatch()`的参数才可以是函数，并且自动将dispatch函数传入作为参数

- 相当于把store.dispatch和参数action之间加了一层**中间层**，对store的dispatch方法进行了封装

### React-saga中间件

#### 方法

`takeEvery("ACTION_TYPE",Generator)`：监听到action，执行对应的函数

`put`：相当于store.dispatch

- generator处理axio失败情况：`try catch`捕获异常

saga可以将所有异步请求拆分到同一个文件里，适合大型项目；

thunk增加了dispatch的类型，将异步请求拆分到actionCreator中

### React-Redux

- 用`Provider`包裹根节点，将数据提供给子树下所有节点，但需要保证Provider**仅包裹一个节点**
- `connect`方法，两个参数`connect(mapStateToProps,mapDispatchToProps)(Component)`
  - `mapStateToProps(state)`：默认接受state作为参数，将Store中state映射为本组件的props
  - `mapDispatchToprops(dispatch)`：默认接受dispatch做参数，将store.dispatch挂载到组件的props上
- 现在可以不用subscribe了
- 使用connect相当于将业务逻辑拆分出来，导出了**容器组件**
- reducer可以拆分到组件对应的文件夹下，用`combineReducers`合并

### 实战

#### sytle-Componenets & reset.css

直接import css会应用到该组件下所有标签

使用style-componenets可以将作用域限制在本组件范围内，避免多组件CSS冲突

reset.css可以统一不同浏览器中同名标签的不同样式

#### 拆分actionCreator、reducer

将`actionCreator`、`reducer`以及`actionType`常量可以都放到组件store文件夹下，并通过index.js暴露接口给外部

#### immutable

`immutable.fromJS({})`将JS对象转化为immutable对象，不可修改，内部数组会变成immutable数组

通过`get(attrName)`方法获取对象内容，`set(attrName，value)`方法返回修改后的新对象

`redux-immutable`库中的`combineReducers`可以返回`immutable`对象

`store.get("header").get("focus") === store.getIn(["header","focus"])`

#### 路由

第三方库: `react-router-dom`

`BrowserRouter`、`Route`标签

`BrowserRouter`和Provider类似，也需要保证仅包裹了一个标签

- `<Route path="/" render={()=>{<div>}}>` **当**出现`“/”`路径就显示div

- `<Route path="/" exact render={()=>{<div>}}> `**仅**出现`“/”`路径才显示div

**动态路由：**

Link标签加上to={'/detail/'+id}

主路由Route path 加上额外参数/:id

**通过参数传递：**

Link标签加上to={'detail?id='+id}

不影响原来Route path的匹配

参数`?id=x`在props.location.search中

**withRouter**

export时包裹组件`withRouter(Component)`，用来获取history、location、match三个对象传入props对象上；

路由组件可以直接获取这些属性，而非路由组件就必须通过`withRouter`修饰后才能获取这些属性，例如：

```
<Route path='/' component={App}/>
```

`App`组件就可以直接获取路由中这些属性了，**但是**，如果`App`组件中如果有一个子组件`Foo`，那么`Foo`就不能直接获取路由中的属性了，必须通过`withRouter`修饰后才能获取到。

### 其他

- dangerouslySetInnerHtml

> `dangerouslySetInnerHTML` 是 React 为浏览器 DOM 提供 `innerHTML` 的替换方案。通常来讲，使用代码直接设置 HTML 存在风险，因为很容易无意中使用户暴露于[跨站脚本（XSS）](https://en.wikipedia.org/wiki/Cross-site_scripting)的攻击。因此，你可以直接在 React 中设置 HTML，但当你想设置 `dangerouslySetInnerHTML` 时，需要向其传递包含 key 为 `__html` 的对象，以此来警示你。

- 异步组件

按需加载组件，提升加载速度

第三方库：react-loadable

