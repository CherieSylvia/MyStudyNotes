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

### JSX的注释

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

### JSX嵌入变量

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

### JSX嵌入表达式

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

### JSX绑定属性

```JavaScript
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