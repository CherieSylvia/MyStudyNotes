# 1. React是什么

React：用于构建用户界面的 JavaScript 库。

## 1.1 React的特点

1. 声明式编程
2. 组件化开发
3. 多平台适配

# 2. Hello React案例

要求：在界面上显示一个文本，点击下方按钮改变文本（hello world -> hello react）

## 2.1 原生代码实现

```html
<body>
    <h2 class="title"></h2>
    <button class="btn">改变文本</button>

    <script>
        let message = "hello world";

        const titleEl = document.getElementsByClassName("title")[0];
        titleEl.innerHTML = message;

        const btnEl = document.getElementsByClassName("btn")[0];
        btnEl.addEventListener('click', e => {
            message = "hello react";
            titleEl.innerHTML = message;
        })
    </script>
</body>
```

## 2.2 React实现

### 2.2.1 React开发依赖

开发React必须依赖三个库：

1. react：包含react所必需的核心代码
2. react-dom：react渲染在不同平台的核心代码
3. babel：将 jsx 转换为 React 代码的工具

> 拆分成 react 和 react-dom 是为了适配 react-native（开发移动端跨平台）
>
> react-dom针对web和native所完成的事情不同：
>
> - web端：react-dom会讲jsx最终渲染成真实的DOM，显示在浏览器中 
>
> - native端：react-dom会讲jsx最终渲染成原生的控件（比如Android中的Button，iOS中的UIButton）

#### babel

- 目前前端使用非常广泛的编辑器、转移器

- 使用babel工具，将ES6转换成大多数浏览器都支持的ES5语法

- babel将jsx（JavaScript XML）的语法转换成React.createElement。

#### 引入React依赖

1. 方式一：直接CDN引入

   ```javascript
   <script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
   <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
   <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
   ```

   > crossorigin属性的目的是为了拿到跨域脚本（远程脚本）的错误信息

2. 方式二：下载后，添加本地依赖

3. 方式三：通过npm管理（脚手架时使用）

### 2.2.2 React 实现

> 注意事项：
>
> 1. 使用 jsx ，并且希望script中的 jsx 代码被解析，必须在script标签中添加一个属性 `type="text/babel"`
> 2. jsx特点：多个标签要用一个根标签包裹

```javascript
<script type="text/babel">

    let message = "hello world"

    function btnClick() {
        message = "hello react"
        console.log(message)
        render()  
    }


    //ReactDOM对象是从 react-dom.development.js  export出的
    // ReactDOM.render(渲染的内容，挂载的对象)
    // 渲染的东西会覆盖掉挂载对象里的内容
    function render() {
        ReactDOM.render(
            <div>
            <h2>{message}</h2>
            <button onClick={btnClick}>改变文本</button>
   			 </div>,
   			 document.getElementById("app")
   		 )
    }
    render();
</script>
```

> 注意：
>
> 更换message后必须重新 render，否则页面不会发生改变

#### 组件化实现

上面代码过于零散，利用组件化来实现

如已经封装好组件，需要渲染，代码如下：

```html
<div id="app">

</div>


<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>

<script type="text/babel">
	//渲染组件
	ReactDOM.render(<App/>,document.getElementById("app"));
</script>
```

封装组件时，使用es6语法的class

- 组件须继承自`React.Component`，利用`render`函数返回要渲染的内容。
- 变量写在`constructor`函数里，需调用`super()`继承父类
- 点击事件可直接写在类里

按照之前的思路，代码如下：**（错误写法）**

```JavaScript
class App extends React.Component {
    constructor () {
        super();
        this.message = "Hello Wolrd"
    }
    render() {
        return (
            <div>
            <h2>{this.message}</h2>
            <button onClick={this.btnClick}>改变文本</button>
            </div>
        )
    }
    btnClick() {
        console.log(this)   //undefined
        this.message = "Hello React"
        this.render();
    }
}
```

出现错误：`Uncaught TypeError: Cannot set property 'message' of undefined`

**原因**：通过onClick 调用，实际上内部是 `btnClick.apply(undefined)`传入的就是undefined，要想正常运行，传入参数this：`onClick={this.btnClick.bind(this)}`

> - 在类中直接定义一个函数，并且将这个函数绑定到html原生的onClick事件上，当前这个函数的this指向的是谁呢？
>
> - 默认情况下是undefined
>
> - - 很奇怪，居然是undefined；
>   - 因为在正常的DOM操作中，监听点击，监听函数中的this其实是节点对象（比如说是button对象）；
>   - 这次因为React并不是直接渲染成真实的DOM，我们所编写的button只是一个语法糖，它的本质React的Element对象；
>   - 那么在这里发生监听的时候，react给我们的函数绑定的this，默认情况下就是一个undefined；

这时`this.message`已改为 Hello React，但界面上DOM无改变，因为改完之后render函数不会重新执行。手动执行后也无变化。

> 注意：任何情况下都不要手动调用render函数，手动调用无任何效果

正确写法：

- 用`this.state`来存储组件的状态。
- 改变变量：通过 `this.setState({})`修改状态，状态发生变化时，UI会自动更新

正确代码如下：

```javascript
//封装App组件
class App extends React.Component {
    constructor () {
        super();
        this.state = {
            message: "Hello world"
        }
    }
    render() {
        return (
            <div>
            <h2>{this.state.message}</h2>
            <button onClick={this.btnClick.bind(this)}>改变文本</button>
        	</div>
        )
    }
    btnClick() {
        this.setState({
            message: "Hello React"
        })
    }
}

//渲染组件
ReactDOM.render(<App/>,document.getElementById("app"));
```

