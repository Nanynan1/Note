# vue_basic

## 1 创建Vue实例

```js
new Vue({
    el:'#demo', //el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串。
    data:{ //data中用于存储数据，数据供el所指定的容器去使用，值我们暂时先写成一个对象。
        name:'atguigu',
        address:'北京'
    }
})
```

## 2 模板语法

```js
Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示
```

### 2.1 插值语法

功能：用于解析标签体内容。

写法：{{xxx}}，xxx是**js表达式**，且可以直接读取到data中的所有属性。

数据来源：

- data
- props
- computed

### 2.2 指令语法

功能：用于解析标签（包括：标签属性、标签体内容、绑定事件.....）。

举例：v-bind:href="xxx" 或  简写为 :href="xxx"，xxx同样要写js表达式，且可以直接读取到data中的所有属性。

## 3 数据绑定

### 3.1 单向绑定``v-bind``

数据只能从data流向页面。

### 3.2 双向绑定``v-model``

数据不仅能从data流向页面，还可以从页面流向data。

双向绑定一般都应用在表单类元素上（如：input、select等）

```html
<input type="text" :value="name"><br/> 单向
<input type="text" v-model="name"><br/> 双向
```

## 4 el与data的两种写法

### 4.1 el

1. new Vue时候配置el属性。
2. 先创建Vue实例，随后再通过``vm.$mount('#root')``指定el的值。

### 4.2 data

1. 对象式 ``data:{name:'xiaom'}``

2. 函数式（使用组件时必须用函数式）

   ```js
   data(){
       return{
           name:'xiaom'
       }
   }
   //由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了。
   ```

## 5 MVVM模型

1. M：模型(Model) ：data中的数据

​            2. V：视图(View) ：模板代码

​            3. VM：视图模型(ViewModel)：Vue实例

vm身上所有的属性 及 Vue原型上所有属性，在Vue模板中都可以直接使用。

## 6 数据代理

### 6.1 介绍

Vue中的数据代理：通过vm对象来代理data对象中属性的操作（读/写）

数据代理的优点：更加方便的操作data中的数据

###   6.2 基本原理

1.  通过Object.defineProperty()把data对象中所有属性添加到vm上。
2. 为每一个添加到vm上的属性，都指定一个getter/setter。
3. 在getter/setter内部去操作（读/写）data中对应的属性。

```js
let number = 18
Object.defineProperty(person,'age',{
    enumerable: true, //控制属性是否可以枚举
    writable: true, //控制属性是否可以被修改
    configurable: true, //是否可以被删除，以上默认false
    
    //当有人读取person的age属性时，get函数(getter)就会被调用，且返回值就是age的值
    get(){
        return 18
    }
    set(value){
    	number = value
	}
})
```

## 7 事件处理

### 7.1 事件的基本使用

1. 使用``v-on:xxx`` 或 ``@xxx ``绑定事件，其中xxx是事件名；
2. 事件的回调需要配置在methods对象中，最终会在vm上；
3. methods中配置的函数，不要用箭头函数！否则this就不是vm了；
4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象；
5. ``@click="demo"`` 和`` @click="demo($event)" ``效果一致，但后者可以传参；

### 7.2 事件修饰符

- prevent：阻止默认事件（常用）；
- stop：阻止事件冒泡（常用）``event.stopPropagation()``
- once：事件只触发一次（常用）；
- capture：使用事件的捕获模式；
- self：只有event.target是当前操作的元素时才触发事件；
- passive：事件的默认行为立即执行，无需等待事件回调执行完毕；

```html
<button @click.stop="showInfo">提示</button>
```

### 7.3 键盘事件

- @keydown（键盘按下时触发）
- @keypress(键盘按住时触发)
- @keyup(键盘弹起)

获取按键的键码 ``e.keyCode``

``Vue.config.keyCodes.自定义键名 = 键码``可以去定制按键别名

1. 回车 => enter
2.   删除 => delete (捕获“删除”和“退格”键)
3. 退出 => esc
4. 空格 => space
5. 换行 => tab (特殊，必须配合keydown去使用)
6.  上 => up
7. 下 => down
8. 左 => left
9. 右 => right

## 8 计算属性

定义：要用的属性不存在，要通过已有属性计算得来。

原理：底层借助了Objcet.defineproperty方法提供的getter和setter。

get函数什么时候执行？

1.  初次读取时会执行一次。
2. 当依赖的数据发生改变时会被再次调用。

优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便。

备注：

1. 计算属性最终会出现在vm上，直接读取使用即可。
2. 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变。

```js
computed:{
    //完整写法
    fullName:{
        get(){},
        set(value){}
    }
    //简写
    fullName(){}
}
//fullName属于属性值，插入时不带括号
{{fullName}}
```

## 9 监视属性

### 9.1 基本使用

1. 当被监视的属性变化时, 回调函数自动调用, 进行相关操作
2. 监视的属性必须存在，才能进行监视！！
3. 监视的两种写法：
   1. new Vue时传入watch配置
   2. 通过vm.$watch监视

```js
watch:{
    isHot:{
        immediate:true, //初始化时让handler调用一下
        //handler什么时候调用？当isHot发生改变时。
        handler(newValue,oldValue){
            console.log(newValue,oldValue)
        }
    }
}

vm.$watch('isHot',{
    //具体属性
})
```

### 9.2 深度监视

1. Vue中的watch默认不监测对象内部值的改变（一层）。
2. 配置``deep:true``可以监测对象内部值改变（多级结构中所有属性的变化）

**注意**：

1. Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以！
2. 使用watch时根据数据的具体结构，决定是否采用深度监视。

```js
//监视多级结构中某个属性的变化
'numbers.a':{
    handler(){
        console.log('a被改变了')
    }
} 
```

### 9.3 简写形式

```js
isHot(newValue,oldValue){
    console.log(newValue,oldValue)
}

vm.$watch('isHot',(newValue,oldValue)=>{...})
```

### 9.4 computed和watch的区别

1. computed能完成的功能，watch都可以完成。
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。

 **两个重要的小原则**：              

1. 所被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象。
2. 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，这样this的指向才是vm 或 组件实例对象。

## 10 绑定样式

### 10.1 class样式

写法: ``class="xxx"`` xxx可以是字符串、对象、数组。

- 字符串写法适用于：类名不确定，要动态获取。
- 对象写法适用于：要绑定多个样式，个数不确定，名字也不确定。
- 数组写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用。

```js
<div :class="mood" >字符串</div>
<div :class="classArr" >数组</div>
<div :class="classObj" >对象</div>

//动态绑定后，在data中定义
mood:'normal',
classArr:['className1','className2'],
classObj:{
    className1:false,
    className2:false,
}
```

### 10.2 style样式

1. ``:style="{fontSize: xxx}"``其中xxx是动态值。
2. ``:style="[a,b]"``其中a、b是样式对象。

## 11 条件渲染

### 11.1 v-if

写法：

- v-if="表达式" 
- v-else-if="表达式"
- v-else="表达式"

适用于：切换频率**较低**的场景。

特点：不展示的DOM元素直接被移除（**包括结构**）

注意：v-if 可以和 v-else-if 、v-else 一起使用，但要求结构**不能**被打断。

```html
<!-- 配合使用可以展示多个内容，且template只能和v-if配合条件渲染 -->
<template v-if="n === 1">
    <h2>你好</h2>
    <h2>北京</h2>
</template>
```

### 11.2 v-show

写法：v-show="表达式"

适用于：切换频率**较高**的场景。

特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉

## 12 列表渲染

### 12.1 v-for

1. 用于展示列表数据
2. 语法：``v-for="(item, index) in xxx" :key="yyy"``
3. 可遍历：数组、对象、字符串（用的很少）、指定次数（用的很少）

```html
<li v-for="(p,index) of persons" :key="index">{{p.name}}-{{p.age}}</li>
```

key的作用同react遍历一样

### 12.2 更新数组时候的一个问题

```js
//在data中定义了person数组
persons:[
    {id:'001',name:'马冬梅',age:30,sex:'女'},
    {id:'002',name:'周冬雨',age:31,sex:'女'},
]
//在单个修改属性时候奏效，但修改整条数据时候失效
//this.persons[0] = {id:'001',name:'马老师',age:50,sex:'男'} //失效
this.persons.splice(0,1,{id:'001',name:'马老师',age:50,sex:'男'}) //成功
```

### 12.3 简单模拟数据监测原理

```js
//对象监测
let data = {
    name:'Jerry',
    age:18,
}

//创建一个监视的实例对象，用于监视data中属性的变化
const obs = new Observer(data)		
console.log(obs)	

//准备一个vm实例对象
let vm = {}
vm._data = data = obs

//创建一个监视的实例对象
function Observer(obj){
    //汇总对象中所有的属性形成一个数组
    const keys = Object.keys(obj)
    //遍历
    keys.forEach((k)=>{
        Object.defineProperty(this,k,{
            get(){
                return obj[k]
            },
            set(val){
                console.log(`${k}被改了，我要去解析模板，生成虚拟DOM.....我要开始忙了`)
                obj[k] = val
            }
        })
    })
}

//对于数组，vue包装了修改数组的七个方法，调用这7个方法来修改数组，vue才能监测到数据的改变
```

### 12.4 Vue.set

``Vue.set(target, key, val)`` 往**什么对象**上加**什么属性**，**属性值**多少

同理，``this.$set(this.student, 'sex', '男')``

**局限**：只能给data里面的某一个对象追加属性，而不能直接给 data / vm实例 追加属性

### 12.5 总结

Vue监视数据的原理：

1.  vue会监视data中所有层次的数据。

2. 如何监测对象中的数据？

   通过**setter**实现监视，且要在new Vue时就传入要监测的数据。

   1. 对象中后追加的属性，Vue默认不做响应式处理
   2. 如需给后添加的属性做响应式，请使用如下API：Vue.set(target，propertyName/index，value) 或  vm.$set(target，propertyName/index，value)

3. 如何监测数组中的数据？ 

   通过包裹数组更新元素的方法实现，本质就是做了两件事：  

   1. 调用原生对应的方法对数组进行更新。
   2. 重新解析模板，进而更新页面。

4. 在Vue修改数组中的某个元素一定要用如下方法：

   1. 使用这些API:  push()、pop()、shift()、unshift()、splice()、sort()、reverse()
   2. Vue.set() 或 vm.$set()

特别注意：Vue.set() 和 vm.$set() 不能给**vm、vm的根数据对象** 添加属性

## 13 收集表单数据

1. 若：<input type="text"/>，则v-model收集的是value值，用户输入的就是value值。
2. 若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value值。
3. 若：<input type="checkbox"/>
   1. 没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
   2. 配置input的value属性:
      1. v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
      2. v-model的初始值是数组，那么收集的的就是value组成的数组

### 13.1 v-model的三个修饰符：

- lazy：失去焦点再收集数据
-  number：输入字符串转为有效的数字
- trim：输入首尾空格过滤

## 14 过滤器

定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）

语法：

1. 注册过滤器：

   - 全局：Vue.filter(name,callback)

   - 局部：new Vue{ filters:{} }

2. 使用过滤器：{{ xxx | 过滤器名}}  或  v-bind:属性 = "xxx | 过滤器名"

 备注：

1. 过滤器也可以接收额外参数、多个过滤器也可以串联
2. 并没有改变原本的数据, 是产生新的对应的数据

## 15 指令

### 15.1 内置指令

- v-bind   : 单向绑定解析表达式, 可简写为 ``:xxx``
- v-model : 双向数据绑定
-  v-for  : 遍历数组/对象/字符串
- v-on   : 绑定事件监听, 可简写为@
- v-if     : 条件渲染（动态控制节点是否**存在**）
-  v-else  : 条件渲染（动态控制节点是否存存在）
-  v-show  : 条件渲染 (动态控制节点是否**展示**)

### 15.2 v-text

作用：向其所在的节点中渲染文本内容。

 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。

```html
<div v-text="name"></div>
```

### 15.3 v-html

作用：向指定节点中渲染包含html结构的内容。

与插值语法的区别：

1. v-html会替换掉节点中所有的内容，{{xx}} 则不会。
2. v-html可以识别html结构。

严重注意：v-html有**安全性问题**

1. 在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
2. 一定要在可信的内容上使用v-html，永不要用在用户提交的内容上

```js
<div v-html="str"></div>
<div v-html="str2"></div>

str:'<h3>你好啊！</h3>',
str2:'<a href=#>快来！</a>',
```

### 15.4 v-cloak

v-cloak没有值，本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。

使用css配合v-cloak可以解决网速慢时页面展示出``{{xxx}}``的问题。

```html
<h2 v-cloak>{{name}}</h2>
```

### 15.5 v-once

v-once所在节点在初次动态渲染后，就视为**静态内容**了。

以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能。

```html
<h2 v-once>初始化的n值是:{{n}}</h2>
```

### 15.6 v-pre

跳过其所在节点的编译过程。

可利用它跳过**没有**使用**指令语法**、没有使用**插值语法**的节点，会加快编译

```html
<h2 v-pre>学习Vue</h2>
```

### 15.7 自定义指令

#### 15.7.1 定义语法：

1. 局部指令：

   ```js
   <input type="text" v-fbind:value="n">
   //new Vue中定义指令如下  
   data:{
       n: 1
   },
   directives:{
       'big-number'(element,binding){...},
        big(element,binding){...},
        fbind:{
           //指令与元素成功绑定时（一上来）
           bind(element,binding){
               element.value = binding.value
           },
           //指令所在元素被插入页面时
           inserted(element,binding){
               element.focus()
           },
           //指令所在的模板被重新解析时
           update(element,binding){
               element.value = binding.value
           }
       }
   }
   ```

2. 全局指令：

   ```js
   Vue.directive('big-number',{...})
   ```

#### 15.7.2 配置对象中常用的3个回调：

- bind：指令与元素成功绑定时调用。
- inserted：指令所在元素被插入页面时调用。
- update：指令所在模板结构被重新解析时调用。

#### 15.7.3 备注：

1. 指令定义时不加``v-``，但使用时要加``v-``
2. 指令名如果是多个单词，要使用``kebab-case``命名方式，不要用``camelCase``命名。

## 16 生命周期

### 16.1 图示

又名：生命周期回调函数、生命周期函数、生命周期钩子。

Vue在关键时刻帮我们调用的一些特殊名称的函数。

生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。

生命周期函数中的this指向是vm 或 组件实例对象。

- beforeCreate
- created
- beforeMounte
- mounted
- beforeUpdate
- updated
- beforeDestroy
- destroyed

![lifecycle](D:\Temp\VSCodework\test1\md分析文档\assets\lifecycle.png)

三个未在图中：nextTick、activated、deactivated

### 16.2 总结

1. 常用的生命周期钩子：
   1. mounted: 发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】
   2. beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】
2. 关于销毁Vue实例
   1. 销毁后借助Vue开发者工具看不到任何信息。
   2. 销毁后自定义事件会失效，但原生DOM事件依然有效。
   3. 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。

## 17 组件

### 17.1 使用组件

Vue中使用组件的三大步骤：

1. 定义组件(创建组件)
2. 注册组件
3. 使用组件(写组件标签)

### 17.2 VueComponent

关于VueComponent：

1. school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的。

2. 我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：new VueComponent(options)。

3. 特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！！！！

4. 关于this指向：

   - 组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】。
   - new Vue(options)配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】。

5. VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）。

    Vue的实例对象，以后简称vm。

一个重要的内置关系：

``VueComponent.prototype.__proto__ === Vue.prototype``

作用：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。

# vue_test

## 1 不同版本的vue

vue.js与vue.runtime.xxx.js的区别：

1. vue.js是完整版的Vue，包含：核心功能+模板解析器。
2. vue.runtime.xxx.js是运行版的Vue，只包含：核心功能；没有模板解析器。

因为vue.runtime.xxx.js没有模板解析器，所以不能使用template配置项，需要使用 render函数接收到的createElement函数去指定具体内容。

## 2 属性

### 2.1 ref属性

1. 被用来给元素或子组件注册引用信息（id的替代者）

2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）

3. 使用方式：

   1. 打标识：`<h1 ref="xxx">.....</h1>` 或 

      ``<School ref="xxx" />`` School组件的实例对象

   2. 获取：`this.$refs.xxx`

### 2.2 props配置

功能：让组件接收外部传过来的数据

```js
//传递数据
<Demo name="xiaom"/>
    
//接收数据
//1.只接收
props:['name']
//2.限制类型
props:{
    name:String
}
//3.限制类型、必要性、默认性
props:{
    name:{
        type:String,
        required:true,
        default:'Jerry'  
    }
}
```

> props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。

1. 使用v-model时要切记：v-model绑定的值**不能**是props传过来的值，因为props是不可以修改的！
2. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错，但不推荐这样做。

props适用于：

1. 父组件==>子组件 通信
2. 子组件==>父组件 通信（要求父先给子一个函数）

```vue
<!--父组件给子组件传递一个函数-->
<MyHeader :addTodo="addTodo"/>

//子组件先接收
props:['addTodo'],
//后面调用函数，借此传回数据
this.addTodo(todoObj)
```

### 2.3 mixin（混入）

功能：可以把多个组件共用的配置提取成一个混入对象

```js
//1.定义mixin.js文件，并暴露配置
export const hunhe = {
	methods: {
		showName(){
			alert(this.name)
		}
	},
	mounted() {
		console.log('你好啊！')
	},
}

//2.引入mixin
//全局引入
Vue.mixin(hunhe)
//局部引入
mixin:[hunhe],
```

### 2.4 插件plugins

本质：包含``install(Vue,options)``方法的一个对象，options是插件使用者传递的数据

```js
//定义并暴露插件
export default {
    install(Vue,options){
        Vue.filter(...)
        Vue.directive(...)
        Vue.mixin(...)
        Vue.prototype.$myMethod = function(){...}
    }
}
//使用插件
Vue.use()
```

### 2.5 scoped样式

```vue
<style scoped>...</style>
让样式在局部生效，防止冲突
```

### 2.6 webStorage

1. 存储内容大小一般支持5MB左右（不同浏览器可能还不一样）

2. 浏览器端通过 ``Window.sessionStorage`` 和 ``Window.localStorage`` 属性来实现本地存储机制。

3. 相关API：

   1. `xxxxxStorage.setItem('key', 'value');` 该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值。

   2. `xxxxxStorage.getItem('person');`

       该方法接受一个键名作为参数，返回键名对应的值。

   3. `xxxxxStorage.removeItem('key');`

       该方法接受一个键名作为参数，并把该键名从存储中删除。

   4. `xxxxxStorage.clear()`

       该方法会清空存储中的所有数据。

4. 备注：

   1. SessionStorage存储的内容会随着浏览器窗口关闭而消失。
   2. LocalStorage存储的内容，需要手动清除才会消失。
   3. `xxxxxStorage.getItem(xxx)`如果xxx对应的value获取不到，那么getItem的返回值是null。
   4. `JSON.parse(null)`的结果依然是null。

### 2.7 nextTick

1. 语法：`this.$nextTick(回调函数)`
2. 作用：在下一次 DOM 更新结束后执行其指定的回调。即updated完成后再调用
3. 什么时候用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行。

```js
//调节代码执行顺序 也可以包一个延时定时器
//nextTick可以在下一次解析模板DOM节点更新完毕后再执行
this.$nextTick(function(){
    this.$refs.inputTitle.focus()
})
```

## 3 通信方式

### 3.1 组件的自定义事件

**适用于：子组件==>父组件**

子想给父传数据，就在父中给子绑定自定义事件，**事件对应回调在父组件里**

第一种写法（使用v-on）：

```js
//父组件中：
<Student @chufaName="getStudentName"/>
getStudentName(name){
    console.log('App收到了学生名：',name)
}

//子组件中：
<button @click="sendName">把学生名给App</button>
sendName(){
    //触发Student组件实例身上的chufaName事件
    this.$emit('chufa',this.name)
}
```

第二种写法（使用ref）：

```js
//父组件中：
<Student ref="student" />
//通过this.$refs.student即可以获取到Student组件的实例对象
mounted(){
    //绑定自定义事件
    this.$refs.student.$on('chufaName',this.getStudentName)
}

//子组件中写法同上
```

注意：

```js
//一次性，仅能使用一次
this.$refs.student.$once('chufaName',this.getStudentName)

//销毁了当前组件的实例，销毁后所有自定义事件全都不奏效。
this.$destroy()

注意：通过this.$refs.xxx.$on('atguigu',回调)绑定自定义事件时，回调要么配置在methods中，要么用箭头函数，否则this指向会出问题！
```

解绑自定义事件：

```js
//给谁绑定的 就在哪解绑
<button @click="unbind">解绑chufaName事件</button>

unbind(){
    this.$off('chufaName') //解绑一个自定义事件
    this.$off(['chufa1','chufa2']) //解绑多个自定义事件
    this.$off() //解绑所有的自定义事件
}
```

Vue2中，如果要给组件加JS原生事件，需要native修饰；但在Vue3中，可以直接写``@click="show"``

```html
<Student @click.native="show"/>
```

### 3.2 全局事件总线

一种组件间通信的方式，适用于**任意组件间**通信

```js
//安装全局事件总线，放在Vue的原型对象上
new Vue({
	el:'#app',
	render: h => h(App),
	beforeCreate() {
		Vue.prototype.$bus = this //安装全局事件总线
	},
})
```

```js
//谁要接收数据，就在该组件里定义
mounted(){
    this.$bus.$on('name',(data)=>{
        console.log('我接收到了数据：',data)
    })
},
//或者
methods(){
  demo(data){......}
},
mounted() {
  this.$bus.$on('xxxx',this.demo)//调用函数
}
//解绑
beforeDestroy() {
    this.$bus.$off('name')
},
    
//如果要提供数据，则调用
this.$bus.$emit('name',data)
```

### 3.3 消息订阅与发布

一种组件间通信的方式，适用于**任意组件间**通信

使用步骤：

1. 安装：``npm i pubsub-js``
2. 引入：``import pubsub from 'pubsub-js'``

```js
//谁要接收数据，就先订阅消息
methods(){
  demo(data){......}
},
mounted() {
  this.pubId = pubsub.subscribe('xxx',this.demo) //订阅消息
},
beforeDestroy() {
    //取消订阅
    pubsub.unsubscribe(this.pubId)
}
    
//如果要发送数据，则调用
pubsub.publish('xxx',666)
```

## 4 Vue封装的过度与动画

1. 作用：在插入、更新或移除 DOM元素时，在合适的时候给元素添加样式类名。

2. 写法：

   1. 准备好样式：

      - 元素进入的样式：
        1. v-enter：进入的起点
        2. v-enter-active：进入过程中
        3. v-enter-to：进入的终点
      - 元素离开的样式：
        1. v-leave：离开的起点
        2. v-leave-active：离开过程中
        3. v-leave-to：离开的终点

   2. 使用`<transition>`包裹要过度的元素，并配置name属性：

      ```vue
      appear：一开始就展示动画
      <transition name="hello" appear>
      	<h1 v-show="isShow">你好啊！</h1>
      </transition>
      
      <style scoped>
      	.hello-enter-active{
      		animation: aniName 0.5s linear;
      	}
      </style>
      ```

   3. 备注：若有多个元素需要过度，则需要使用：`<transition-group>`，且每个元素都要指定`key`值。

## 5 配置代理

### 5.1 方法一

在vue.config.js中添加如下配置：

```js
devServer:{
  proxy:"http://localhost:5000"
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求会转发给服务器 （优先匹配前端资源）

### 5.2 方法二

编写vue.config.js配置具体代理规则：

```js
module.exports = {
	devServer: {
      proxy: {
      '/api1': {// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api1': ''}
      },
      '/api2': {// 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api2': ''}
      }
    }
  }
}
/*
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
2. 缺点：配置略微繁琐，请求资源时必须加前缀。

## 6 插槽

1. 作用：让父组件可以向子组件指定位置插入**html结构**，也是一种组件间通信的方式，适用于 **父组件 ===> 子组件** 。
2. 分类：默认插槽、具名插槽、作用域插槽

### 6.1 默认插槽

```vue
<!--父组件中-->
<Category>
    <!--组件标签里的标签体内容-->
    <img src="#" alt="">
</Category>
components:{Category},

<!--子组件中-->
<template>
    <div>
       <!-- 定义插槽 -->
       <slot>插槽默认内容...</slot>
    </div>
</template>
```

### 6.2 具名插槽

```vue
<!--父组件中  v-slot相同-->
<Category>
    <!--组件标签里的标签体内容-->
    <img slot="num1" src="#" alt="">
    <input slot="num2" type="text"/>
</Category>
components:{Category},

<!--子组件中-->
<template>
    <div>
       <!-- 定义插槽 -->
       <slot name="num1">插槽默认内容1...</slot>
       <slot name="num2">插槽默认内容2...</slot>
    </div>
</template>
```

### 6.3 作用域插槽

理解：数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定）

```vue
<!--父组件中  slot-scope相同-->
<Category>
    <template scope="scopeData">
        <!-- 生成的是ul列表 -->
        <ul>
            <li v-for="g in scopeData.games" :key="#">{{g}}</li>
        </ul>
    </template>
    <template scope={games}>
        <!-- 生成的是ul列表 -->
        <ul>
            <li v-for="g in games" :key="#">{{g}}</li>
        </ul>
    </template>
</Category>
components:{Category},

<!--子组件中-->
<template>
    <div>
       <!-- 定义插槽 -->
       <slot :games="games"></slot>
    </div>
</template>

data() {
    return {
        games:['game1','game2','game3']
    }
},
```

## 7 Vuex

### 7.1 概念

在Vue中实现**集中式状态（数据）管理**的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种**组件间通信**的方式，且适用于任意组件间通信。

使用：多个组件需要共享数据时

### 7.2 Vuex使用

创建文件：``src/store/index.js``

```js
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//应用Vuex插件
Vue.use(Vuex)

//准备actions对象——响应组件中用户的动作，可以是异步函数
//this.$store.dispatch(方法名,数据)
const actions = {
    jiaOdd(context,value){
        //一个与 store 实例具有相同方法和属性的 context 对象
        context.state.num
        context.commit('JIA',value)
	}
}
    
//准备mutations对象——修改state中的数据,必须是同步函数
//this.$store.commit(方法名,数据)
const mutations = {
    JIA(state,value){
		......
	}
}

//准备state对象——保存具体的数据
// this.$store.state.sum
const state = {
    sum:0,
}

//创建并暴露store
export default new Vuex.Store({
	actions,
	mutations,
	state
})
```

在``main.js``中创建vm时传入store配置项

```js
new Vue({
	el:'#app',
	render: h => h(App),
	store
})
```

> 备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写`dispatch`，直接编写`commit`

### 7.3 getters

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。可以认为是store的计算属性

2. 在`store.js`中追加`getters`配置

   ```js
   const getters = {
   	bigSum(state){
   		return state.sum * 10
   	}
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	......
   	getters
   })
   ```

3. 组件中读取数据：`$store.getters.bigSum`

### 7.4 四个map方法的使用

#### 7.4.1 mapState

帮助我们映射state中的数据为计算属性，借助mapState生成计算属性，从state中读取数据。

```js
computed: {
    //借助mapState生成计算属性：sum、school、subject（对象写法）
 ...mapState({sum:'sum',school:'school',subject:'subject'})    
    //借助mapState生成计算属性：sum、school、subject（数组写法）
    ...mapState(['sum','school','subject']),
},
```

#### 7.4.2 mapGetters

借助mapGetters生成计算属性，从getters中读取数据。

```js
//借助mapGetters生成计算属性，从getters中读取数据。（对象写法）
...mapGetters({bigSum:'bigSum'})

//借助mapGetters生成计算属性，从getters中读取数据。（数组写法）
...mapGetters(['bigSum'])
```

#### 7.4.3 mapMutations

用于帮助我们生成与`mutations`对话的方法，即：包含`$store.commit(xxx)`的函数

```js
//借助mapMutations生成对应的方法，方法中会调用commit去联系mutations(对象写法)
...mapMutations({increment:'JIA',decrement:'JIAN'}),

//借助mapMutations生成对应的方法，方法中会调用commit去联系mutations(数组写法)
...mapMutations(['JIA','JIAN'])

//就不用我们自己写 this.$store.commit('JIA')
//如果要携带参数，就加在绑定事件上
<button @click="increment(n)">+</button>
```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

#### 7.4.4 mapActions

用于帮助我们生成与`actions`对话的方法，即：包含`$store.dispatch(xxx)`的函数

```js
//借助mapActions生成对应的方法，方法中会调用dispatch去联系actions(对象写法)
...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})

//借助mapActions生成对应的方法，方法中会调用dispatch去联系actions(数组写法)
...mapActions(['jiaOdd','jiaWait'])
```

### 7.5 模块化+命名空间

目的：让代码更好维护，让多种数据分类更加明确。

```js
namespaced: true, //开启命名空间，每个store模块都要加
const store = new Vuex.Store({
  modules: {
    countAbout,
    personAbout
  }
})
```

开启命名空间后，读取数据写法变化：

读取state数据：

```js
this.$store.state.personAbout.name

...mapState('personAbout',['name'])
```

读取getters数据：

```js
this.$store.getters['personAbout/getName']

...mapGetters('personAbout',['getName'])
```

调用actions里的方法：

```js
this.$store.dispatch('personAbout/acName',data)

...mapActions('personAbout',{increment:'acName'})
```

调用mutations里的方法：

```js
this.$store.commit('personAbout/muName',data)

...mapMutations('personAbout',{incrementmu:'muName'})
```

## 8 路由

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key是路径，value是组件。

### 8.1 基本使用

```js
// 该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../components/About'
import Home from '../components/Home'

//创建并暴露一个路由器
export default new VueRouter({
	routes:[
		{
			path:'/about',
			component:About
		},
		{
			path:'/home',
			component:Home
		}
	]
})
```

实现切换：``active-class``可以配置高亮样式

```html
<router-link active-class="active" to="/about">About</router-link>
```

指定展示位置：

```html
<router-view></router-view>
```

### 8.2 几个注意点

1. 路由组件通常存放在`pages`文件夹，一般组件通常存放在`components`文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被**销毁**掉的，需要的时候再去挂载。
3. 每个组件都有自己的`$route`属性，里面存储着自己的路由信息。
4. 整个应用只有一个router，可以通过组件的`$router`属性获取到。

### 8.3 多级路由

配置路由规则：

```js
children:[ //通过children配置子级路由
    {
        path:'news', //此处不能写 /news
    	component:News
    }
]
```

跳转（要写完整路径，与react不同）：

```html
<router-link to="/home/news">News</router-link>
```

展示：依旧用``router-view``指定

```html
<router-view></router-view>
```

### 8.4 命名路由

作用：可以简化路由的跳转

在路由配置时候给路由命名：

```js
name: 'routeName'
```

在跳转时候：

```html
<router-link to="{name:'routeName'}">News</router-link>
```

### 8.5 路由的params参数

声明接收：

```js
{
    name:'jieshou'
    path: '/home/:id/:title',
    component:Home,
}
```

传递参数：

```html
<!-- 跳转并携带params参数，to的字符串写法 -->
<router-link :to="/home/666/你好">跳转</router-link>
				
<!-- 跳转并携带params参数，to的对象写法 -->
<router-link 
	:to="{
		name:'jieshou',
		params:{
		   id:666,
           title:'你好'
		}
	}"
>跳转</router-link>
```

> 注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

接收参数：

```js
$route.params.id
$route.params.title
```

### 8.6 query参数

无需声明，直接传递：

```html
<!-- 跳转并携带query参数，to的字符串写法 -->
<router-link :to="/home/?id=666&title=你好">跳转</router-link>
				
<!-- 跳转并携带query参数，to的对象写法 -->
<router-link 
	:to="{
		path:'/home',
		query:{
		   id:666,
           title:'你好'
		}
	}"
>跳转</router-link>
```

接收参数：

```js
$route.query.id
$route.query.title
```

### 8.7 路由的props配置

作用：让路由组件更方便的收到参数

```js
{
    name:'xiangqing',
    path:'/detail',
    component:Detail,

    //props的第一种写法，值为对象，该对象中的所有key-value都会以props的形式传给Detail组件。
    // props:{a:1,b:'hello'}

    //props的第二种写法，值为布尔值，若布尔值为真，就会把该路由组件收到的所有params参数，以props的形式传给Detail组件。
    // props:true

    //props的第三种写法，值为函数（可以传递query参数）
    props($route){
        return {
            id:$route.query.id,
            title:$route.query.title
        }
    }
    // 结构赋值的连续写法
    // props({query:{id,title}}){
    // 	return {id,title}
    // }

}
```

### 8.8 ``<router-link>``的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为`push`和`replace`，`push`是追加历史记录，`replace`是替换当前记录。路由跳转时候默认为`push`
3. 如何开启`replace`模式：`<router-link replace .......>News</router-link>`

### 8.9 编程式路由导航

作用：不借助`<router-link> `实现路由跳转，让路由跳转更加灵活

```js
//$router的两个API
this.$router.push({
	name:'xiangqing',
		params:{
			id:xxx,
			title:xxx
		}
})

this.$router.replace({
	name:'xiangqing',
		params:{
			id:xxx,
			title:xxx
		}
})
this.$router.forward() //前进
this.$router.back() //后退
this.$router.go() //可前进也可后退
```

### 8.10 缓存路由组件

作用：让不展示的路由组件保持挂载，不被销毁。

因为不展示的路由组件默认销毁

```html
//include=组件名,要缓存的组件名；如果没有写，则缓存该区域的所有组件
<keep-alive :include="['News','Message']"> 
<keep-alive include="News"> 
    <router-view></router-view>
</keep-alive>
```

### 8.11 activated与deactivated

两个新的生命周期钩子，要求**组件是被缓存的**

1. 作用：路由组件所**独有**的两个钩子，用于捕获路由组件的激活状态。
2. 具体：
   1. `activated`路由组件被激活时触发。
   2. `deactivated`路由组件失活时触发。

```js
activated() {
    //从不出现到出现
    console.log('News组件被激活了')
    //留下定时器ID
    this.timer = setInterval(() => {
        console.log('@')
        this.opacity -= 0.01
        if(this.opacity <= 0) this.opacity = 1
    },16)
},
deactivated() {
    console.log('News组件失活了')
    clearInterval(this.timer)
},
```

### 8.12 路由守卫

1. 作用：对路由进行权限控制
2. 分类：全局守卫、独享守卫、组件内守卫

#### 8.12.1 全局守卫

```js
//在路由配置时候设置路由元信息
meta:{isAuth:true,title:'新闻'}

//全局前置守卫：初始化时执行、每次路由切换前执行
router.beforeEach((to,from,next)=>{
	console.log('beforeEach',to,from)
	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
		if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
			next() //放行
		}else{
			alert('暂无权限查看')
			// next({name:'guanyu'})
		}
	}else{
		next() //放行
	}
})

//全局后置守卫：初始化时执行、每次路由切换后执行
router.afterEach((to,from)=>{
	console.log('afterEach',to,from)
	if(to.meta.title){ 
		document.title = to.meta.title //修改网页的title
	}else{
		document.title = 'vue_test'
	}
})
```

#### 8.12.2 独享守卫

```js
//放在路由配置中 
{
    path:'/home',
    component:Home,
    meta:{isAuth:true},
    beforeEnter(...){...}
}

beforeEnter(to,from,next){
	console.log('beforeEnter',to,from)
	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
		if(localStorage.getItem('school') === 'atguigu'){
			next()
		}else{
			alert('暂无权限查看')
			// next({name:'guanyu'})
		}
	}else{
		next()
	}
}
```

#### 8.12.3 组件内守卫

```js
//进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) {
},
//离开守卫：通过路由规则，离开该组件时被调用
beforeRouteLeave (to, from, next) {
}
```

### 8.13 路由器的两种工作模式

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。
2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。
3. hash模式：
   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。
4. history模式：
   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

```js
//在router里修改
const router = new VueRouter({
    mode:'history',//默认hash
    routes:[......]
})
```

``npm run build``打包 npm i connect-history-api-fallback 后端解决history刷新问题

1. npm init
2. 输入名字
3. npm i express 安装
4. 创建server.js

# Vue3

## 1 创建Vue3.0工程

1. 使用vue-cli创建

   ```
   ## 创建
   vue create vue_test
   ## 启动
   npm run serve
   ```

2. 使用vite创建 vite官网：[https://vitejs.cn](https://vitejs.cn/)

什么是vite————新一代前端开发与构建工具 

优势：

````
1. 开发环境中，无需打包操作，可快速的冷启动。

2. 轻量快速的热重载（HMR）

3. 真正的按需编译，不再等待整个应用编译完成。

```
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```
````

## 2 常用Composition API

### 2.1 setup

1. 理解：Vue3.0中的一个新的配置项，值为一个函数。
2. setup是所有Composition API(组合API)"表演的舞台"。
3. 组件中所用到的：数据、方法等等，均要配置在setup中。
4. setup函数的两种返回值：
   1. 若返回一个对象，则对象中的属性、方法，在模板中均可以直接使用。（重点关注！）
   2. 若返回一个渲染函数：则可以自定义渲染内容（了解）
5. 注意点：
   1. 尽量不要与Vue2.x配置混用
      - Vue2.x配置(data、methods、computed...)中可以访问到setup中的属性、方法。
      - 但在setup中 不能访问到Vue2.x配置(data、methods、computed...)
      - 如果有重名，setup优先
   2. setup不能是一个async函数，因为返回值不再是return的对象，而是promise，模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要Suspense和异步组件的配合）

```js
export default{
	name:'App',
    setup(){
        let name = 'Jerry'
        function sayHello(){...}
        //返回一个对象
        return {
            name,
            sayHello
        }
        //返回一个函数
        return ()=> h('h1','你好')
    }
}
```

### 2.2 ref函数

1. 作用：定义一个响应式的数据
2. 语法： `const xxx = ref(initValue)`
   - 创建一个包含**响应式数据**的引用对象（reference对象，简称ref对象）
   - JS中操作数据：`xxx.value`
   - 模板中读取数据：不需要`.value`,直接 `<div>{{xxx}}</div>`
3. 备注：
   - 接收到的数据可以是：基本类型、也可以是对象类型。
   - 基本类型的数据：响应式依然是靠`Object.defineProperty()`的`get`与`set`完成。
   - 对象类型的数据：内部“求助”了Vue3.0中的一个新函数——reactive函数（借助ES6中的Proxy函数，将Proxy函数操作封装在reactive函数中）。

```js
setup(){
    let friend = ref({
        name:'Jerry',
        age:18
    })
    function changeInfo(){
        friend.value.name = 'Jack'//需要先.value
    }
    return {
        friend,
        changeInfo
    }
}
```

### 2.3 reactive函数

1. 作用：定义一个对象类型的响应式数据（基本类型不要用它，要用`ref`函数）
2. 语法：`const 代理对象 = reactive(源对象)`接收一个对象（或数组），返回一个代理对象（Proxy的实例对象，简称proxy对象）
3. reactive定义的响应式数据是深层次的。
4. 内部基于ES6的 Proxy 实现，通过代理对象操作源对象内部数据进行操作。

### 2.4 Vue的响应式原理

#### 2.4.1 Vue2.x的响应式

- 实现原理：

  - 对象类型：通过`Object.defineProperty()`对对象的已有属性值的读取、修改进行拦截（**数据劫持**）
  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

  ```js
  let data = {}
  Object.defineProperty(data,'count',{
      get() {},
      set(value) {}
  })
  ```

- 存在问题：

  - 新增属性、删除属性，界面不会更新。

    ``this.$set(this.person,'sex','女')``解决

    ``this.$delect(this.person.'sex')``解决

  - 直接通过下标修改数组，界面不会自动更新。

#### 2.4.2 Vue3.0的响应式

- 实现原理：
  - 通过Proxy（代理）：拦截对象中任意属性的变化，包括：属性值的读写、属性的添加、属性的删除等。
  - 通过Reflect（反射）：对源对象的属性进行操作。
  - MDN文档中描述的Proxy与Reflect：
    - Proxy: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy
    - Reflect: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

```js
new Proxy(person,{
    //拦截读取属性值 target为目标对象，propName为属性名
    get(target,propNmae){
        return Reflect.get(target,propName)
    }
    //拦截 修改属性值 或 添加属性
    set(target,propName,value){
        return Reflect.set(target,propName,value)
    }
    //拦截删除属性
    deleteProperty(target,propName){
        return Reflect.deleteProperty(target,propName)
    }
})

proxy.name = 'tom'
```

```js
//通过Reflect.defineProperty操作，操作失败也不会影响下面的代码
const x1 = Reflect.defineProperty(obj,'c',{
    get(){
        return 3
    }
})
console.log(x1) //返回的为布尔值 true
```

### 2.5 reactive与ref

- 从定义数据角度对比：
  - ref用来定义：基本类型数据
  - reactive用来定义：对象（或数组）类型数据
  - 备注：ref也可以用来定义对象（或数组）类型数据，它内部会自动通过`reactive`转为代理对象
- 从原理角度对比：
  - ref通过`Object.defineProperty()`的`get`与`set`来实现响应式（数据劫持）
  - reactive通过使用Proxy来实现响应式（数据劫持），并通过Reflect操作源对象内部的数据
- 从使用角度对比：
  - ref定义的数据：操作数据需要`.value`，读取数据时模板中直接读取不需要`.value`
  - reactive定义的数据：操作数据与读取数据：都不需要`.value`

### 2.6 setup的两个注意点

- setup执行的时机
  - 在beforeCreate之前执行一次，this是undefined
- setup的参数
  - props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性
  - context：上下文对象
    - attrs：值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性，相当于vue2.x中的`this.$attrs`
    - slots：收到的插槽内容，相当于vue2.x中的`this.$slots`
    - emit：分发自定义事件的函数，相当于`this.$emit`

```js
props:['msg','school']
emits:['hello']//不声明接收则警告
setup(props,context){
    ......
    function test(){
        context.emit('hello',666)//触发父组件传过来的自定义事件
    }
}
```

### 2.7 计算属性与监视

#### 2.7.1 computed函数

与vue2.x中computed配置功能一致

```js
import {computed} from 'vue'

setup(){
    ...
    //计算属性——简写，没有考虑计算属性被修改的情况
    let fullName = computed(()=>{
        return person.firstName + '-' + person.lastName
    })
    //计算属性——完整
    let fullName = computed({
        get(){
            return person.firstName + '-' + person.lastName
        }
        set(value){
            const nameArr = value.split('-')
            person.firstName = nameArr[0]
            person.lastName = nameArr[1]
        }
    })
}
```

#### 2.7.2 watch函数

- 与Vue2.x中watch配置功能一致
  - vue2中只能写一个watch配置项，但在vue3中可以调用多个watch函数
- 两个小“坑”：
  - 监视reactive定义的**响应式数据**时：oldValue无法正确获取、强制开启了深度监视（deep配置失效）
  - 监视reactive定义的响应式数据中**某个属性**时：deep配置有效

```js
//情况一：监视ref定义的响应式数据
watch(sum,(dewValue,oldValue)=>{
    console.log('sum变化了',newValue,oldValue)
},{immediate:true})

//情况二：监视多个ref定义的响应式数据
watch([sum,msg],(newValue,oldValue)=>{
    console.log('sum或msg变化了',newValue,oldValue)
}) //newValue...输出为数组

// 情况三：监视reactive定义的响应式数据
watch(person,(newValue,oldValue)=>{
    console.log('person变化了'newValue,oldValue)
},{immeduate:true,deep:false}) //此时的deep配置项不再奏效

//情况四：监视reactive定义的响应式数据中某个属性
watch(()=>person.job,(newValue,oldValue)=>{
    console.log('person的job变化了'newValue,oldValue)
},{immeduate:true,deep:true})

//情况五：监视reactive定义的响应式数据中的某些属性
watch([()=>person.name,()=>person.age],(newValue,oldValue)=>{
    console.log('person的name或age变化了',newValue,oldValue)
})

//特殊情况
watch(()=>person.job,(newValue,oldValue)=>{
    console.log('person的job变化了',newValue,oldValue)
},{deep:true}) //此处由于监视的是reactive素定义的对象中的某个属性（依然是对象），所以deep配置有效
```

> watch监视ref定义的对象时，
>
> 1.如下，监测的对象是reactived生成的Proxy对象，默认开启深度监视
>
> ```js
> watch(person.value,()=>{...})
> ```
>
> 2.如下，我们用代码开启深度监视
>
> ```js
> watch(person,()=>{},{deep:true})
> ```

#### 2.7.3 watchEffect函数

- watch的套路是：既要指明监视的属性，也要指明监视的回调。
- watchEffect的套路是： 不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。
- watchEffect有点像computed：
  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值
  - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

```js
//watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
//默认开启immediate:true
watchEffect(()=>{
    const x1 = sum.value
    const x2 = person.age
    console.log('watchEffect配置的回调执行了')
})
```

### 2.8 生命周期

- Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有两个被更名：
  - `beforeDestory`改名为`beforeUnmount`
  - `destoryed`改名为`unmounted`
- Vue3.0也提供了Composition API形式（要引用，写在setup里）的生命周期钩子，与生命周期钩子(以配置项形式使用)对应关系如下：
  - `beforeCreate` ==> `setup()`
  - `created` ==> `setup()`
  - `beforeMount`==>`onBeforeMount`
  - `mounted`==>`onMounted`
  - `deforeUpdate`==>`onBeforeUpdate`
  - `updated`==>`onUpdated`
  - `deforeUnmount`==>`onBeforeUnmount`
  - `unmounted`==>`onUnmounted`

![lifecycle.16e4c08e](D:\Temp\VSCodework\test1\md分析文档\assets\lifecycle.16e4c08e.png)

### 2.9 自定义hook函数

- 什么是hook？————本质是一个函数，把setup函数中使用的Composition API进行了封装。
- 类似于vue2.x中的mixin
- 自定义hook的优势：复用代码，让setup中的逻辑更清楚易懂。

```js
//hooks/usePoint.js
import {reactive,onMounted,onBeforeUnmount} from 'vue'
export default function (){
	//实现鼠标“打点”相关的数据
	let point = reactive({
		x:0,
		y:0
	})

	//实现鼠标“打点”相关的方法
	function savePoint(event){
		point.x = event.pageX
		point.y = event.pageY
		console.log(event.pageX,event.pageY)
	}

	//实现鼠标“打点”相关的生命周期钩子
	onMounted(()=>{
		window.addEventListener('click',savePoint)
	})

	onBeforeUnmount(()=>{
		window.removeEventListener('click',savePoint)
	})

	return point
}
```

### 2.10 toRef

- 作用：创建一个ref对象，其value值指向另一个对象中的某个属性值。
- 语法：`const name = toRef(person,'name')`
- 应用：要将响应式对象中的某个属性单独提供给外部使用时。
- 扩展：`toRefs`与`toRef`功能一致，但可以批量创建多个ref对象（仅一层），语法：`toRefs(person)`
  - 引用和指针的区别：引用声明的时候必须初始化，因为它自己和引用对象是同一地址，指针可以不初始化（野指针），因为它自己有地址，但是他的内容是别人的地址，toRef更像指针

```js
setup(){
    let person = reactive({
        name:'Jerry',
        age:18,
        job:{
            salary:20
        }
    })
    return {
        name:toRef(person,'name'),
        age:toRef(person,'age'),
        salary:toRef(person.job,'salary'),
        //toRefs可以处理多个
        ...toRefs(person)
    }
}
```

## 3 其他Composition API

### 3.1 shallowReactive与shallowRef

- shallowReactive：只处理对象最外层（第一层）属性的响应式（浅响应式）。
- shallowRef：只处理基本数据类型的响应式，**不进行**对象的**响应式**处理。（ref在处理对象的响应式时，会求助reactive）
- 什么时候使用？
  - 如果有一个对象数据，结构比较深，但变化时只是外层属性变化====>shallowReactive
  - 如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换====>shallowRef

### 3.2 readonly与shallowReadonly

- readonly：让一个响应式数据变为只读的（深只读）
- shallowReadonly：让一个响应式数据变为只读的（浅只读，只最外层一层只读）
- 应用场景：不希望数据被修改时。

```js
person = readonly(person) //覆盖
person = shallowReadonly(person)
```

### 3.3 toRaw与markRaw

- toRaw：
  - 作用：将一个由`reactive`生成的**响应式数据**转为**普通对象**。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw：
  - 作用：标记一个对象，使其永远不会再成为响应式对象。
  - 应用场景：
    - 有些值不应被设置为响应式的，例如复杂的第三方类库等。
    - 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

```js
const p = toRaw(person)
```

### 3.4 customRef

作用：创建一个自定义的ref，并对其依赖项跟踪和更新触发进行显式控制。

实现防抖效果:

```js

setup() {
    //自定义一个ref——名为：myRef
    function myRef(value,delay){
        let timer 
        //第一个return是将写的自定义ref交(return)出去
        return customRef((track,trigger)=>{
            //第二个return是语法要求，返回一个对象
            return {
                get(){
                    console.log(`有人从myRef这个容器中读取数据了，我把${value}给他了`)
                    track() //通知Vue追踪value的变化（提前和get商量一下，让他认为这个value是有用的）
                    return value
                },
                set(newValue){
                    console.log(`有人把myRef这个容器中数据改为了：${newValue}`)
                    clearTimeout(timer)  //实现防抖，在每次修改前清掉已开启的定时器
                    timer = setTimeout(()=>{
                        value = newValue
                        trigger() //通知Vue去重新解析模板
                    },delay)
                },
            }
        })
    }

    // let keyWord = ref('hello') //使用Vue提供的ref
    let keyWord = myRef('hello',500) //使用程序员自定义的ref

    return {keyWord}
}

```

### 3.5 provide与inject

作用：实现**祖与后代**组件间通信

套路：祖组件有一个`provide`选项来提供数据，子组件有一个`inject`选项来开始使用这些数据（父组件也可以inject到，但我们一般不用这个方式）

```js
//祖组件中
setup(){
    ......
    let car = reactive({name:'奔驰',price:'40万'})
    provide('car',car) //给自己的后代组件传递数据
    ......
}
    
//孙组件中：
setup(){
    ......
    const car = inject('car')
    return {car}
    ......
}
```

### 3.6 响应式数据的判断

- isRef：检查一个值是否为一个ref对象
- isReactive：检查一个对象是否由`reactive`创建的响应式代理
- isReadonly：检查一个对象是否由`readonly`创建的只读代理
- isPoxy：检查一个对象是否是由`reactive`或者`readonly`方法创建的代理

## 4 Composition API的优势

### 4.1 Options API存在的问题

使用传统OptionsAPI中，新增或者修改一个需求，就需要分别在data，methods，computed里修改。

### 4.2 Composition API的优势

我们可以更加优雅地组织我们的代码、函数，让相关功能的代码更加有序地组织在一起。

## 5 新的组件

### 5.1 Fragment

- 在Vue2.x中：组件必须有一个根标签
- 在Vue3中：组件可以没有根标签，内部会将多个标签包含在一个Fragment虚拟元素中
- 好处：减少标签层级，减小内存占用

### 5.2 Teleport

`Teleport`是一种能够将我们的组件html结构移动到指定位置的技术。

```html
<teleport to="body">
    <div v-if="isShow" class="mask">
        <div class="dialong">
            <h3>我是一个弹窗</h3>
            <button @click="isShow = false">关闭弹窗</button>
        </div>
    </div>
</teleport>
```

### 5.3 Suspense

**等待异步组件**时渲染一些额外内容，让应用有更好的用户体验

```js
// import Child from './components/Child'//静态引入组件
import {defineAsyncComponent} from 'vue' 
const Child = defineAsyncComponent(()=>import('./components/Child')) //异步引入组件
```

```html
<Suspense>
    <!--借助slot原理实现，真正要展示的-->
    <template v-slot:default>
        <Child/>
    </template>
    <!--退路——上面的未展示出来时展示-->
    <template v-slot:fallback>
        <h3>稍等，加载中...</h3>
    </template>
</Suspense>
```

## 六、其他

### 6.1 全局API的转移

- Vue2.x有许多全局API和配置

  - 例如：注册全局组件、注册全局指令等。

  ```js
  //注册全局组件
  Vue.component('MyButton',{
      data:() => ({
          count: 0
      }),
      template: '<button @click="count++">Clicked {{count}} times.</button> '
  })
  
  //注册全局指令
  Vue.directive('focus',{
      inserted: el => el.focus()
  })
  ```

- Vue3中对这些API做出了调整：

  - 将全局的API，即：`Vue.xxx`调整到应用实例（`app`）上

| 2.x全局API(Vue)          | 3.x实例API(app)             |
| ------------------------ | --------------------------- |
| Vue.config.xxx           | app.config.xxx              |
| Vue.config.productionTip | 移除                        |
| Vue.component            | app.component               |
| Vue.directive            | app.directive               |
| Vue.prototype            | app.config.globalProperties |
| Vue.use                  | app.use                     |
| Vue.mixin                | app.mixin                   |

### 6.2 其他改变

- data选项应始终被声明为一个函数：防止组件在被复用时候产生数据关联关系

- 过度类名的更改：

  - Vue2.x写法

  ```css
  .v-enter,
  .v-leave-to{
      opacity: 0;
  }
  .v-leave,
  .v-leave-to{
      opacity: 1;
  }
  ```

  - Vue3.x写法

  ```css
  .v-enter-from,
  .v-leave-to {
      opacity: 0;
  }
  .v-leave-from,
  .v-enter-to {
      opacity: 1;
  }
  ```

- 移除keyCode作为v-on的修饰符，同时也不再支持`config.keycodes`

- 移除`v-on.native`修饰符

  - 父组件中绑定事件

  ```js
  <my-component
      v-on:close="handleComponentEvent"
      v-on:click="handleNativeClickEvent"
  />
  ```

  - 子组件中声明自定义事件

  ```js
  <script>
      export default {
          emits: ['close']//指定未声明的click为原生事件
      }
  </script>
  ```

- 移除过滤器（filter）

  - 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是“只是JavaScript”的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。