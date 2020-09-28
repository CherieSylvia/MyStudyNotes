# 1. ES5与ES6 语法补充

## 1.1 ES5定义类

```javascript
function Person(name,age) {
    this.name = name;
    this.age = age;
}

Person.prototype.running = function() {
    console.log(this.name + this.age + "running")  //aaa18running
}

var p = new Person("aaa",18);
console.log(p.name + "：" + p.age);  //aaa：18
p.running()
```

## 1.2 ES6定义类

```JavaScript
class Person {
    //构造方法
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    //定义方法
    running() {
        console.log(this.name,this.age,"running")  //bbb 19 running
    }
}

var p = new Person("bbb",19)
console.log(p.name,p.age)  //bbb 19
p.running()
```

## 1.3 this绑定问题

1. ```JavaScript
   var func = p.running;
   func()  //undefined，本来绑定在func上，直接调用func()隐式绑定丢失
   ```

2. ```JavaScript
   var func = p.running;
   
   var obj = {
       name: "ccc",
       age: 20
   }
   func.call(obj);  //ccc 20 running，显示绑定obj对象
   ```

3. ```JavaScript
   var func = p.running;
   
   var obj = {
       name: "ccc",
       age: 20
   }
   
   func = func.bind(obj);
   func()  //ccc 20 running，bind将一个函数总是显示绑定到一个对象上
   ```

## 1.4 ES6类的继承

```JavaScript
class Person {
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }

    running() {
        console.log("running")
    }
}

class Student extends Person {
    constructor(name,age,sno) {
        //子类中必须初始化父类对象（调用super）
        super(name,age)
        this.sno = sno;
    }
}

const stu = new Student("aaa",18,110)
console.log(stu.name,stu.age,stu.sno)  //aaa 18 110
stu.running()  //running
```

> 在constructor中，子类必须通过super来调用父类的构造方法，对父类进行初始化，否则会报错。

## 1.5 数组的高阶函数（map）

**map**：回调函数有三个参数

​	执行时的对应元素，对应的下标值，完整的数组对象

```JavaScript
<script>
    const names = ["aaa","bbb","ccc","ddd"]

    const newNames = names.map((item,index,arr) => {
        return item + "000"
    })
    console.log(newNames)  //["aaa000","bbb000","ccc000","ddd000"]
</script>
```

# 2. 案例练习

## 2.1 电影列表

```JavaScript
<script type="text/babel">
    class App extends React.Component {
        constructor() {
            super()
            this.state = {
                movies: ["星际穿越", "大话西游", "盗梦空间", "少年派"]
            }
        }

        render() {
            let array = [];
            for(let item of this.state.movies) {
                array.push(<li>{item}</li>)
            }

            return (
                <div> 
                    //1.普通做法
                    <h2>电影列表1</h2>
                    <ul>
                        {array}
           			</ul>
            		//2.使用高阶函数map
            		<h2>电影列表2</h2>
            		<ul>
                        {
                            this.state.movies.map((item,index,arr) => {
                                return <li>{item}</li>
                            })
       					}
       			 	</ul>
				</div>
			)
		}
	}

	ReactDOM.render(<App/>,document.getElementById("app"))
</script>
```

## 2.2 计数器

```JavaScript
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            counter: 0
        }
    }

    render() {
        return (
            <div>
                <h2>当前计数：{this.state.counter}</h2>
                <button onClick={this.increment.bind(this)}>+1</button>
                <button onClick={this.decrement.bind(this)}>-1</button>
            </div>
		)
	}

    increment() {
        this.setState({
            counter: this.state.counter + 1
        })
    }

    decrement() {
        this.setState({
            counter: this.state.counter - 1
        })
    }
}

ReactDOM.render(<App/>,document.getElementById("app"))
```

# 3. JSX语法解析

## 3.1 认识JSX语法

什么是JSX？

- JSX是一种JavaScript的语法扩展（eXtension），也在很多地方称之为JavaScript XML，因为看起就是一段XML语法；
- 它用于描述我们的UI界面，并且其完全可以和JavaScript融合在一起使用；

```JavaScript
//JSX语法
const element = <h2>Hello World</h2>
ReactDOM.render(element,document.getElementById("app"))
```

JSX的书写规范：

- JSX的顶层**只能有一个根元素**，所以我们很多时候会在外层包裹一个div原生（或者使用后面我们学习的Fragment）；

- 为了方便阅读，我们通常在jsx的外层包裹一个小括号()，这样可以方便阅读，并且jsx可以进行换行书写；

- JSX中的标签可以是单标签，也可以是双标签；

- - 注意：如果是单标签，必须以/>结尾；

## 3.2 JSX的使用

### 3.2.1 JSX的注释

```JavaScript
render() {
    return (
        <div>
            {/*我是一段JSX的注释*/}
            <h2>Hello World</h2>
        </div>
    )
}
```

### 3.2.2 JSX嵌入变量

- 情况一：当变量是Number、String、Array类型时，可以直接显示

- 情况二：当变量是null、undefined、Boolean类型时，内容为空，不显示；

- - 如果希望可以显示null、undefined、Boolean，那么需要转成字符串；
  - 转换的方式有很多，比如toString方法、和空字符串拼接，String(变量)等方式；

- 情况三：对象类型不能作为子元素（**报错：not valid as a React child**）

```JavaScript
this.state = {
    //1.{}可以直接显示
    name: "xxx",
    age: 18,
    movies: ["星际穿越", "大话西游", "盗梦空间", "少年派"],

    //2.{}不显示
    test1: null,
    test2: undefined,
    flag: true,

    obj: {
        name: "aaa",
        age: 18
    }
}

render() {
    return (
        <div>
            <h2>{this.state.name}</h2>
            <h2>{this.state.age}</h2>
            <h2>{this.state.movies}</h2>  {/*星际穿越大话西游盗梦空间少年派*/}

            <h2>{this.state.test1}</h2>
            <h2>{this.state.test2}</h2>
            <h2>{this.state.flag}</h2>
            <h2>{this.state.flag.toString()}</h2>  {/*true*/}

			<h2>{this.state.flag ? "你好啊" : null}</h2>  {/*你好啊*/}

            <h2>{this.state.obj}</h2>  {/*报错不显示*/}
        </div>
    )
}
```

### 3.2.3 JSX嵌入表达式

JSX中，也可以是一个表达式。例如：

- 运算表达式
- 三元运算符
- 执行一个函数

```JavaScript
class App extends React.Component {
    constructor() {
        super()
        this.state = {
            firstname: "xxx",
            lastname: "aaa",
            age:20
        }
    }
    render() {
        const {firstname,lastname,age} = this.state
        return (
            <div>
                {/*运算符表达式*/}
                <h2>{firstname + " " + lastname}</h2>
                <h2>{20 * 5}</h2>

                {/*三元表达式*/}
   				 <h2>{age>18 ? "成年人" : "未成年人"}</h2>

                {/*调用一个函数*/}
                <h2>{this.getFullName()}</h2>
            </div>
		)
    }
    getFullName() {
        return this.state.firstname + " " + this.state.lastname
    }
}
```

### 3.2.4 JSX绑定属性

```JavaScript
function getSizeImage(imgURL,size) {
    return imgURL + `?param=${size}x${size}`
}

class App extends React.Component {
    constructor() {
        super()
        this.state = {
            title: "标题",
            imgURL: "http://p2.music.126.net/OjlAjd43ajVIns8-M98ugA==/109951165177312849.jpg",
            link: "http://www.baidu.com",
            active: true
        }
    }
    render() {
        const { title, imgURL, active, link } = this.state
        return (
            <div>
            	{/*1.绑定普通属性*/}
                <h2 title={title}>我是标题</h2>
                <img src={getSizeImage(imgURL,140)} alt=""/>
                <a href={link} target="_blank">百度一下</a>  {/*target="_blank"：在另一个窗口打开*/}

                {/*2.绑定class*/}
                {/*warning:Invalid DOM property `class`. Did you mean `className`?*/}
                <div class="box title">我是div元素</div>  
                <div className={"box title " + (active ? "active" : "")}>我也是div元素</div>
                <label htmlFor=""></label>

                {/*3.绑定内联样式style*/}
                <div style={{color: "red", fontSize: "50px"}}>我是div元素，使用内联样式style</div>
			</div>
        )
    }
}
```

> 注意：
>
> 1. 绑定class比较特殊，因为class在js中是一个关键字，所以jsx中不允许直接写class，使用className替代
> 2. style后面跟的是一个对象类型，写js语法，对象中是样式的属性名和属性值键值对对象；使用驼峰标识，而不是连接符 -

## 3.3 JSX事件监听

### 3.3.1 this绑定问题

**方案1：bind给btnClick显示绑定this**

在传入函数时，我们可以主动绑定this：

- 这里我们主动将btnClick中的this通过bind来进行绑定（显示绑定）
- 那么之后React内部调用btnClick函数时，就会有一个this，并且是我们绑定的this；

**方案2：使用ES6 class fields语法**

在传入函数时，我们可以主动绑定this：

- 这里我们主动将btnClick中的this通过bind来进行绑定（显示绑定）
- 那么之后React内部调用btnClick函数时，就会有一个this，并且是我们绑定的this；

**方案3：事件监听时传入箭头函数（推荐）**

因为 `onClick` 中要求我们传入一个函数，那么我们可以直接定义一个箭头函数传入：

- 传入的箭头函数的函数体是我们需要执行的代码，我们直接执行 `this.btnClick()`；
- `this.btnClick()`中通过this来指定会进行隐式绑定，最终this也是正确的；

> 箭头函数不绑定this，找上层作用域App，找到正确this

```JavaScript
class App extends React.Component {
    constructor() {
        super()

        this.state = {
            message: "Hello world",
            counter : 100
        }

        this.btnClick = this.btnClick.bind(this);
    }
    render() {
        return (
            <div>
                {/*方案一：bind绑定this(显示绑定)*/}
                <button onClick={this.btnClick}>按钮1</button>
                <button onClick={this.btnClick}>按钮2</button>
                <button onClick={this.btnClick}>按钮3</button>

                {/*方案二：定义函数时，使用箭头函数*/}
                <button onClick={this.increment}>+1</button>

                {/*方案三：直接传入一个箭头函数，在箭头函数中调用需要执行的函数*/}
                <button onClick={ () => { this.decrement() } }>-1</button>
			</div>
		)
	}
    btnClick() {
        console.log(this.state.message)
    }
    increment = () => {
        console.log(this.state.counter)
    }
    decrement() {
        console.log(this.state.counter)
    }
}
//JSX语法
ReactDOM.render(<App/>,document.getElementById("app"))
```

### 3.3.2 事件参数传递

在执行事件函数时，有可能我们需要获取一些参数信息：比如event对象、其他参数。

1. **原生获取event对象**

```HTML
<button class="btn">按钮</button>
<script>
    document.getElementsByClassName("btn")[0].addEventListener("click",(e) => {
        console.log(e);
    })
</script>
```

2. 获取event对象

   - 很多时候我们需要拿到event对象来做一些事情（比如阻止默认行为）
   - 假如我们用不到this，那么直接传入函数就可以获取到event对象；

   ```JavaScript
   class App extends React.Component {
       constructor() {
           super()
       }
       render() {
           return (
               <div>
               	<a href="http://www.baidu.com" onClick={this.btnClick}>百度一下</a>
   			</div>
   		)
   	}
       btnClick(event) {
           //阻止a标签的默认行为
           event.preventDefault();
           console.log("按钮发生了点击",event);
       }
   }
   ```

3. 获取其他参数

   - 有更多参数时，我们最好的方式就是传入一个箭头函数，主动执行的事件函数，并且传入相关的其他参数；

   ```JavaScript
   class App extends React.Component {
               constructor() {
                   super()
   
                   this.state = {
                       movies: ["大话西游","流浪地球","哪吒","姜子牙"]
                   }
               }
               render() {
                   return (
                       <div>
                           <ul>
                               {
                                   this.state.movies.map((item,index,arr) => {
                                       return (
                                           <li onClick={(e) => this.liClick(item,index,e)}>{item}</li>
                                       )
                                   })
                               }
                           </ul>
                       </div>
                   )
               }
               liClick(item,index,e) {
                   console.log(item,index,e);
               }
           }
   ```

## 3.4 条件渲染

- 在vue中，我们会通过指令来控制：比如v-if、v-show；
- 在React中，所有的条件判断都和普通的JavaScript代码一致；

常见的条件渲染方式：

1. 条件判断语句if：适合逻辑代码非常多的情况

2. 三元运算符：根据不同的条件返回不同的结果

3. 与运算符：条件成立，渲染一个组件，不成立，什么也不渲染

   > 逻辑与：一个条件成立，另一个条件就不判断了

```JavaScript
class App extends React.Component {
    constructor() {
        super()

        this.state = {
            isLogin: true
        }

    }
    render() {
        const { isLogin } = this.state

        // 1.通过if判断，逻辑代码非常多的情况
        let welcome = null;
        let btnText = null;
        if(isLogin) {
            welcome = "欢迎回来~"
            btnText = "退出"
        } else {
            welcome = "请登录~"
            btnText = "登录"
        }
        return (
            <div>
                <h2>{ welcome }</h2> 
            	{/*2.三目运算符*/}
                <button onClick={() => this.btnClick()}>{isLogin ? "退出" : "登录"}</button>
                <hr/>
            
                <h2>{isLogin ? "你好啊，xxx" : null}</h2>
				{/*3.逻辑与&&*/}
                {/*逻辑与：isLogin为false，表达式为false，不显示*/}
                <h2>{isLogin && "你好啊，xxx"}</h2>
			</div>
		)
	}
    btnClick() {
        this.setState({
            isLogin: !this.state.isLogin
        })
    }
}
```

### 3.4.1 v-show效果（不频繁销毁创建DOM元素）

针对一个HTML原生，渲染和不渲染之间，如果切换的非常频繁，那么会相对比较损耗性能：

- 在开发中，其实我们可以通过display的属性来控制它的显示和隐藏；

- 在控制方式在vue中有一个专门的指令：v-show；

- React没有指令，但是React会更加灵活（灵活带来的代价就是需要自己去实现）；

  > {isLogin && <h2>你好啊，xxx</h2>} ：此代码消耗性能

```
render() {
    const { isLogin } = this.state
    const displayValue = isLogin ? "block" : "none"
    return (
        <div>
            <button onClick={e => this.btnClick()}>{isLogin ? "退出" : "登录"}</button>
            <h2 style={{display: displayValue}}>你好啊，xxx</h2>
        </div>
	)
}
```

## 3.5 列表渲染

在React中，**展示列表**最多的方式就是使用数组的**map**高阶函数；

很多时候我们在展示一个数组中的数据之前，需要先对它进行一些处理：

- 比如**过滤**掉一些内容：**filter函数**
- 比如**截取**数组中的一部分内容：**slice函数**

```JavaScript
constructor() {
    super()

    this.state = {
        names: ["aaa","bbb","ccc","ddd","eee"],
        numbers: [11,35,50,334,110,8,,70,99]
    }

}
render() {
    return (
        <div>
        <h2>名字列表</h2>
        <ul>
            {
                this.state.names.map(item => {
                	return <li>{item}</li>
            	})
            }
   		</ul>
        <h2>数字列表（过滤）</h2>
        <ul>
            {
           		this.state.numbers.filter(item => item >= 50)
                				.map(item => <li>{item}</li>)
                 }
        </ul>
        <h2>数字列表（截取）</h2>
        <ul>
		{
			this.state.numbers.slice(0,4)
                			.map(item => <li>{item}</li>)
		}
        </ul>
        </div>
	)
}
```

