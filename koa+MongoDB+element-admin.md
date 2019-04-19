这只是简单配置前后端，没有处理错误，也没有做好模块化，后面会完善，这是作为入门，了解整体的流程

我的相关文章：

- node的创建服务器的原生使用
- koa2框架的使用
---
- MongoDB的原生使用指令（bilibili）
- mongoose框架的使用（官方文档）
- 利用MongoDB原生指令自己封装构造函数（仿mongoose框架）（bilibili）
---
- babel7的使用（官方文档）
---
- 完整项目（前后端）仿饿了么外卖移动端、后台管理系统


## 一、创建前端项目
- 在我们要放项目的文件夹中运行git或命令行
``` shell
git clone https://github.com/PanJiaChen/vue-admin-template.git
```
- 不需要特地新建一个文件夹存克隆项目，生成的就是一个文件夹，可以重命名。
- 关于vue、vue-cli和[vue-element-admin](https://panjiachen.github.io/vue-element-admin-site/zh/guide/#%E5%8A%9F%E8%83%BD)这里不细讲，有兴趣的，可以看他们的官方文档和github
- 克隆好后，进入文件夹，安装依赖`cnpm i`
- 启动项目`npm run dev`
![](https://note.youdao.com/yws/public/resource/ebf8f1a25ca9e4dac223b1f8f197d6b3/xmlnote/74A70A38E9474588969D316E8DF2C09C/11802)
- 这样就创建好我们的前端项目啦（空壳）

## 二、创建Koa服务器
### 1、在根目录创建server文件夹
> 没错，目录结构将会是这样子，src前端项目与server后端项目在同一级当中

![](https://note.youdao.com/yws/public/resource/ebf8f1a25ca9e4dac223b1f8f197d6b3/xmlnote/463415D0C5A8464C8A8FA8DD22CE8EDA/11805)

### 2、初始化server项目
> 不要急，前期的准备还是挺多的，本文也以后端入门为主
在server文件夹中运行命令行
``` shell
npm init
```
- 注意：不是我们上传代码是初始化项目的`git init`
- 运行成功之后，会在server文件夹中生成一个package.json的文件（主要用于自定义启动服务器和依赖包管理）
### 3、安装koa相关依赖
> 关于koa2我们在其他地方讲
```shell
cnpm i --save-dev koa koa-bodyparser koa-router
```
- 这里只安装了演示用的koa依赖，实际上还需要安装很多相关依赖
```
koa
koa-router
koa-bodyparser
...
```
### 4、配置基础服务器并测试
> 安装好了依赖，就引入并调用试试吧
- 在server文件夹中新建index.js
```js
const Koa = require('koa') // 引入koa类，注意要大写（是个类class）
const bodyParser = require('koa-bodyparser') // 引入post请求解析
const router = require('koa-router')()  // 引入post请求解析

const app = new Koa() // 实例化Koa

//调用路由模块的get/post等方法(路径，异步回调方法)
router.get('/', async (ctx, next) => {
	// todo
})

app.use(bodyParser()) // 调用kao实例的use方法（插入中间件-堆积木）
app.use(router.routes()) // 调用use堆积木，传入路由模块的routes方法

app.listen(9000)
console.log('app start at port 9000...')
```
- 这里有一个小插曲，对`koa-router`模块的引入产生了好奇`require('koa-router')()`
- 看它的github库，看到官方的调用方法是
```js
const Router = require('koa-router')
const router = new Router()

app.use(router.routes())
```
- 我：？？？这是class类？？？class类能用`require('koa-router')()`这种方式跳过`new`？？？
![](https://note.youdao.com/yws/public/resource/ebf8f1a25ca9e4dac223b1f8f197d6b3/xmlnote/8D60115DFE3B4973838EC2454F516811/11809)
- 不对啊，像`koa`这个引入就只能使用new这个类才能调用
- 于是去[github](https://github.com/ZijianHe/koa-router/blob/master/lib/router.js)的源码找`koa-router`暴露的是什么
#### 关于new构造函数
- 暴露的是一个对象函数，即在该函数上设置原型属性、方法等。
- 调用可以跟普通函数一样调用并后面用`.`再跟一个方法
- 也可以用`new`把这个函数当成**构造函数**实例化成一个对象，用`.`跟一个方法
![](https://note.youdao.com/yws/public/resource/ebf8f1a25ca9e4dac223b1f8f197d6b3/xmlnote/7A2605AE5C1D4A1CAE1E0D6D0ADEE442/11812)
#### 启动服务器
不管小插曲，只要我们用好koa提供的路由，配置koa实例的端口，就能启动服务器了
```shell
node index
```
- 命令行输出`app start at port 9000...`
- 浏览器访问`http://localhost:9000`也不会出现`无法访问此网站`了

## 三、优化服务器编写
> 写过`vue`或者其他前端项目的我们都知道，`ES6、ES7`的很多命令是非常方便且语义化的  
> 但是`node10x`到目前为止，支持了许多`ES6`语法，却依旧没有支持`import/export`  
> 我们又想像`src文件夹`里前端一样用到`ES6`的话，就可以引入和配置`babel`啦。（前端`webpack`也是用这个模块来`支持es6`的）
### 1、安装Babel7相关依赖
> 与网上大部分教程不同，这是最新的babel7，相关内容参考这篇[博客]()
```shell
cnpm i --save-dev @babel/node @babel/core @babel/preset-env
```
### 2、配置Babel7文件
- 在`server文件夹`中新建`.babelrc`文件
```js
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}
```
编译并启动服务器
```shell
babel-node index
```
或者
```shell
npx babel-node index
```
### 3、安装自动重启服务器依赖
> 用过vue或者其他前端的人都知道，我们保存代码，项目会帮我们重新启动渲染，不需要手动去关闭再重启（改了配置文件除外如：config文件夹）
```shell
cnpm i --save-dev nodemon
```
- `nodemon`依赖模块，是**命令行工具**，我们可以使用`nodemon index`代替`node index`
- 但是我们还要运行`babel`运行编译啊，当然`nodemon`可以让我们自定义，监听什么运行什么命令行
```shell
nodemon -w index --exec babel-node index
```
```
-w ——监听（接文件路径或文件夹）
--exec ——监听到变化后执行（后接要执行的命令）
```
### 4、配置简写nodemon、babel命令行
> 我们都知道`webpack`中的`package.json`能让我们通过`npm run dev`或者`npm start`等命令行执行操作
- 其实这些命令行都可以在`package.json`中自定义配置
```json
"scripts": {
  "start": "nodemon -w index --exec babel-node index"
}
```
- 这样我们就能使用`npm start`简写我们的`合并了nodemon和babel`的命令行了

## 四、创建mongoDB服务器
> mongoDB不是简单安装依赖就可以的，它需要像`node`一样下载安装，并且还要配置  
> 这里就不讲解安装`MongoDB`了，讲了也可能会过时，网上有很多教程




[参考博客（掘金）](https://juejin.im/post/5bad9b1af265da0ae80120fe)
