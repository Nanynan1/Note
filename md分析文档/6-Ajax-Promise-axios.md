## AJAX-Promise-axios

## 1 Ajax概述

### 1.1 Ajax简介

AJAX全称为Asynchronous JavaScript And XML，就是异步的JS和XML。通过AJAX可以在浏览器中向服务器发送异步请求，最大优势：**无刷新获取数据**

### 1.2 XML简介

XML可扩展标记语言，被设计用来传输和存储数据。

XML和HTML类似，不同的是HTML中都是预定义标签，而XML中没有预定义标签，全都是自定义标签，用来表示一些数据。

### 1.3 AJAX请求传递参数

1. query
2. params
3. body
   - urlencode
   - json

### 1.4 AJAX的优缺点

#### 1.3.1 优点

1. 可以无需刷新页面而与服务器端进行通信
2. 允许你根据用户事件来更新部分页面内容

#### 1.3.2 缺点

1. 没有浏览历史，不能回退
2. 存在跨域问题
3. SEO（搜索引擎优化）不友好

## 2 HTTP

超文本传输协议，协议详细规定了浏览器和万维网服务器之间互相通信的规则。

### 2.1 MDN文档

[HTTP | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)

### 2.2 HTTP请求交互的基本过程

1. 浏览器端向服务器发送HTTP 请求(请求报文)
2. 后台服务器接收到请求后, 调度服务器应用处理请求, 向浏览器端返回HTTP响应(响应报文)
3. 浏览器接收到响应, 解析显示响应体/调用监视回调

### 2.3 报文格式与参数

- 行

- 头：Host: value

  ​				Cookie: value

  ​				Content-type: value

  ​				User-Agent: value

- 空行

- 体：Get请求的请求体为空，Post请求的请求体可以不为空

### 2.4 常见响应状态码

- 200 OK：请求成功
- 2.1 Created：已创建
- 401 Unauthorized：未授权/请求需要用户的身份认证
- 404 Not Found：无法找到资源
- 500 Internal Server Error：服务器内部错误，无法完成请求

## 3 原生AJAX的基本使用XHR

### 3.1 核心

1. 使用``XMLHttpRequest``（XHR）对象可以与服务器交互，也就是发送ajax请求
2. 前端可以获取到数据，且无需整个页面刷新
3. 使得Web网页可以只更新页面的局部，而不影响用户的操作
4. [XMLHttpRequest - Web API 接口参考 | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)AJAX的所有操作都是通过该对象进行

### 3.2 Get请求

```js
btn.onclick = function(){
    // 1.创建对象
    const xhr = new XMLHttpRequest();
    // 2.初始化 设置请求方式和url
    xhr.open('GET', url)
    // 3.发送
    xhr.send()
    // 4.事件绑定，处理服务端返回的结果
    xhr.onreadystatechange = function(){
        // readyState 是 xhr 对象中的属性，表示状态0 1 2 3 4
   		if(xhr.readyState === 4){
          // 判断响应状态码 200  404  403 401 500
          if(xhr.status >= 200 && xhr.status < 300){
              // 处理结果
          }
        }     
    }
}
```

设置url参数

```js
xhr.open('GET','http://127.0.0.1:8000/server?a=100&b=200&c=300')
```

数据：

- ``xhr.status`` 状态码
- ``xhr.statusText`` 状态字符串
- ``xhr.getAllResponseHeaders()`` 所有响应头
- ``xhr.response`` 响应体

readyState状态：

> 0：代理被创建，但尚未调用open()方法
>
> 1：open() 方法已经被调用
>
> 2：send()方法已经被调用，并且头部和状态已经可获取
>
> 3：下载中，responseText属性已经包含部分数据
>
> 4：下载操作已经完成

### 3.3 POST请求

与GET不同的是：

```js
// 设置请求参数
xhr.send('a=100&b=200&c=300')
```

### 3.4 json数据请求

服务端server.js

```js
app.all('/json-server', (request, response) => {
  // 设置响应头, 设置允许跨域
  response.setHeader('Access-Control-Allow-Origin', '*');
  // 设置响应头, 设置允许自定义头信息
  response.setHeader('Access-Control-Allow-Headers', '*');
  // 响应一个数据
  const data = {
    name: 'atguigu'
  };
  // 对对象进行字符串转换
  let str = JSON.stringify(data)
  // 设置响应体 
  response.send(str);
});
```

同时前端代码：

```js
// 绑定键盘按下事件
window.onkeydown = function(){
  const xhr = new XMLHttpRequest();
  // *2*.(自动转换) 设置响应体数据的类型(自动转换)
  xhr.responseType = 'json';
  xhr.open('GET', 'http://127.0.0.1:8000/json-server');
  // 发送
  xhr.send();
  // 事件绑定
  xhr.onreadystatechange = function(){...}
```

### 3.5 处理

#### 3.5.1 请求超时与网络异常

```js
// 超时设置 （2秒）
xhr.timeout = 2000;
// 超时回调
xhr.ontimeout = function(){
	alert('网络超时，请稍后重试')
}
// 网络异常回调
xhr.onerror = function(){
	alert('网络异常，请稍后重试')
}
```

#### 3.5.2 取消请求

```js
// 手动取消
xhr.abort()
```

## 4 jQuery中的AJAX

### 4.1 请求

```js
$.get(url,[data],[callback],[type])
```

- data: 请求携带的参数
- callback：载入成功时回调函数
- type：设置返回内容格式，xml,html,script,json,text,_default

### 4.2 通用方法

```js
$.ajax({
	url: 'http://127.0.0.1:8000/jquery-server',
	// 参数
	data: {a:100, b:200},
	// 请求类型
	type: 'GET',
	// 响应体结果
	dataType: 'json',
	success: function(data){console.log(data);},
	timeout: 2000,
	error: function(){console.log('出错');},
	// 头信息
	headers: {
		c: 300,
		d: 400
	}	
})
```

## 5 Promise学习

### 5.1 Promise是什么

#### 5.1.1 理解

1. Promise是JS中进行异步编程的新解决方案；
2. 从语法上来说：promise是一个构造函数
3. 从功能上来说：promise对象用来封装一个异步操作并且开源获取其成功/失败的结果值

> 旧方案是单纯使用回调函数

#### 5.1.2 Promise的状态

PromiseState: ``pending -> resolved/ fullfilled``成功

​							``pending -> rejected``失败

注意：

- 对象的状态不受外界影响
- 状态改变只有这两种，且一个Promise对象只能改变一次
- 一旦状态改变，就不会再变，任何时候都可以得到这个结果
- 无论成功还是失败，都会有一个结果数据。(value  / reason)保存在``PromiseResult``中

#### 5.1.3 Promise的基本使用

```js
const promise = new Promise((resolve, reject) => {
    if(/*异步操作成功*/){
        resolve(value);
    }else {
        reject(reason);
    }
});
```

- resolve函数：将``Promise``对象的状态“未完成=>成功”，并将异步操作的结果作为参数``value``传递出来

- reject函数：将``Promise``对象的状态“未完成=>失败”，并将异步操作报出的错误作为参数``error/reason``传递出来

- Promise实例生成以后，可以用``then``方法分别指定`resolved`状态和`rejected`状态的回调函数。

  ```js
  // promise.then(successCallback, failureCallback)
  promise.then(
  	value => {
          //success
      },
      reason => {
          //failure
      }
  )
  ```

### 5.2 为什么要用Promise

#### 5.2.1 指定回调函数的方式更加灵活

1. 旧的：回调函数必须在执行异步任务前指定
2. promise：启动异步任务 => 返货promise对象 => 给promise对象绑定回调函数（甚至可以在异步任务结束后指定多个）

#### 5.2.2 支持链式调用，可以解决回调地狱问题

什么是回调地狱？

​		回调地狱嵌套调用，外部回调函数异步执行的结果是嵌套的回调执行的条件。（即一层层的嵌套回调函数）

**解决方案**：

- promise 链式调用

  ```js
  doSomething()
    .then(result => doSomethingElse(result))
    .then(newResult => doThirdThing(newResult))
    .then(finalResult => {
      console.log('Got the final result:' + finalResult)
    })
    .catch(failureCallback)
  
  ```

- 终极解决：async/ await

  ```js
  async function request(){
      try{
          const result = await doSomething()
          const newResult = await doSomethingElse(result)
          const finalResult = await doThirdThing(newResult)
          console.log('Got the final result:' + finalResult)
      } catch(error){
          failureCallback(error)
      }
  }
  ```

### 5.3 如何使用Promise

#### 5.3.1 Promise构造函数

``Promise(executor)``：executor函数同步执行``(resolve,reject) => {}``

说明：``executor``是执行器，会在``Promise``内部立即同步回调，异步操作``resolve``/``reject``就在``executor``中执行

#### 5.3.2 Promise.prototype.then方法

``p.then(inResolved, onRejected)`` 指定两个回调（成功 失败）

#### 5.3.3 Promise.prototype.catch方法

``p.catch(onRejected)`` 指定失败的回调

```js
new Promise((resolve, reject) => { // excutor执行器函数
 setTimeout(() => {
   if(...) {
     resolve('成功的数据') // resolve()函数
   } else { 
     reject('失败的数据') //reject()函数
    }
 }, 1000)
}).then(
	value => { // onResolved()函数
 	console.log(value) // 成功的数据
}
).catch(
	reason => { // onRejected()函数
 	console.log(reason) // 失败的数据
}
)
```

#### 5.3.4 Promise.resolve方法

``Promise.resolve(value)`` 返回一个带着给定值解析过的Promise对象，如果参数本身就是一个Promise对象，则直接返回这个Promise对象。

- 如果value为非Promise类型的对象，返回状态为resolved的promise对象
- 如果传入参数为Promise对象，则参数的状态决定返回的结果

#### 5.3.5 Promise.reject方法

``Promise.reject(reason)`` 返回一个失败的Promise对象（其状态为rejected)

#### 5.3.6 Promise.all方法

``Promise.all(iterable)`` 返回一个新的Promise对象，只有所有的``promise``都resolved才resolved，只要有一个rejected就直接rejected

其中，``iterable`` 包含n个promise的可迭代对象，如Array或String

```js
let p1 = new Promise((resolve, reject) => {
	resolve('OK');
})
let p2 = Promise.resolve('Success');
let p3 = Promise.resolve('Oh Yeah');
const result = Promise.all([p1, p2, p3]);

```

#### 5.3.7 Promise.race方法

``Promise.race(iterable)`` 返回一个新的Promise对象，iterable中首先完成的promise的结果状态为最终新对象的状态。

```js
const pRace = Promise.race([p1, p2, p3])
// 谁先完成就输出谁(不管是成功还是失败)
const p1 = new Promise((resolve, reject) => {
	setTimeout(() => {
 		resolve(1)
 }, 1000)
})
const p2 = Promise.resolve(2)
const p3 = Promise.reject(3)

pRace.then(
    value => {
        console.log('race onResolved()', value)
     },
    reason => {
       console.log('race onRejected()', reason) 
     }
)
//race onResolved() 2
```

### 5.4 Promise的几个关键问题

#### 5.4.1 如何改变promise的状态

1. ``resolve(value)`` : padding -> resolved

2. ``reject(reason)`` : padding -> rejected

3. 抛出异常：padding -> rejected

   ```js
   const p = new Promise((resolve, reject) => {
   	throw new Error('出错了') // 抛出异常，promise变为rejected失败状态，reason为抛出的error
   })
   p.then(
   	value => {},
   	reason => {console.log('reason',reason)}
   )
   // reason Error:出错了
   ```

#### 5.4.2 一个promise指定多个成功/失败回调函数，都会调用吗

当 peomise **改变**为对应状态时都会调用

```js
const p = new Promise((resolve, reject) => {
	reject(2)
})
p.then(
	value => {},
	reason => {console.log('reason1',reason)}
)
p.then(
	value => {},
	reason => {console.log('reason2',reason)}
)
// reason1 2
// reason2 2

```

#### 5.4.3 改变promise状态和指定回调函数 谁先谁后

都有可能。

如果要后改变状态，可以使用定时器等。

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(1)
    },1000)
}).then(
	value => {},
    reason => {
        console.log("reason",reason)
    }
)
```

#### 5.4.4 promise.then()返回的新promise的结果状态由什么决定

1. 简单表达：由``then()`` 指定的回调函数执行的结果决定

2. 详细表达：

   1. 如果抛出异常，新promise状态为``rejected`` ,``reason`` 为抛出的异常

      ```js
      let p = new Promise((resolve, reject) => {
      	resolve('ok');
      });
      let result = p.then(value => {
      	// 抛出错误
      	throw '出了问题';
      }, reason => {
      	console.warn(reason);
      });
      // rejected
      ```

   2. 如果返回的是非promise的任意值，新promise变为``resolved``

      ```js
      let p = new Promise((resolve, reject) => {
      	resolve('ok');
      });
      let result = p.then(value => {
      	return 987;
      }, reason => {
      	console.warn(reason);
      });
      // resolved
      ```

   3. 如果返回的是另一个promise，则此promise的结果成为新promise的结果

      ```js
      new Promise((resolve, reject) => {
      	resolve(1)
      }).then(
      	value => {
          	console.log('onResolved1()', value)
        },
          reason => {
           	console.log('onRejected1()', reason)
        }
      ).then(
        	value => {
          	console.log('onResolved2()', value)
        },
        	reason => {
          	console.log('onRejected2()', reason)
        }
      )
      // onResolved1() 1
      // onResolved2() undefined
      
      ```

> ### Promise链式调用的两个注意点
>
> 1. 上一次``.then()`` 处理的数据变成undefined，如上
>
>    此处的undefined是由于，前一个``.then()`` 没有显式地使用``return`` 返回数据，在Promise内部相当于只写了return 。因此，在下一次的链式调用时，后续``.then()`` 的``onResolved`` 就只能拿到undefined了。
>
>    若想要在链式调用中让return结果一直传递下去，就必须在处理函数中显式地返回数据，否则数据会丢失。
>
>    ```js
>    .then(
>        (data) => {
>            console.log('onResolved1', data)
>            // 情况1: 返回固定值
>            return 'success'；
>            // 情况2: 返回promise
>            return new Promise(resolve => {
>                resolve(data)
>            })；
>        }
>    )
>    .then((data) => { console.log('onResolved2', data)})
>    ```
>
> 2. onResolved 和 onRejected 的执行时机
>
>    ```js
>    const p = new Promise((resolve, reject) => {
>      reject('失败了')
>    }).then(
>      (data) => { console.log('onResolved1', data)},
>      (error) => {
>        // 场景1: 没有 return
>        console.log('onRejected1', error)
>        // 场景2: return 固定值
>        return 'failed'
>        // 场景3: return Promise.reject
>        return Promise.reject(error)
>        // 场景4: return Promise.resolve
>        return Promise.resolve(error)
>        // 场景5: 抛出错误:
>        throw error;
>      }
>    )
>    p.then(
>      (data) => { console.log('onResolved2', data)},
>      (error) => { console.log('onRejected2', error)}
>    )
>          
>    ```
>
>    发现：
>
>    | 上一个.then中onResolved 的返回值 | 下一个.then的执行结果 |
>    | -------------------------------- | --------------------- |
>    | return undefined                 | 执行 onResolved       |
>    | return 固定值                    | 执行 onResolved       |
>    | return Promise.resolve           | 执行 onResoled        |
>    | return Promise.rejecte           | 执行 onRejected       |
>    | throw 数据                       | 执行 onRejected       |
>
>    上一个``.then()`` 中回调函数返回不同的结果，会决定下一个``.then()``
>
>    执行的回调。即：<u>和上一个``.then()`` 执行哪个回调无关，只和返回值有关</u> 

#### 5.4.5 promise如何串联多个操作任务

1. promise的``then()`` 返回一个新的promise，可以并成``then()`` 的链式调用

2. 通过``then`` 的链式调用串联多个同步/异步任务

   ```js
   let p = new Promise((resolve, reject) => {
     setTimeout(() => {
         resolve('OK');
     }, 1000);
   });
   
   p.then(value => {
     return new Promise((resolve, reject) => {
         resolve("success");
     });
   }).then(value => {
     console.log(value); // success
   }).then(value => {
     console.log(value); // undefined
   })
   
   ```

#### 5.4.6 Promise异常穿透

1. 当使用promise的then链式调用时，可以在最后指定失败的回调

2. 前面任何操作出了异常，都会传到最后失败的回调中处理

   ```js
   new Promise((resolve, reject) => {
      //resolve(1)
      reject(1)
   }).then(
     value => {
       console.log('onResolved1()', value)
       return 2
     }
   ).then(
     value => {
       console.log('onResolved2()', value)
       return 3
     }
   ).then(
     value => {
       console.log('onResolved3()', value)
     }
   ).catch(
     reason => {
       console.log('onRejected1()', reason)
     }
   )
   // onRejected1() 1
   
   ```

   ```js
   // 在多重 .then 以及 .catch 一层层调用时，相当于每次 .then 抛出失败的结果reason
   reason => {throw reason}
   // 最后传递到 catch 中处理
   ```

##### .then 对比 .catch

```js
promise.then(f1).catch(f2);
promise.then(f1, f2);
```

如上两个代码片段并不相等。如果f1出现 error，在第一个链式调用中，error 会被catch 捕获，并在f2中被处理，但在第二个链式调用中不会，此时f1和f2处于同一层级，二者只会执行其一。

##### 隐式的 try...catch

一遇到异常抛出，浏览器就会顺着Promise链寻找下一个就近的``onRejected`` 失败回调函数或者由``.catch`` 指定的回调函数，这一错误的传递就被称为 **promise的异常穿透**。

```js
new Promise((resolve, reject) => {
    reject('失败了')
})
    .then(
        (data) => { console.log('onResolved1', data); },
        (error) => { console.log('onRejected2', error); }
    )
    .catch(
        (error) => {
            console.log('catch', error)
        }
    )
// onRejected2 失败了
// error在.then中被处理，不会被catch所捕获
```

##### 异步回调中的错误无法捕获

需要注意的是：异步（比如定时器）中的错误，promise捕获不到

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        throw new Error("Whoops!");
    }, 1000);
})
    .catch(error => {
        console.log('catch error:', error);
    });
```

前面所讲，promise有个隐式的``try...catch`` ，所有**同步错误**都会得到处理。

但这里的错误不是在executor 运行时生成的，是在稍后生成的。相当于，在延时等待1s后，抛出错误才会执行，但此时外面的promise已经执行结束退出主线程了。因此，promise无法处理它，程序会在此崩掉。

##### executor 中的异步错误处理

在executor 执行器中，不管最终拿到什么结果，都使用``resolve`` 或``reject`` 去接收，以便后续的链式调用：

```js
new Promise((resolve, reject) => {
    setTimeout(() => {
        reject(new Error("whoops!"))
    },1000);
})
```

或者也可以先用``try...catch`` 捕获throw出的error，然后用``reject``去接收。

注意：``try...catch`` 必须写在定时器里面，否则捕获不到

```js
new Promise((resolve, reject) => {
	setTimeout(() => {
        try{
            throw new Error("whoops!");
        } catch(error){
            reject(error)
        }
    },1000);
})
```

##### try...catch 不能捕获promise的错误

这里说的``try.catch`` 指的是，想在Promise外部通过使用``try...catch`` 捕获其内部的错误，如下所示：

```js
function fn1() {
    try {
        new Promise((resolve, reject) => {
            throw new Error('promise1 error')
        })
    } catch (error) {
        console.log(e.message);
    }
}

function fn2() {
    try {
        Promise.reject('promise2 error');
    } catch (error) {
        console.log(error);
    }
}
```

上面的两个``try...catch`` 都不能捕获到error，因为promise内部的错误不会冒泡出来，所以在 try..catch 看来，这只是一个Promise，而不是语法错误，至于里面具体是什么，它并不知道。

但是如果非要用``try...catch``？

##### async/ await 解决方案

async/await 很好解决了上面提出的问题，相比于``promise.then`` ，``await`` 只是获取promise的结果的一个语法，且更易于读写。

``async``：在函数前面加上``async``关键字，这个函数的返回结果就会变成一个Promise对象。

``await``：await必须放在async函数里，在await后面接一个函数，来等待这个函数执行完成。

``await promise``：如果 promise 正常 resolve，则返回的是其结果；如果 promise被reject，它就 throw 这个 error。 

```js
//模拟请求
function http(params){
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(params === 0) reject("error")
            resolve("success")
        },1000);
    })
}

// 业务调用
async function query(params){
    try{
        const data = await http(params)
        console.log('data:',data)
    } catch(error){
        console.log('error:' error)
    }
}
```

#### 5.4.7 中断promise链

可以通过返回一个状态一直为``pending`` 的promise结束promise链。如下：

```js
.catch(error){
    console.log('onRejected',error)
    // 中断 promise 链
    return new Promise(() => {})
}
```

#### 5.4.8 promise构造函数是同步还是异步

promise构造函数是同步执行的，then方法是异步执行

```js
//先指定回调函数 ，后改变状态（同时指定数据）
new Promise((resolve,reject)=>{
    setTimeout(()=>{ // 异步执行回调函数
        reject(1)
    },1000)

}).then(
    value=>{},
    reason=>{
        console.log("reason1",reason)
    }
)

 //先改变状态 ，后指定回调函数
 new Promise((resolve,reject)=>{，
        resolve(2)
}).then(
    value=>{console.log("value2",value)},
    reason=>{
        console.log("reason2",reason)
    }
)

//验证resolve,reject都是异步操作
console.log("111111111");

// 依次输出：
// 111111111 -> value2,2 -> reason1,1
```

手写promise：[(40条消息) 【Promise】自定义 - 手写Promise - Promise.all - Promise(executor)_YK菌的博客-CSDN博客](https://blog.csdn.net/weixin_44972008/article/details/114134995)

## 6 axios学习

> axios是一个基于Promise用于浏览器和nodejs的HTTP客户端，它本身具有以下特征：
>
> - 从浏览器中创建XMLHttpRequest
> - 从node.js发出http请求
> - 支持Promise API
> - 拦截请求和响应
> - 转换请求和响应数据
> - 自动转换JSON数据
> - 客户端支持防止CSRF/XSRF

### 6.1 请求配置

```js
{
    url:'/user',
    method: 'get',
    // 浏览器会自动将baseURL与url进行拼接
    baseURL: 'https://some-domain.com/api/',
    transformRequest: [function(data, headers){
       // 对请求信息先进行处理，再发送到服务端。
    }]，
    transformResponse: [function(data){
        // 对返回数据先进行处理
    }],
    headers:{'X-Requested-With': 'XMLHttpRequest'},
    // 设置url参数
    params: {
        ID:12345
    }，
    // 设置url参数格式
    paramsSerializer:{},
    // 请求体 对象格式
    data: {
        firstname: 'Fred'
    },
    // 字符串格式
    data: 'Country=China&City=Wuhan',
    timeout: 1000, // 超时时间
    responseType: 'json', // default
	responseEncoding: 'utf8', // default
 	
    // 代理
    proxy：{}
}
```

具体参考：[axios/axios：基于Promise的浏览器和节点HTTP客户端.js (github.com)](https://github.com/axios/axios)

#### 6.1.1 默认配置

```js
axios.defaults.method = 'GET',
axios.defaults.baseURL = 'http://localhost:3000';
```

#### 6.1.2 创建实例对象

```js
const htt = axios.create({
	baseURL: 'http://api.apiopen.top',
	timeout:2000
})

htt({
    url:'/getJoke',
}).then(response => {
    console.log(response);
})

// 等同于 http.get('/getJoke').then({})
```

#### 6.1.3 拦截器

在请求或响应前截取请求或响应。比如：登录验证

```js
// 设置请求拦截器 config 配置对象
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 设置响应拦截器
axios.interceptors.response.use(function (response) {
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });

// 设置请求拦截1、2  响应拦截1、2 
// 最后输出 请求2 -> 请求1 -> 响应1 -> 响应2
```

删除拦截器：

```js
axios.interceptor.request.eject(请求拦截器对象);
// 清除请求的所有侦听器
axios.interceptor.request.clear(); 
```

#### 6.1.4 取消请求

```js
// 2. 声明全局变量
let cancel = null;
btns[0].onclick = function(){
    // 检测上一次的请求是否已经完成
    if(cancel !== null){
        // 取消上一次的请求
        cancel();
    }
    axios({
        method:'get',
        url:'http://localhost:3000/posts',
        // 1. 添加配置对象的属性
        cancelToken: new axios.CancelToken(function(c){
            // 3. 将 c 的值赋值给 cancel
            cancel = c;
        })
    }).then(response => { cancel = null;})
}
// 给取消按钮绑定事件
btns[1].onclick = function(){
        cancel();
}         
```

### 6.2 axios源码

先造一个函数出来，然后在函数上添加各种方法属性，形成最终的结果。

既可以当作函数使用，也可以当作对象调用上面的方法使用

#### 6.2.1 axios发送请求

```js
// 构造函数
function Axios(config){
    // 初始化
    this.defaults = config; // 为了创建 default 默认属性
    this.intercepters = {
        request: new InterceptorManager(),
        response: new InterceptorManager()
    }
}
// 原型添加相关的方法
Axios.prototype.request = function(config){
    console.log('发送ajax请求');
}
// 可以发送ajax请求是因为调用了request方法
Axios.prototype.get = function(config){
    return this.request(config);
}
Axios.prototype.post = function(config){
    return this.request(config);
}

// 声明函数
function createInstance(config){
    // 实例化一个对象
    let context = new Axios(config); // context.get()等，但不能当做函数使用（context()）
    // 创建请求函数
    let instance = Axios.prototype.request.bind(context);// instance 是一个函数 且可以 instance({}) 此时instance不能使用 instance.get()
    // 将 Axios.prototype 对象中的方法添加到instance函数对象中
    Object.keys(Axios.prototype).forEach(key => {
        instance[key] = Axios.prototype[key].bind(context);//此时this.default this.interceptors 可以获取到属性
    })
    // 为 instance 函数对象添加属性 default 与 interceptors
    Object.keys(context).forEach(key => {
        instance[key] = context[key];
    })
    //console.dir(instance);
    return instance;
}
let axios = createInstance()
// 发送请求
// axios({method:'POST'});
```

#### 6.2.2 axios请求发送过程 

axios是由``Axios.prototype.request`` 通过``bind``创建而来

axios发送请求时，调用了``request``函数，而``request``函数内部又调用了``dispatchRequest``，其内部又由``adapter``（xhr）发送请求，返回结果为一个普通值，即一个成功的promise对象，决定了``dispatchRequest``的结果，进而决定整个``request``结果

```js
// 1.声明构造函数
function Axios(config){
    this.config = config;
}
Axios.prototype.request = function(config){
    // 发送请求
    // 创建一个promise对象
    let promise = Promise.resolve(config)
    //console.log(promise) // 成功的promise  fulfilled
    //声明一个数组
    let chains = [dispatchRequest, undefined];// undefined 占位
    //调用 then 方法指定回调
    let result = promise.then(chains[0], chains[1]);
    //返回 promise 的结果
    return result;
}

// 2. dispatchRequest 函数
function dispatchRequest(config){
    //调用适配器发送请求
    return xhrAdapter(config).then(response => {
        // 对响应的结果进行转换处理
        return response;
    }, error => {
        throw error;
    })
    
}

// 3. adapter 适配器
function xhrAdapter(config){
    return new Promise((resolve, reject) => {
        // 发送 ajax 请求
        let xhr = new XMLHttpRequest();
        //初始化
        xhr.open(config.method, config.url);
        xhr.send(); // 发送
        // 绑定事件
        xhr.onreadystatechange = function(){
            if(xhr.readyState === 4){
                // 判断成功的条件
                if(xhr.status >= 200 && xhr.status < 300){
                    //成功的状态
                    resolve({
                        //配置对象
                        config: config,
                        //响应体
                        data: xhr.response,
                        //响应头
                        headers: xhr.getAllResponseHeaders(), //字符串 parseHeaders
                        // xhr 请求对象
                        request: xhr,
                        //响应状态码
                        status: xhr.status,
                        //响应状态字符串
                        statusText: xhr.statusText
                    });
                }else{
                    //失败的状态
                    reject(new Error('请求失败 失败的状态码为' + xhr.status))
                }
            }
        }
    })
}

// 4. 创建 axios 函数
let axios = Axios.prototype.request.bind(null);
axios({
    method:'GET',
    url:'http://localhost:3000/posts'
}).then(response => {
    console.log(response);
});
```

#### 6.2.3 axios拦截器工作原理

```js
// 构造函数
function Axios(config){
    this.config = config;
    thhis.intercepters = {
        request: new InterceptorManager(),
        response: new InterceptorManager(),
    }
}
// 发送请求 难点与重点
Axios.prototype.request = function(config){
    // 创建一个 promise 对象
    let promise = new Promise.resolve(config);
    // 创建一个数组
    const chains = [dispatchRequest, undefined];
    // 处理拦截器
    // 请求拦截器 将请求拦截器的回调 压入到 chains 的前面  request.handles = []
    this.interceptors.request.handlers.forEach(item => {
        chains.unshift(item.fulfilled, item.rejected);
    });
    //响应拦截器 压入到尾部
    this.interceptors.response.handlers.forEach(item => {
        chains.push(item.fulfilled, item.rejected);
    });
    // 遍历
    while(chains.length > 0){
        promise = promise.then(chains.shift(),chains.shift());
    }
    return promise;
}

// 发送请求
function diapatchRequest(config){
    // 返回一个promise队形
    return new Promise((resolve, reject) => {
        resolve({
            status: 200,
            statusText: 'OK'
        });
    });
}

// 拦截器管理器构造函数
function InterceptorManager(){
    this.handlers = [];
}
InterceptorManager.protptype.use = function(fulfilled, rejected){
    this.handlers.push({
        fulfilled,
        rejected
    })
}

// 创建实例
let context = new Axios({});
// 创建axios函数
let axios = Axios.prototype.request.bind(context);
//将 context 属性 config interceptors 添加至 axios 函数对象身上
Object.keys(context).forEach(key => {
    axios[key] = context[key];
});
```

#### 6.2.4 axios取消请求工作原理

```js
// 构造函数
function Axios(config){
    this.config = config;
}
// 原型 request 方法
Axios.prototype.request = function(config){
    return dispatchRequest(config);
}
// dispatchRequest 函数
function dispatchRequest(config){
    return xhrAdapter(config);
}
// xhrAdapter
function xhrAdapter(config){
    //发送 AJAX 请求
    return new Promise((resolve, reject) => {
        //实例化对象
        const xhr = new XMLHttpRequest();
        //初始化
        xhr.open(config.method, config.url);
        //发送
        xhr.send();
        //处理结果
        xhr.onreadystatechange = function(){
            if(xhr.readyState === 4){
                //判断结果
                if(xhr.status >= 200 && xhr.status < 300){
                    //设置为成功的状态
                    resolve({
                        status: xhr.status,
                        statusText: xhr.statusText
                    });
                }else{
                    reject(new Error('请求失败'));
                }
            }
        }
        // 关于取消请求的处理
        if(config.cancelToken){
            //对 cancelToken 对象身上的promise对象指定成功的回调
            config.cancelToken.promise.then(value => {
                xhr.abort();
                reject(new Error('请求已经被取消'))
            })
        }
}
//创建 axios 函数
const context = new Axios({});
const axios = Axios.prototype.request.bind(context);

// CancelToken 构造函数
function CancelToken(execytor){
    //声明一个变量
    var resolvePromise;
    //为实例对象添加属性
    this.promise = new Promise((resolve) => {
        //将resolve 赋值给 resolvePromise
        resolvePromise = resolve;
    });
    // 调用executor函数
    executor(function(){
        //执行resolvePromise函数
        resolvePromise();
    })
}
```

