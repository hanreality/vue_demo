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

### 配置devServer

实时更新代码的功能，安装**webpack-dev-server**

```jsx
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