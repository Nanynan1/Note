Node.js作为一个JavaScript的运行环境，仅仅提供了基础的功能和API，可以搭配：
- 基于express框架，快速构建Web应用
- 基于Electron框架，构建跨平台的桌面应用
- 基于restify框架，构建API接口项目
- 读写操作数据库、创建实用的命令行工具辅助前端开发、etc...

**学习路径**：
JavaScript基础语法 + Node.js内置API模块(fs, path, http等) + 第三方API模块(express, mysql等)

#### fs模块
用来操作文件的模块
```js
const fs = require('fs') //导入
fs.readFile(path[,options],callback(err,dataStr))
fs.writeFile(path,data[,options],callback) //向指定文件中写入内容，重复使用时，新内容会覆盖旧内容
```
代码在运行时，会以执行node命令时所处的目录，动态拼接出被操作文件的完整路径

``__direname`` 表示当前文件所处的目录，可以使用路径拼接来写路径

#### path模块
path模块是Node.js官方提供的，用来处理路径的模块
 - ``path.join()``方法，用来将多个路径片段拼接成一个完整的路径字符串
 `` ../ ``会抵消前面的路径（一个仅抵消一层）
 - ``path.basename()`` 方法，用来从路径字符串中，将文件名解析出来。
 - ``path.extname()`` 方法，获取路径中的扩展名部分
 - 使用步骤如上所示，导入再使用

#### http模块
在网络节点中，负责消费资源的电脑，叫做客户端；负责对外提供网络资源的电脑，叫做服务端。
``web服务器软件``
通过几行简单的代码，轻松手写一个服务器软件，从而对外提供web服务。
**IP地址**：互联网上每台计算机的唯一地址
格式：``a.b.c.d`` 0~255
**域名、域名服务器**：一个IP可以对应多个域名，但一个域名只能对应一个IP，DNS服务器就是提供IP地址和域名之间的转换服务的服务器

1. 创建最基本的web服务器
```js
const http = require('http')
const server = http.createServer()
// 为服务器实例绑定request事件，监听客户端的请求
server.on('request', (req, res) => {
    console.log('发起请求')
    // 避免中文乱码问题，设置响应头
    res.setHeader('Content-Type', 'text/html; charset=utf-8')
    res.end('发起请求')
})
// 启动服务器
server.listen(80, function(){
    console.log('启动成功')
})
```

#### 模块化
1. 3大类：内置模块、自定义模块、第三方模块

2. ``module.exports``对象
作用：向外共享模块作用域中的成员
```javascript
// 在一个自定义模块中，默认情况下，module.exports = {}
// 向module.exports 对象上挂载要暴露属性
module.exports.username = 'zzz'
```
**注意**：require模块时，得到的永远是 module.exports 指向的对象。

3. 为了简化代码，Node提供了exports对象，默认情况下，exports与module.exports 指向同一个对象。

4. Node.js遵循CommonJS规范

5. 模块的加载机制
    - 内置模块的加载优先级最高
    - 加载自定义模块时，必须指定以``./``或``../``开头的路径标识符；如果没有，则会把它当作内置模块或第三方模块加载。
    - 导入自定义模块时，如果省略了文件的扩展名，则Node.js尝试``.js -> .json -> .node -> 加载失败``
    - 如果不是前两个模块，则尝试从``/node_modules``上加载第三方模块。如果没有，则移动到再上一层父目录进行加载，直到文件系统的根目录。
在用require()进行加载时，先是在被加载目录下查找``package.json``文件，并寻找``main``属性作为加载的入口；如果失败，则试图加载``index.js``文件，都失败则报错。

#### npm与包
包是基于内置模块封装出来的，极大的提高了开发效率
https://www.npmjs.com/ 搜索
https://registry.npmjs.org/ 下载包

包的版本号的语义化版本规范
``大版本.功能版本.Bug修复版本``

1. 包管理配置文件
在执行命令所处的目录中，快速新建 package.json 文件
```npm init -y``

一次性安装所有包：``npm install(npm i)``命令
卸载包：``npm uninstall 包名``

如果某些包在开发和项目上线之后都需要用到，将包记录到dependencies节点中。
如果某些包只在项目开发阶段使用，项目上线之后不会用到，则记录到 devDependencies 节点中：
``npm i 包名 -D``
是 ``npm install 包名 --save-dev`` 的简写

2. nrm工具
更方便地切换下载包的镜像源
```js
//将 nrm 安装为全局可用的工具
npm i nrm -g
//查看所有可用的镜像源
nrm ls
nrm use taobao
```

3. 包的分类
 - 项目包
 - 全局包，被安装到``C:\Users\86180\AppData\Roaming\npm\node_modules`` 路径下，安装卸载时记得加 ``-g``

4. i5ting_toc工具
可以把md文档转为html页面的小工具，使用步骤如下：
```
npm install -g i5ting_toc
i5ting_toc -f 要转换的md文档路径 -o 
```

5. 开发属于自己的包
（1）新建文件夹，作为包的根目录
（2）在文件夹中，新建如下三个文件：
    - package.json（包管理配置文件）
    - index.js（包的入口文件）
    - README.md（包的说明文档）

6. 发布包
（1）终端中登录npm账号：```npm login```
**注意**：登陆之前，必须先把下包的服务器地址切换到npm的官方服务器。
（2）把终端切换到包的根目录之后，执行``npm publish``命令，发布包（包名不能相同）
（3）``npm unpublish 包名 --force``，从npm删除已发布的包（72小时以内发布的）

#### Express
1. 简介
Express是基于Node.js平台，快速、开放、极简的Web开发框架，其作用与Node.js内置模块http模块类似，是专门用来创建Web服务器的

2. 基本使用
（1）安装``npm i express@4.17.1``
（2）创建基本的Web服务器
```js
const express = require('express')
const app = express()
// const router = express.Router()

app.get('/user', (req,res) => {
    res.send({name:'aa', age:20})
})
//post同理

// 获取URL（请求头）中携带的查询参数
app.get('/', (req,res) => {
    console.log(req.query) //默认一个空对象
})

// 获取URL中的动态参数
app.get('/user/:id',(req,res) => {
    concole.log(req.params) //默认一个空对象
    // req.params.id
    //可以匹配到多个动态参数
})

// 启动服务器
app.listen(80, () => {
    console.log('启动成功')
})
```

3. 托管静态资源
``express.static()``创建一个静态资源服务器
```app.use(express.static('public'))```
可以通过``http://localhost:3000/images/bg.jpg``访问，存放静态文件的目录名不会出现在URL中

4. nodemon
可以监听项目文件的变动，自动帮我们重启项目
``npm install -g nodemon``
``node app.js => nodemon app.js``

5. 路由
在Express中，路由指的是客户端的请求与服务器处理函数之间的映射关系，路由由3部分组成：
    - 请求的类型
    - 请求的URL地址
    - 处理函数
``app.METHOD(PATH, HANDLER)``
（1）模块化路由
``express.Router()``创建路由对象
（2）``app.use()``函数的作用，就是来注册全局中间件的

6. 中间件
中间件，特指业务流程的中间处理环节；Express的中间件，本质上就是一个function处理函数：
```js
app.get('/', function(req, res, next){
    next(); //中间件函数的形参列表中必须包含next参数
})
```
``next()``函数是实现多个中间件连续调用的关键，它表示把流转关系转交给下一个中间件或路由。

（1）全局生效的中间件
``app.use(中间件函数)``
（2）中间件的作用
多个中间件之间，共享同一份req和res，基于这样的特性，我们可以在上游的中间件中，统一为req和res对象添加自定义的属性和方法，供下游的中间件或路由进行使用。
（3）局部生效的中间件
不使用``app.use()``定义的中间件，就是局部生效的中间件。
```js
// mv1这个中间件只在当前路由中生效
app.get('/',mv1,function(req,res){
    res.send('Home page.')
})
```
（4）定义多个局部中间件：如上为第一种写法，
第二种：``app/get('/',[mv1, mv2],function(req,res){})``
两种方式等价

7. 中间件的分类
- 应用级别的中间件：通过``app.use() | app.get() | app.post()``绑定到app实例上的中间件
- 路由级别：绑定到``express.Router()``实例上的
- 错误级别：必须有4个形参：``(err, req, res, next)``
作用：专门用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题。
**注意**：错误级别的中间件，必须注册在所有路由之后。
- Express内置中间件
3个常用的：
    - express.static 快速托管静态资源
    - express.json 解析JSON格式的请求体数据
    - express.urlencoded 解析URL-encoded 格式的请求体数据
    ```js
    app.use(express.json())
    app.use(express.urlencoded({cextended: falsec}))
    ```
- 第三方的中间件

8. 自定义中间件
Node.js内置了一个querystring模块，专门用来处理查询字符串。通过这个模块的parse()函数，可以把查询字符串解析成对象的格式。
```js
const qs = require(querystring)
const body = qs.parse(str)
```

