# react_basic

## 1 简介

### 1.1 介绍

用于动态构建用户界面的JavaScript库（只关注于视图）

### 1.2 特点

1. 声明式编码
2. 组件化编码
3. React Native 编写原生应用
4. 高效（优秀的Diffing算法），尽量减少于真实DOM的交互

### 1.3 高效原因

1. 使用虚拟(virtual)DOM, 不总是直接操作页面真实DOM。
2. DOM Diffing算法, 最小化页面重绘。

## 2 虚拟DOM

1. 本质是Object类型的对象
2. 虚拟DOM比较“轻”，真实DOM比较“重”，因为虚拟DOM是React内部在用，无需真实DOM上那么多的属性。
3. 虚拟DOM最终会被React转化为真实DOM，呈现在页面上

## 3 JSX

### 3.1 作用与语法

作用：用来简化创建虚拟DOM，不是字符串也不是HTML/XML标签，最终产生的就是一个JS对象

语法规则：

1. 定义虚拟DOM时，不要写引号。
2. 标签中混入JS表达式时要用{}
3. 样式的类名指定不要用class，要用`className`。
4. 内联样式，要用`style={{key:value}}`的形式去写。key使用小驼峰形式
5. 只有一个根标签
6. 标签必须闭合 `<input type="text" />`
7. 标签首字母
   1. 若小写字母开头，则将该标签转为html中同名元素，若html中无该标签对应的同名元素，则报错。
   2. 若大写字母开头，react就去渲染对应的组件，若组件没有定义，则报错。

### 3.2 js语句与js表达式

1. 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方
   - a
   - a+b
   - arr.map()
   - function test() {}
2. 语句（代码）：不产生值，只控制代码的走向
   - if() {}
   - for() {}
   - switch(){case:xxxx}

## 4 模块与组件

模块：向外提供特定功能的js程序，一般就是一个js文件

组件：包含页面局部功能的全部代码和资源的集合

### 4.1 函数式组件

```jsx
function MyComponent(){
    console.log(this); //此处的this为undefined
    //babel编译后开启了严格模式，禁止自定义函数中的this指向window
    return <h2>函数式组件（适用于【简单组件】的定义）</h2>
}
ReactDOM.render(<MyComponent/>,document.getElementById('test'))
/* 
执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
	1.React解析组件标签，找到了MyComponent组件。
	2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。
*/
```

### 4.2 类式组件

```jsx
class MyComponent extends React.Compont {
	render(){
        return <h2>类定义的组件（适用于【复杂组件】的定义）</h2>
    }
}
ReactDOM.render(<MyComponent/>,document.getElementById('test'))
/* 
执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
	1.React解析组件标签，找到了MyComponent组件。
	2.发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
	3.将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。*/
```

### 4.3 区别

1. 函数组件是一个纯函数，它接收一个props对象返回一个react元素；而类组件需要去继承React.Component并且创建render函数返回react元素。
2. 函数组件**没有**生命周期和状态state，而类组件有。

## 5 组件三大属性

### 5.1 state

1. state是组件对象最重要的属性, 值是对象(可以包含多个key-value的组合)
2. 组件被称为"状态机", 通过更新组件的state来更新对应的页面显示(重新渲染组件)

```jsx
/*
state可以在constructor中初始化
也可以在内部直接初始化
*/
```

### 5.2 props

#### 5.2.1 理解

1. 每个组件对象都会有props(properties的简写)属性
2. **组件标签**的所有属性都保存在props中

#### 5.2.2 作用

1. 组件间传数据
2. 注意: 组件内部不要修改props数据

#### 5.2.3 对props进行限制

```jsx
//对标签属性进行类型、必要性的限制 Person为组件名
// Person.propTypes = {
static propTypes = {
    name:PropTypes.string.isRequired, //限制name必传，且为字符串
    sex:PropTypes.string,//限制sex为字符串
    age:PropTypes.number,//限制age为数值
    speak:PropTypes.func,//限制speak为函数
}
//指定默认标签属性值
static defaultProps = {
    sex:'男',//sex默认值为男
    age:18 //age默认值为18
}

当为函数组件时，接收props参数
```

### 5.3 refs

组件里的标签可以用ref来标识自己

1. 字符串形式的ref

   `<input ref="input1" type="text" />`

2. 回调函数形式的ref

   - 内联函数方式，在更新过程中会被执行两次，第一次传入参数null，第二次传入参数DOM元素

     ```jsx
     <input ref={currentNode => this.input1 = currentNode } type="text" />
     ```

     

   - class的绑定函数的方式

3. createRef

   React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点,该容器是“专人专用”的，只能存一个

   ```jsx
   myRef = React.createRef()
   <input ref={this.myRef} />
   console.log(this.myRef.current.value)
   ```

## 6 事件处理与高阶函数

### 6.1 事件处理

1. 通过onXxx属性指定事件处理函数(注意大小写)
   1. React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 ——— 为了更好的兼容性
   2. React中的事件是通过事件委托方式处理的(委托给组件最外层的元素) ———为了的高效
2. 提过event.target得到发生事件的DOM元素对象——不要过度使用ref

#### 6.1.1 阻止表单提交

``event.preventDefault()``

### 6.2 高阶函数

如果一个函数符合下面2个规范中的任何一个，那该函数就是高阶函数。

1. 若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数。
2. 若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数。
3. 常见的高阶函数有：Promise、setTimeout、arr.map()等等

函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。

```js
function sum(a){
    return(b)=>{
        return (c)=>{
            return a+b+c
        }
    }
}
```

### 6.3 受控组件与非受控组件

受控组件依赖状态state，只有继承 React.Component 才会有状态；非受控组件与其相反

受控组件一般适用于需要动态设置初始值时，非受控组件一般用于**无任何动态**初始值信息时。

## 7 组件的生命周期

### 7.1 旧生命周期

![](D:\Temp\VSCodework\test1\react\demo\react_basic\12_组件的生命周期\2_react生命周期(旧).png)

1. 初始化阶段: 由ReactDOM.render()触发---初次渲染
   1. constructor() 构造器
   2. componentWillMount() 将要挂载
   3. render()
   4. **componentDidMount()** ==== 常用，一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
2. 更新阶段: 由组件内部this.setSate()或父组件render触发
   1. shouldComponentUpdate() 控制组件更新的“阀门” 组件是否更新
   2. componentWillUpdate() 将要更新
   3. **render()** ==== 必须使用的一个
   4. componentDidUpdate() 更新完毕
3. 卸载组件: 由ReactDOM.unmountComponentAtNode(元素)触发
   1. **componentWillUnmount()** ==== 常用，一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息

### 7.2 新生命周期

![3_react生命周期(新)](D:\Temp\VSCodework\test1\react\demo\react_basic\12_组件的生命周期\3_react生命周期(新).png)

新增：

1. getDerivedStateFromProps 若state的值在任何时候都取决于props，那么可以使用
2. getSnapshotBeforeUpdate 在更新之前获取快照

## 8 key的作用

### 8.1 react/vue中的key有什么作用（key的内部原理）

虚拟DOM中key的作用：

1. 简单来说: key是虚拟DOM对象的标识, 在更新显示时key起着极其重要的作用。

2. 详细的说: 当状态中的数据发生变化时，react会根据【新数据】生成【新的虚拟DOM】, 随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：
   1. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
   
      - 若虚拟DOM中内容没变, 直接使用之前的真实DOM
   
      - 若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM
   
   4. 旧虚拟DOM中未找到与新虚拟DOM相同的key，根据数据创建新的真实DOM，随后渲染到到页面

### 8.2 用index作为key可能会引发的问题

1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作: 会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。
2. 如果结构中还包含输入类的DOM：会产生错误DOM更新 ==> 界面有问题。 
3. 注意！如果不存在对数据的逆序添加、逆序删除等破坏顺序操作， 仅用于渲染列表用于展示，使用index作为key是没有问题的。

### 8.3 开发中如何选择key

1. 最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
2. 如果确定只是简单的展示数据，用index也是可以的。

# react_staging

## 1 创建

``create-react-app name``

#### 解构赋值

```jsx
//获取用户的输入(连续解构赋值+重命名)
const {keyWordElement:{value:keyWord}} = this

<input ref={c => this.keyWordElement = c} type="text" />
```

## 2 案例相关知识点

### 2.1 props通信

父子之间通信： 

1. 【父组件】给【子组件】传递数据：通过props传递 
2. 【子组件】给【父组件】传递数据：通过props传递，要求父提前给子传递一个函数 ，然后子组件调用``this.props.xxxx()``

### 2.2 消息订阅与发布机制

#### 2.2.1 理解

1. 先订阅subscribe，再发布publish（理解：有一种隔空对话的感觉） 

2. 适用于任意组件间通信 

3. 要在组件的componentWillUnmount中取消订阅unsubscribe

   `PubSub.subscribe(name,(msg,data)=>{})` `PubSub.publish(name,data)`

#### 2.2.2 应用

```jsx
//在要接受消息组件里订阅
componentDidMount(){
    this.token = Pubsub.subscribe('name',(_,stateObj)=>{
        this.setState(stateObj)
    })
}

componentWillUnmount(){
    Pubsub.unsubscribe(this.token)
}

//在要发送消息组件里发布
PubSub.publish('atguigu',{isFirst:false,isLoading:true})
```

### 2.3 发送网络请求

#### 2.3.1 axios发送

```jsx
axios.get(`/api1/search/user2?q=${keyWord}`).then(
	response => {
        PubSub.publish('name',{data})
    },error => {
        PubSub.publish('name',{data2})
    }
)
```

#### 2.3.2 fetch发送

```jsx
search = async()=>{
    try{
        const response = await fetch(`//api1/search/user2?q=${keyWord}`)
        const data = await response.json()
        PubSub.publish('name',{data})
    } catch(error){
        PubSub.publish('name',{data2})
    }
}
```

## 3 路由

### 3.1 基本使用

```jsx
{/*编写路由链接*/}
<Link to="/about">About</Link>

{/*注册路由*/}
<Route path="/about" component={Home} />//v5.x写法
<Route path="/about" element={<Home/>} />//v6写法
```

### 3.2 路由组件与一般组件

1. 写法不同
2. 存放位置不同： 
   - 一般组件：components 
   - 路由组件：pages 
3. 接收到的props不同： 
   - 一般组件：写组件标签时传递了什么，就能收到什么 
   - 路由组件：接收到三个固定的属性 
     1. history: 
        - go: ƒ go(n) 
        - goBack: ƒ goBack() 
        - goForward: ƒ goForward() 
        - push: ƒ push(path, state) 
        - replace: ƒ replace(path, state) 
     2. location: 
        - pathname: "/about" 
        - search: "" 
        - state: undefined 
     3. match: 
        - params: {} 
        - path: "/about" 
        - url: "/about"

### 3.3 NavLink

1. NavLink可以实现路由链接的高亮，通过activeClassName指定样式名
2. 标签体内容是一个特殊的标签属性
3. 通过this.props.children可以获取标签体内容

```jsx
<NavLink activeClassName="classname" to="/about">About </NavLink>
```

### 3.4 Switch的使用

1. 通常情况下，path和component是一一对应的关系。
2. Switch可以提高路由匹配效率(单一匹配)

### 3.5 解决多级路径刷新页面样式丢失问题

1. public/index.html 中 引入样式时不写 ``./ ``写`` / ``（常用）
2. 引入样式时不写`` ./ ``写 ``%PUBLIC_URL% ``（常用）
3. 使用HashRouter

### 3.6 路由的严格匹配和模糊匹配

1. 默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
2. 开启严格匹配：``<Route exact={true} path="/about" component={About}/>``
3. 严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

### 3.7 Redirect的使用

一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由

```jsx
<Switch>
    <Route path="/about" component={About}/>
    <Route path="/home" component={Home}/>
    <Redirect to="/about"/>
</Switch>
```

### 3.8 向路由组件传递参数

#### 3.8.1 params参数

```jsx
//路由链接(携带参数)
<Link to={`/demo/test/${msgObj.id}/${msgObj.title}`}>详情</Link>
//注册路由(声明接收)：
<Route path="/demo/test/:id/:title" component={Test}/>
//接收参数：
const {id,title} = this.props.match.params
```

#### 3.8.2 search参数

```jsx
//路由链接(携带参数)
<Link to={`/demo/test/?id=${msgObj.id}&title=${msgObj.title}`}>详情</Link>
//注册路由(无需声明接收)：
<Route path="/demo/test/" component={Test}/>

//接收参数：
import qs from 'qs'
const {search} = this.props.location
//获取到的search是urlencoded编码字符串，需要解析
const {id,title} = qs.parse(search.slice(1))
```

#### 3.8.3 state参数

刷新也可以保留参数

```jsx
//路由链接
<Link to={{pathname:'/home/message/detail',state:{id:msgObj.id,title:msgObj.title}}}>详情</Link>
//注册路由(无需声明接收)：
<Route path="/demo/test/" component={Test}/>

//接收参数：
const {id,title} = this.props.location.state
```

### 3.9 编程式路由导航

```jsx
借助this.prosp.history对象上的API对操作路由跳转、前进、后退
   -this.prosp.history.push() //默认，压栈
   -this.prosp.history.replace()//替换 replace={true}
   -this.prosp.history.goBack()//后退
   -this.prosp.history.goForward()//前进
   -this.prosp.history.go()//参数为正则前进
```

### 3.10 BrowserRouter与HashRouter区别

1. 底层原理不一样： 
   - BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。 
   - HashRouter使用的是URL的哈希值。 
2. path表现形式不一样：
   - BrowserRouter的路径中没有#,例如：localhost:3000/demo/test 
   - HashRouter的路径包含#,例如：localhost:3000/#/demo/test 
3. 刷新后对路由state参数的影响 ：
   1. BrowserRouter没有任何影响，因为state保存在history对象中。 
   2. HashRouter刷新后会导致路由state参数的丢失！！！ 
4. 备注：HashRouter可以用于解决一些路径错误相关的问题。

### 3.11 withRouter

withRouter可以加工一般组件，让一般组件具备路由组件所特有的API

withRouter的返回值是一个新组件

```jsx
//在暴露时候使用
export default withRouter(Header)
```

# react_extension

## 1 setState更新状态两种写法

### 1.1 对象式

``setState(stateChange, [callback])``         

1. stateChange为状态改变对象(该对象可以体现出状态的更改)            
2. callback是可选的回调函数, 它在状态更新完毕、界面也更新后(render调用后)才被调用

### 1.2 函数式

``setState(updater, [callback])``      

1. updater为返回stateChange对象的函数。         
2. updater可以接收到state和props。            
3. callback是可选的回调函数, 它在状态更新、界面也更新后(render调用后)才被调用。

如果需要在setState()执行后获取最新的状态数据，要在第二个callback函数中读取

## 2 lazyLoad

```jsx
// 1.通过React的lazy函数配合import()函数动态加载路由组件 ===> 路由组件代码会被分开打包
const Login = lazy(()=>import('./pages/Login'))
	
// 2.通过<Suspense>指定在加载得到路由打包文件前显示一个自定义loading界面
<Suspense fallback={<h1>loading.....</h1>}>
    <Route path="/xxx" component={Xxxx}/>
    <Redirect to="/login"/>
</Suspense>
```

## 3 Hooks

### 3.1 作用

1. Hook是React 16.8.0版本增加的新特性/新语法 
2. 可以在**函数组件**中使用 state 以及其他的 React 特性

### 3.2 常用的hook

- State Hook:  ``React.useState()``
- Effect Hook:  ``React.useEffect() ``
- Ref Hook:  ``React.useRef()``

```jsx
//数组的结构赋值，count为0，setCount为改变count的函数
const [count,setCount] = React.useState(0)
const myRef = React.useRef()

React.useEffect(()=>{
    let timer = setInterval(()=>{
        setCount(count => count+1 )
        // setCount(count+1 )
    },1000)
    return ()=>{
        clearInterval(timer)
    }
},[])
```

#### 3.2.1 State Hook

1. State Hook让函数组件也可以有state状态, 并进行状态数据的读写操作 
2. 语法: const [xxx, setXxx] = React.useState(initValue)   
3.  useState()说明:        
   - 参数: 第一次初始化指定的值在内部作缓存        
   - 返回值: 包含2个元素的数组, 第1个为内部当前状态值, 第2个为更新状态值的函数 
4. setXxx()2种写法:        
   - setXxx(newValue): 非函数写法, 直接指定新的状态值, 内部用其覆盖原来的状态值        
   - setXxx(value => newValue): 函数写法, 接收原本的状态值, 返回新的状态值, 内部用其覆盖原来的状态值

#### 3.2.2 Effect Hook

发生在浏览器渲染结束后执行

1. Effect Hook 可以让你在函数组件中执行副作用操作(用于模拟类组件中的生命周期钩子)

2. React中的副作用操作:       

   - 发ajax请求数据获取       
   - 设置订阅 / 启动定时器       
   - 手动更改真实DOM 

3. 语法和说明:     

   ```jsx
   useEffect(() => {
       //比如开启定时器
       return () => { // 在组件卸载前执行          // 在此做一些收尾工作, 比如清除定时器/取消订阅等         
   	}        
   }, [stateValue]) // 如果指定的是[], 回调函数只会在第一次render()后执行
   ```

4. 可以把 useEffect Hook 看做如下三个函数的组合

   - componentDidMount()
   - componentDidUpdate()
   - componentWillUnmount() 

#### 3.2.3 Ref Hook

1. Ref Hook可以在**函数组件**中存储/查找组件内的标签或任意其它数据 
2. 语法: ``const refContainer = useRef() ``
3. 作用:保存标签对象,功能与React.createRef()一样

## 4 Fragment

react return里返回多个元素值要有父标签包裹。

React.Fragment组件能够在不额外创建 DOM 元素的情况下，让 render()方法中返回多个元素。相当于空标签<></>。

还可以接收key，遍历循环渲染元素

```jsx
<Fragment key={item.id}>
    <Xxxx />
</Fragment>
```

## 5 Context

### 5.1 理解

一种组件间通信方式, 常用于【祖组件】与【后代组件】间通信

### 5.2 使用

```jsx
1) 创建Context容器对象：
	const XxxContext = React.createContext()  
2) 渲染子组件时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：
	<xxxContext.Provider value={数据}>
		子组件
    </xxxContext.Provider>
    
3) 后代组件读取数据：
	//第一种方式:仅适用于类组件 
	  static contextType = xxxContext  // 声明接收context
	  this.context // 读取context中的value数据
	  
	//第二种方式: 函数组件与类组件都可以
	  <xxxContext.Consumer>
	    {
	      value => ( // value就是context中的value数据
	        要显示的内容
	      )
	    }
	  </xxxContext.Consumer>
```

## 6 组件优化

### 6.1 Component的2个问题

1. 只要执行setState(),即使不改变状态数据, 组件也会重新render()
2. 只当前组件重新render(), 就会自动重新render子组件 ==> 效率低

原因：Component中的shouldComponentUpdate()总是返回true

我们希望：只有当组件的state或props数据发生改变时才重新render()

### 6.2 解决

```js
办法1: 
	重写shouldComponentUpdate()方法
	比较新旧state或props数据, 如果有变化才返回true, 如果没有返回false
办法2:  
	使用PureComponent
	PureComponent重写了shouldComponentUpdate(), 只有state或props数据有变化才返回true

shouldComponentUpdate(nextProps,nextState){
	return !this.state.carName === nextState.carName
    
	注意: 
		只是进行state和props数据的浅比较, 如果只是数据对象内部数据变了, 返回false  
		不要直接修改state数据, 而是要产生新数据
项目中一般使用PureComponent来优化
```

## 7 render props

### 7.1 怎么向组件内部动态传入带内容的结构（标签）

- Vue中: 

  使用slot技术, 也就是通过组件标签体传入结构  <AA><BB/></AA>

- React中:

  使用children props: 通过组件标签体传入结构

  使用render props: 通过组件标签属性传入结构, 一般用render函数属性

#### children props

```jsx
<A>
  <B>xxxx</B>
</A>
{this.props.children}
问题: 如果B组件需要A组件内的数据, ==> 做不到 
```

#### render props

```jsx
<A render={(data) => <C data={data}></C>}></A>
A组件: {this.props.render(内部state数据)}
C组件: 读取A组件传入的数据显示 {this.props.data} 
```

## 8 错误边界

### 8.1 作用

错误边界：用来捕获后代组件错误，渲染出备用页面

只能捕获后代组件**生命周期**产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误

### 8.2 使用

```jsx
// 生命周期函数，一旦子组件报错，就会触发
static getDerivedStateFromError(error) {    			   console.log(error);    
  // 在render之前触发 
  // 返回新的state
  return {        
      hasError: true,    
  }; 
} 
componentDidCatch(error, info) {    
   // 统计页面的错误，发送请求发送到后台去
   console.log(error, info); 
}

{this.state.hasError ? <h2>当前网络不稳定，稍后再试</h2> : <Child/>}
```

## 9 组件通信方式总结

### 9.1 方式

1. props：
   - children props
   - render props
2. 消息订阅-发布：
   - pubs-sub、event等等
3. 集中式管理：
   - redux、dva等等
4. conText:
   - 生产者-消费者模式

### 9.2 组件间关系

```js
父子组件：props
兄弟组件(非嵌套组件)：消息订阅-发布、集中式管理
祖孙组件(跨级组件)：消息订阅-发布、集中式管理、conText(用的少)
```

# react-router6

## 1 概述

1. React Router 以三个不同的包发布到 npm 上，它们分别为：
   1. react-router: 路由的核心库，提供了很多的：组件、钩子。
   2. **react-router-dom**:包含react-router所有内容，并添加一些专门用于 DOM 的组件，例如 `<BrowserRouter>`等。
   3. react-router-native: 包括react-router所有内容，并添加一些专门用于ReactNative的API，例如:`<NativeRouter>`等。
2. 与React Router 5.x 版本相比，改变了什么？
   1. 内置组件的变化：移除`<Switch/>` ，新增 `<Routes/>`等。
   2. 语法的变化：`component={About}` 变为 `element={<About/>}`等。
   3. 新增多个hook：`useParams`、`useNavigate`、`useMatch`等。
   4. **官方明确推荐函数式组件了！！！**

## 2 Component

### 2.1 ``<BrowserRouter>``

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom'
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

<HashRouter>修改的是地址栏的hash值，两者用法与5.x相同

### 2.2 ``<Routes/>``与``<Route/>``

1. v6版本中**移除**了先前的`<Switch>`，引入了新的替代者：`<Routes>`。
2. `<Routes>` 和 `<Route>`要配合使用，且**必须**要用`<Routes>`包裹`<Route>`。
3. `<Route>` 相当于一个 if 语句，如果其路径与当前 URL 匹配，则呈现其对应的组件。
4. `<Route caseSensitive>` 属性用于指定：匹配时是否区分大小写（默认为 false）。
5. 当URL发生变化时，`<Routes> `都会查看其所有子` <Route>` 元素以找到最佳匹配并呈现组件 。
6. `<Route>` 也可以嵌套使用，且可配合`useRoutes()`配置 “路由表” ，但需要通过 `<Outlet>` 组件来渲染其子路由。

```jsx
{/* 注册路由 */}
<Routes>
    <Route path="/about" element={<About/>}/>
    <Route path="/home" element={<Home/>}/>
</Routes>
```

### 2.3 NavLink高亮

NavLink默认类名是active，而如果需要自定义类名：

不同于v5.x中可以直接修改类名，v6中只能借助函数修改

```jsx
<NavLink className={computedClassName} to="/home">Home</NavLink>

function computedClassName({isActive}){
    return isActive ? 'item addName' : 'item'
}

/*
	默认情况下，当Home的子组件匹配成功，Home的导航也会高亮，
	当NavLink上添加了end属性后，若Home的子组件匹配成功，则Home的导航没有高亮效果。
*/
<NavLink to="home" end >home</NavLink>
```

### 2.4 重定向

```jsx
<Route path="/" element={<Navigate to="/about"/>}/>
```

### 2.5 ``<Navigate>``

1. 作用：只要<Navigate>组件被渲染，就会修改路径，切换视图
2. replace属性用于控制跳转模式（push或replace，默认push）

```jsx
import React,{useState} from 'react'
import {Navigate} from 'react-router-dom'

export default function Home() {
	const [sum,setSum] = useState(1)
	return (
		<div>
			<h3>我是Home的内容</h3>
			{/* 根据sum的值决定是否切换视图 */}
			{sum === 1 ? <h4>sum的值为{sum}</h4> : <Navigate to="/about" replace={true}/>}
			<button onClick={()=>setSum(2)}>点我将sum变为2</button>
		</div>
	)
}
```

### 2.6 ``<Outlet>``

当<Route>产生嵌套时，渲染其对应的后续子路由

```jsx
//根据路由表生成对应的路由规则
const element = useRoutes([
  {
    path:'/home',
    element:<Home/>,
    children:[
      {
        path:'news',
        element:<News/>
      },
      {
        path:'message',
        element:<Message/>,
      }
    ]
  }
])

//Home.js
return (
    <div>
        <h2>Home组件内容</h2>
        <div>
            <NavLink className="list-group-item" to="news">News</NavLink>
            <NavLink className="list-group-item" to="message">Message</NavLink>
            {/* 指定路由组件呈现的位置 */}
            <Outlet />
        </div>
    </div>
)
```

## 3 Hooks

### 3.1 useRoutes()

作用：根据路由表，动态创建<Routes>和<Route>

```jsx
//路由表配置：src/routes/index.js
import About from '../pages/About'
import {Navigate} from 'react-router-dom'

export default [
	{
		path:'/about',
		element:<About/>
	},
	{
		path:'/',
		element:<Navigate to="/about"/>
	}
]

//App.jsx
import React from 'react'
import {NavLink,useRoutes} from 'react-router-dom'
import routes from './routes'

export default function App() {
	//根据路由表生成对应的路由规则
	const element = useRoutes(routes)
	return (
		<div>
			......
      {/* 注册路由 */}
      {element}
		  ......
		</div>
	)
}
```

### 3.2 useNavigate()

作用：返回一个函数用来实现编程式导航

```jsx
export default function Demo() {
  const navigate = useNavigate()
  const handle = () => {
    //第一种使用方式：指定具体的路径
    navigate('/login', {
      replace: false,
      state: {a:1, b:2}
    }) 
    //第二种使用方式：传入数值进行前进或后退，类似于5.x中的 history.go()方法
    navigate(-1)
  }
  return (
    <div>
      <button onClick={handle}>按钮</button>
    </div>
  )
}
```

### 3.3 useParams()

作用：返回当前匹配路由的params参数，类似5.x中的match.params

```jsx
<Route path="users/:id" element={<User />}/>

//获取参数
const { id } = useParams();
```

### 3.4 useSearchParams()

作用：用于读取和修改当前位置的URL中的查询字符串

**返回值**：一个包含两个值的数组，内容分别为：当前的seaech参数、更新search的函数。

```jsx
const [search,setSearch] = useSearchParams()
const id = search.get('id')
const title = search.get('title')
const content = search.get('content')
return (
    <ul>
        <li>
            <button onClick={()=>setSearch('id=008&title=哈哈&content=嘻嘻')}>点我更新一下收到的search参数</button>
        </li>
        <li>消息编号：{id}</li>
        <li>消息标题：{title}</li>
        <li>消息内容：{content}</li>
    </ul>
)
```

### 3.5 useLocation()

作用：获取当前 location 信息，对标5.x中的路由组件的`location`属性

```jsx
const x = useLocation()
console.log('@',x)
// x就是location对象: 
/*
{
  hash: "",
  key: "ah9nv6sz",
  pathname: "/login",
  search: "?name=zs&age=18",
  state: {a: 1, b: 2}
}
*/
```

### 3.6 useMatch()

作用：返回当前匹配信息，对标5.x中的路由组件的`match`属性。

```jsx
const match = useMatch('/login/:x/:y')
console.log(match) //输出match对象
//match对象内容如下：
/*
{
  params: {x: '1', y: '10'}
  pathname: "/LoGin/1/10"  
  pathnameBase: "/LoGin/1/10"
  pattern: {
    path: '/login/:x/:y', 
    caseSensitive: false, 
    end: false
  }
}
*/
```

### 3.7 useInRouterContext()

 作用：如果组件在 `<Router>` 的上下文中呈现，则 `useInRouterContext` 钩子返回 ``true``，否则返回 ``false``。

### 3.8 useNavidationType()

1. 作用：返回当前的导航类型（用户是如何来到当前页面的）。
2. 返回值：`POP`、`PUSH`、`REPLACE`。
3. 备注：`POP`是指在浏览器中直接打开了这个路由组件（刷新页面）。

###  3.9 useOutlet()

作用：用来呈现当前组件中渲染的嵌套路由。

```jsx
const result = useOutlet()
console.log(result)
// 如果嵌套路由没有挂载,则result为null
// 如果嵌套路由已经挂载,则展示嵌套的路由对象
```

### 3.10 useResolvedPath()

作用：给定一个URL值，解析其中的：path、search、hash值

# redux