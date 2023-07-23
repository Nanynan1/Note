# webpack

## 1 使用

**定义**：本质上，webpack是一个用于现代JavaScript应用程序的*静态模块打包工具*。当webpack处理应用程序时，它会在内部从一个或多个入口点构建一个依赖图，然后将项目中所需的每一个模块组合成一个或多个bundles，它们均为静态资源，用于展示你的内容。

**打包**：把静态模块内容，压缩，整合，转译等（前端工程化）

```js
1.使用 npm init 初始化一个项目，并安装webpack
2.npm i webpack webpack-cli -g // 全局安装
3.npm i webpack webpack-cli -D //写入到package.json的开发依赖中
```

命令：`` npm run build``

```js
1.yarn init -y //初始化项目
2.安装依赖webpack、webpack-cli
3.执行 yarn webpack 来对代码进行打包
```

[webpack设置]: https://www.webpackjs.com/concepts/

## 2 五个核心概念

1. entry
2. output
3. loader加载器：webpack 只能理解 JavaScript 和 JSON 文件，loader让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块。
4. plugin：loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。
5. mode模式：development（开发模式）,  production(*),  none

webpack.config.js文件配置：

```js
const path = require("path"); 

module.exports = {
    //入口
    entry: "./src/main.js",
    //输出
    output: {
        //文件的输出路径
        // __dirname nodejs的变量，代表当前文件的文件夹目录
        path: path.resolve(__dirname,"dist"), //绝对路径
        //文件的输出文件名
        filename: "main.js",
        
    },
    //加载器
    module: {
        rules: [
            //loader的配置-打包图片
            {
                test: /\.(png|jpg|jpeg|gif)$/i,
                //图片直接资源类型的数据，可以通过指定type来处理
                type: 'asset', 
                generator: {
                    filename: 'asset/[hash][ext][query]'
                }
            },
             { test: /\.ts$/, use: 'ts-loader' },
        ],
    },
    //插件
    // plugins: [
    //     //plugin的配置
    // ],
    //模式
    mode: 'development',
};
```

## 3 优化-CDN使用

**CDN定义**：内容分发网络，指的是一组分布在各个地区的服务器

作用：把静态资源文件/第三方库放在CDN网络中各个服务器中，供用户就近请求获取。

> 开发模式使用第三方库，生产模式下使用CDN加载引入

## 4 多页面打包

多页面多入口

```js
module.exports = {
    entry: {
        '模块名1':path.resolve(__direname,"./src/main1.js"),
        '模块名2':path.resolve(__direname,"./src/main2.js"),
    },
    output: {
        path: path.resolve(__dirname,"dist"),
        filename: "./[name]/main.js",
    },
    plugins: [
        //在打包代码后，自动在打包文件夹里生成页面
        new HTMLPlugin()
        new HtmlWebpackPlugin({
            template:'./public/1.html',  //模板文件
            filename:'./路径/index.html', //输出文件
            chunks:['模块名1'] // 引入哪些打包后的模块（与entry的key一致）
        })
        new HtmlWebpackPlugin({
            template:'./public/2.html',
            filename:'./路径/index.html',
            chunks:['模块名2']
        })
    ]
    mode: 'development',
    //对代码进行一个映射，运行的是打包后的代码，但可以调试源码
    devtool: "inline-source-map"
};
```

## 5 babel

将高版本代码转换为旧版本代码，以提高兼容性

```js
module: {
  rules: [
    {
      test: /\.m?js$/, //以js、mjs结尾的文件
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}

//在package.json中配置 指定要兼容的浏览器
"browserslist": [
    "default", //不再解析箭头函数
]
```

``npm i -D webpack-dev-server``开发服务器

``npm webpack server --open``自动打开

# vite

## 1 简介

相较于webpack，vite采用了不同的运行方式：

- 开发时并不对代码进行打包，而是采用ESM的方式来运行项目
- 在项目部署时，再对项目进行打包

## 2 使用

``yarn add vite`` 安装开发依赖vite

``yarn vite`` 启动开发服务器，即时刷新

``yarn vite build`` 对代码进行打包

``yarn vite preview`` 预览打包后代码

### 2.1 vite创建项目

```bash
npm create vite@latest
yarn create vite
pnpm create vite
```

