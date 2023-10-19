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

### ChatGPT：webpack 打包的流程

1. 解析入口文件：webpack会根据配置文件中指定的入口文件（entry）开始解析应用程序之间的依赖关系
2. 模块解析：Webpack 使用模块解析器来解析入口文件及其依赖的模块。它会根据配置中的规则，识别各种类型的模块（JS、css、图片）并进行相应的处理
3. 加载器处理：当遇到非 JS 类型的模块时，Webpack 会使用相应的加载器来处理这些模块。加载器可以将这些非 JavaScript 模块转换成 JavaScript 模块，或者对他们进行其他处理
4. 模块打包：webpack 会根据模块之间的依赖关系，将他们打包成一个或多个输出文件。
5. 代码优化：webpack 可以进行代码优化，如去除未使用的代码、压缩代码，以减少打包后文件的大小
6. 输出文件：Webpack 会将打包后的文件输出到配置指定的输出路径中
7. 使用插件：webpack 可以使用插件来扩展其功能。插件可以处理额外的任务，如生成 HTML 文件、提取 CSS 文件、代码分割等

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

## Optimization （优化）

optimization 是 Webpack 的一个配置选项，用于配置打包过程中的优化策略和行为。通过配置 optimization ，我们可以对打包文件进行各种优化，以提高应用的性能和加载速度

- usedExports：是否开启 Tree Shaking，用于剔除未使用的代码。当开启时，webpack 会分析模块和导入的使用情况，并将未使用的代码将最终的打包文件中剔除掉，减小文件体积
- minimize：是否开启代码压缩，对打包后的代码进行压缩，减小文件体积
- splitChunks：代码分割配置，用于将代码拆分成多个块，以实现按需加载。
- `runtimeChunk`: 是否将 Webpack 的运行时代码提取到单独的文件中，默认为 `false`。通过将运行时代码与业务代码分离，可以实现更好的缓存效果。

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

## webpack-DllPlugin

DllPlugin 是一个 WebPack 插件，用于创建和管理动态链接库。它可以将一些基础依赖库（如第三方库、框架等）提前打包成一个独立的动态链接库，**以提高构建速度和减少重复构建的时间**

## 如何打包一个库文件

**创建一个全新的项目**

- 创建 library 项目
- 使用 npm init -y 进行配置 package.json
- 新建 src 目录
- 根目录下创建 **webpack.config.js** 文件
- 安装 webpack、webpack-cli

有很多种情况可以引入我们的打包文件，例如使用 ES Module、CommonJS、AMD、CMD、script 标签

针对以上几种使用场景，我们可以在 output 中配置 library 和 libraryTarget 属性

**配置属性说明：**

- **`library`**：这个属性指，我们库的全局变量是什么，类似于`jquery`中的`$`符号
- **`libraryTarget`**: 这个属性指，我们库应该支持的模块引入方案，`umd`代表支持`ES Module`、`CommomJS`、`AMD`以及`CMD`

## 如何提高 webpack 的打包速度？

1. 多入口的情况下，使用 optimization.splitChunks 来提取公共代码
2. 通过 external 配置来提取常用库
3. 利用 DllPlugin 和 DllReferencePlugin 预编译资源模块，通过 DllPlugin 来对哪些我们引用但是绝对不会修改的 npm 包进行预编译，再通过 DllReferencePlugin 将预编译的模块加载进来
4. 使用 thread-loader 实现多进程加速编译
5. 使用 terser-webpack-plugin 对 js 进行代码压缩
6. 优化 resolve 配置缩小范围
7. 使用 tree-shaking 和 Scope hoisiting 来剔除多余代码

## 如何减少 webpack 打包体积？

1. 使用 externals 配置来提取常用库
2. 使用 tree-shaking 和 scope hoisting 来剔除多余代码
3. 使用 optimize-css-assets-webpack-plugin 压缩css
4. 利用 terser-webpack-plugin 对 js 进行代码压缩

## 文件指纹是什么？

文件指纹是指打包后输出文件的名的后缀

- hash：和整个项目的构建有关，只要项目文件有修改，整个项目构建的 hash 就会变化
- chunkhash：和 webpack 打包的 chunk 有关，不同的 chunk、不同的 entry 会生成不同的 chunkhash
- contenthash：根据文件内容来定义 hash，文件内容不发生变化，则 contenthash 就不会变化。直接在输出文件名添加对应的 hash 值即可

## webpack 面试题

![img](https://uploadfiles.nowcoder.com/images/20230816/930553934_1692168250459/D2B5CA33BD970F64A6301FA75AE2EB22)