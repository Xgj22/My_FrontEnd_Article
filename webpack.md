## webpack 的理解

webpack 是一个用于现代 javascript 应用程序的静态模块打包工具。我们可以使用 webpack 管理模块。

### webpack 的主要作用：

1. 模块打包。可以将不同模块的文件打包整合到一起，并且保证它们之间引用正确，执行有序。利用打包我们就可以在开发的时候根据自己的业务自由划分文件模块，保证项目结构的清晰和可读性。
2. 编译兼容。通过 webpack 的 Loader 机制，编译转换诸如 .less .vue .jsx 等文件（这些文件浏览器无法识别）。让我们在开发中能够使用新特性新语法做开发，提高开发效率。
3. 能力扩展。通过 webpack 的 Plugin 机制，在实现模块化打包和编译兼容的基础上，进一步实现诸如按需加载、代码压缩等一系列功能，帮助我们进一步提高自动化程度，工程效率以及打包输出的质量。

### webpack 的构建流程：

webpack 的运行流程是一个串行的过程

1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
3. 确定入口：根据配置中的 entry 找出所有入口文件
4. 编译模块：从入口文件出发，调用所有配置的 loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
5. 完成模块编译：在经过上一步使用 loader 翻译完所有模块后，得到每个模块被翻译后的内容以及它们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

**简单说：**

- 初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
- 编译：从 entry 出发，针对每个 Module 串行调用对应的 loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
- 输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

## 常见的 loader 有哪些？

常见的 loader 如下：

- image-loader ： 加载并压缩图片文件
- less-loader：加载并编译 less 文件
- sass-loader：加载并编译 sass/scss 文件
- css-loader：加载 css ，支持模块化、压缩、文件导入等特性，使用 css-loader 必须配合使用 style-loader
- babel-loader：把 ES6 转换成 ES5
- postcss-loader：扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀
- eslint-loader：通过 ESLint 校验 js 代码
- vue-loader：加载并编译 Vue 组件
- file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
- url-loader：与 `file-loader` 类似，区别是用户可以设置一个阈值，大于阈值会交给 `file-loader` 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)

## 常见的 Plugin 有哪些？

plugin 有着各种灵活的功能，例如打包优化、资源管理、环境变量注入

- HtmlWebpackPlugin：简化 HTML 文件创建
- mini-css-extract-plugin：分离样式文件，CSS 提取为独立文件，支持按需加载
- clean-webpack-plugin：目录清理

## 说一下 Webpack 的热更新原理

```js
const Webpack = require('webpack')
module.exports = {
  // ...省略其他配置
  devServer:{
    port:3000,
    hot:true,
    contentBase:'../dist'
  },
  plugins:[
    new Webpack.HotModuleReplacementPlugin()
  ]
}

```

webpack 的热更新又称为热替换（hot module replacement（HMR））。这个机制可以做到不用刷新浏览器而将新变更的模块替换到旧的模块。HMR 的核心就是客户端从服务端拉去更新后的文件，准确地说是 chunk diff（chuk 需要更新的部分），实际上 webpack-dev-server 与浏览器之间维护了一个 websocket，当本地资源发生变化时，webpack-dev-server 会向浏览器推送更新，带上构建时的 hash ，让客户端与上一次资源进行对比。客户端对比出差异后会向 webpack-dev-server 发起 AJAX 请求来获取更改内容（文件列表、hash），这样客户端就可以再借助这些信息继续向 webpack-dev-server 发起 JSONP 请求获取该 chunk 的增量更新。

后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由`HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader`和`vue-loader`都是借助这些 API 实现热更新。

## webpack 面试题

![img](https://uploadfiles.nowcoder.com/images/20230816/930553934_1692168250459/D2B5CA33BD970F64A6301FA75AE2EB22)