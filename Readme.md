# 手动搭建Vue工程（Vue3 和 Webpack5）

参考 [https://juejin.cn/post/6921161482663100423](https://juejin.cn/post/6921161482663100423)

### 配置Webpack环境

得到一个只有 **package.json**文件的项目

```java
//创建工程文件夹
mkdir (your project name) && cd (your project name)
//初始化项目
npm init -y
```

安装 **webpack**、**webpack-cli**(webpack-cli是webpack的执行工具)

```java
npm install -D webpack webpack-cli 
```

根目录下添加 **src** 文件，并且在 **src** 文件夹内添加 **main.js** 文件；根目录下添加**index.html** 和 **webpack.config.js**

在**webpack.config.js添加内容：**

```jsx
// webpack.config.js
const path = require('path')

module.exports = {
  mode: 'development', // 环境模式
  entry: path.resolve(__dirname, './src/main.js'), // 打包入口
  output: {
    path: path.resolve(__dirname, 'dist'), // 打包出口
    filename: '[name].[hash].js'// 打包完的静态资源文件名
  }
}
```

修改 **package.json** 的 **scripts** 属性:

```json
"scripts": {
  "dev": "webpack --config ./webpack.config.js"
}
```

运行打包指令

```json
npm run dev
```

打包完成之后，发现dist文件夹啥都没有。

安装 **html-webpack-plugin** 插件

```java
npm install -D html-webpack-plugin
```

在 **webpack.config.js** 下引入并使用

```jsx
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, './src/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, './index.html'), // 我们要使用的 html 模板地址
      filename: 'index.html', // 打包后输出的文件名
      title: '手搭 Vue 开发环境' // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    })
  ]
}
```

为index.html增加内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><%= htmlWebpackPlugin.options.title %></title>
</head>
<body>
  <div id="root"></div>
</body>
</html>
```

为main.js添加内容

```jsx
const root = document.getElementById('root')
root.textContent = 'Hello World！'
```

再次运行 npm run dev 就会发现dist文件夹里有东西了，但是每次运行前dist目录并未清空

引入clean-webpack-plugin 小插件，实现运行前把dist目录清空

```jsx
npm install -D clean-webpack-plugin
```

修改webpack.config.js配置

```jsx
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, './src/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, './index.html'), // 我们要使用的 html 模板地址
      filename: 'index.html', // 打包后输出的文件名
      title: '手搭 Vue 开发环境' // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    }),
		new CleanWebpackPlugin()
  ]
}
```

### 引入Vue 3.x

```jsx
npm install -S vue@next
```

-S 是指生产环境需要用到的包 — dependencies

-D 是指开发环境需要的以来 —devDependencies

要用vue@next才能成功引入Vue 3.x，否则是2.x最高版本

src目录新建App.vue并添加内容

```html
<template>
  <div>Hello World!</div>
</template>

<script>
export default {
  
}
</script>
```

修改main.js文件内容

```jsx
import { createApp } from 'vue' // Vue 3.x 引入 vue 的形式
import App from './App.vue' // 引入 APP 页面组建

const app = createApp(App) // 通过 createApp 初始化 app
app.mount('#root') // 将页面挂载到 root 节点
```

还需要适当的loader程序去处理.vue文件类型

```java
npm install -D vue-loader@next
npm install -D @vue/compiler-sfc
```

修改 **webpack.config.js** 的内容：

```jsx
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const { VueLoaderPlugin } = require('vue-loader/dist/index')

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, './src/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js'
  },
	module: {
    rules: [
      {
        test: /\.vue$/,
        use: [
          'vue-loader'
        ]
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, './index.html'), // 我们要使用的 html 模板地址
      filename: 'index.html', // 打包后输出的文件名
      title: '手搭 Vue 开发环境' // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    }),
		new CleanWebpackPlugin()
  ]
}
```

在 App.vue 中加入 style 内容：

```html
<template>
  <div>Hello World!</div>
</template>

<script>
export default {
  
}
</script>
<style>
  div {
    color: yellowgreen;
  }
</style>
```

引入loader

```java
npm install -D style-loader
npm install -D css-loader
```

vue-style-loader 是服务端渲染的时候，需要的 loader，包括 less-loader、sass-loader 都是在用到的时候，才去添加。

修改 **webpack.config.js** 的内容：

```jsx

const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const { VueLoaderPlugin } = require('vue-loader/dist/index')

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, './src/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js'
  },
	module: {
    rules: [
      {
        test: /\.vue$/,
        use: [
          'vue-loader'
        ]
      },
			{
	      test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
	    }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, './index.html'), // 我们要使用的 html 模板地址
      filename: 'index.html', // 打包后输出的文件名
      title: '手搭 Vue 开发环境' // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    }),
		new CleanWebpackPlugin()
  ]
}
```

运行代码

```jsx
npm run dev
```

### 配置babel

通过babel将代码编译成浏览器识别的代码（针对新特性低版本浏览器不支持的问题），比如箭头函数，经过 babel 的转化后，就会变成普通的函数。

babel的使用方式

- 使用单体文件
- 命令行（babel-cli）
- 构建工具如webpack中的babel-loader插件

采用第三种方式

```jsx
npm install -D @babel/core @babel/preset-env babel-loader
```

修改 webpack.config.js文件内容：

```jsx
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const { VueLoaderPlugin } = require('vue-loader/dist/index')

module.exports = {
  mode: 'development',
  entry: path.resolve(__dirname, './src/main.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash].js'
  },
	module: {
    rules: [
      {
        test: /\.vue$/,
        use: [
          'vue-loader'
        ]
      },
			{
	      test: /\.css$/,
        use: [
          'style-loader',
          'css-loader'
        ]
	    },
			{
	      test: /\.js$/,
	      exclude: /node_modules/, // 不编译node_modules下的文件
	      loader: 'babel-loader'
	    },
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, './index.html'), // 我们要使用的 html 模板地址
      filename: 'index.html', // 打包后输出的文件名
      title: '手搭 Vue 开发环境' // index.html 模板内，通过 <%= htmlWebpackPlugin.options.title %> 拿到的变量
    }),
		new CleanWebpackPlugin()
  ]
}
```

根目录下新建babel.config.js文件

内容为：

```jsx
module.exports = {
  presets: [
    ["@babel/preset-env", {
      "targets": {
        "browsers": ["last 2 versions"] // 最近 2 个版本的浏览器
      }
    }]
  ]
}
```

这里 browsers 的配置，就是让 env 去识别要打包代码到什么程度，版本选的越新，打包出来的代码就越小。因为通常版本越低的浏览器，代码转译的量会更大。具体的配置可以参考[github.com/browserslis…](http://github.com/browserslis%E2%80%A6)

### 配置devServer

实时更新代码的功能，安装**webpack-dev-server**

```java
npm install -D webpack-dev-server
```

在 **webpack.config.js** 中**module.exports**下添加如下配置：

```jsx

devServer: {
  contentBase: path.resolve(__dirname, './dist'),
  port: 8080,
  publicPath: '/'
}
```

修改 **package.json**运行脚本

```json
"scripts": {
	"dev": "webpack serve --progress --config ./webpack.config.js"
}
```

在Term里运行

```json
npm run dev
```