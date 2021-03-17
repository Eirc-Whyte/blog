---
title: mobx-react踩坑
date: 2020-02-24 20:13:34
tags: 
- react
- mobx
categories: 
- 前端学习
thumbnail: /gallery/image-20200229214823165.png
---

哦吼快看这个托更博主终于更博了（雾）

<!-- more-->

> MobX用于简单、可扩展的状态管理。通常搭配 React 使用，但不只限于 React。如何你厌烦了 Redux 繁杂的模板代码和 API，那么可以尝试下 MobX。

#### 前言

目前对状态管理非常浅显的理解：状态管理就是把动态页面的每一个“帧”看作一个状态，当发生与用户之间的交互（点击、输入）时，呈现给用户的页面的状态会发生改变（登录成功、加载新页面等）。而react的状态信息可能存储在组件树的各个组件之上，因此如果一次交互需要触发多个不同层级组件的状态刷新时，可能会比较麻烦，因此需要状态管理工具。

扯白了说，react的各个组件就相当于一堆子函数，而且参数只能通过相邻层级进行传递。这对于某些数据而言十分不自然。在C++中，我们自然可以开辟全局变量来解决这种问题，而在react中，由于涉及到与浏览器的交互balabala，比较普遍的解决方案是使用Redux进行状态管理。

但是！Redux的应用场景大多数还是大型应用，写起来比较规（ma）范（fan）。对于数据库课设这种小型应用，可选的还有mobx、dva这种轻量一些的状态管理工具。他们都可以直接在组件树的**任意节点**访问以及更新状态库store中的数据（状态）。在我的理解中就相当于一个**动态的全局变量库**。

选择mobx是因为dva的文档看不太懂（雾）

然而mobx也有很多坑，就比如——

#### issue1：配置babel环境

由于mobx普遍采用装饰器语法（其实也可以选择非装饰器语法但是太丑2333）所以需要配上装饰器语法的支持：

##### solution1：使用 customize-cra react-app-rewired 

由于使用的是官方的create-react-app*脚手架*，所以如果不能自己直接修改webpack之类

> “脚手架”是一种元编程的方法，用于构建基于数据库的应用。许多MVC框架都有运用这种思想。
> 程序员编写一份specification（规格说明书），来描述怎样去使用数据库；而由（脚手架的）编译器来根据这份specification生成相应的代码，进行增、删、改、查数据库的操作。我们把这种模式称为"脚手架"，在脚手架上面去更高效的建造出强大的应用！

的配置。所以我们使用社区开源的一个修改CRA（CreatReactApp）配置的工具customize-cra 配合react-app-rewired，直接绕过（覆盖）原配置，进行自定义配置。

1. 安装customize-cra react-app-rewired 以及装饰器支持@babel/plugin-proposal-decorators

```shell
yarn add customize-cra react-app-rewired @babel/plugin-proposal-decorators
```

2. 项目根目录（src的同级目录）新建文件config-overrides.js文件：

```javascript
const { override, addDecoratorsLegacy } = require('customize-cra');
module.exports = override(
 addDecoratorsLegacy()
 );
```

3. 修改package.json文件：

```json
"scripts": {
 "start": "react-app-rewired start",
 "build": "react-app-rewired build",
 "test": "react-app-rewired test",
 "eject": "react-app-rewired eject"
  },
```

##### solution2：使用npm run eject弹出配置文件进行修改

执行完这个命令`yarn run eject`后会将封装在 CRA 中的配置全部`反编译`到当前项目，这样用户就可以完全取得 webpack 文件的控制权，想怎么修改就怎么修改了。

> CRA 与其他脚手架不同的另一个地方，就是可以通过升级其中的`react-scripts`包来升级 CRA 的特性。比如用老版本 CRA 创建了一个项目，这个项目不具备 [PWA](https://developers.google.com/web/progressive-web-apps/) 功能，但只要项目升级了`react-scripts`包的版本就可以具备 PWA 的功能，项目本身的代码不需要做任何修改。
>
> 但如果我们使用了`eject`命令，就再也享受不到 CRA 升级带来的好处了，因为`react-scripts`已经是以文件的形式存在于你的项目，而不是以包的形式，所以无法对其升级。
>
> 作者：Run丘比特

1. 首先弹出配置

```shell
yarn run eject
```

2. 安装装饰器扩展

```shell
yarn add babel-plugin-transform-decorators-legacy
```

3. 修改package.json ( 注意transform-decorators-legacy要放在第一个 )

```json
"babel": {
    "plugins": [
        "transform-decorators-legacy"
    ],
    "presets": [
        "react-app"
    ]
}
```

然后就解决了装饰器语法的问题，可以开始愉快地使用mobx了。

因为网上很多教程可能都没用CRA构建，所以基本都是只有**sulution2**的后半部分，没有写明需要**弹出配置文件**，因此再怎么折腾.balrc都不管用。所以，如果用了CRA脚手架构建react应用时使用mobx（或者其他需要装饰器语法的包时）都要先eject/override一下配置文件才行。

> Reference传送门：
>
> [MobX入门 TodoList](https://segmentfault.com/a/1190000015387255)
>
> [Create React App无eject配置](https://juejin.im/post/5dedd6c8f265da33d15884bf)

#### issue2：updateUser is not a function！?

这个问题也不知道是啥时候整的历史遗留问题，之前也不知道为啥work，现在突然就不work了让我一顿好找。感谢Chrome的开发者工具，断点调试真香。

其实就是涉及到一个语法错误的问题。

mobx的使用流程大概是：

新建一个observable的store存放状态——在应用入口文件index.js中将其import到文件中——new 一个store——使用Provider组件（context实现）包裹最外层的组件节点，刚刚new 的store作为属性传入——在内层任意一个节点需要时inject到组件中并使用observer装饰器监听被inject状态的变化并重新渲染。

然而我在index.js中new 是这么写的：

```js
const store = {
  store:new Store()
}
```

然后还是这么导入：

```html
<Provider store={store}>
    <BrowserRouter>
        <App/>
    </BrowserRouter>
</Provider>
```

然后访问store的action函数时一直提示：

*Unhandled Rejection (TypeError):  this.props.store.updateUser( ) is Not a function.*

找了一万个博客也找不到，一度怀疑是装饰器解释器的问题，但是更换了不用装饰器的方法还是8行。最后在Chrome里瞎搞，在调用函数的前一行打了个断点，然后再到控制台**观察它的自动补全内容**（tcl），这才发现玄机：this.props.store打印出来是个

`{store : UserInfo}`

然后this.props.store.store.updateUser是个函数！然后右手就轻轻地落到了自己的脸上（MMMMMMMMP）

最后修改了store的定义：

``` js
const store = new Store()
```

就好了qwq



预告:react-cookie的使用

待续...