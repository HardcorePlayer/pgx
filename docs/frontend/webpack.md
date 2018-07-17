webpack 配置
-----

## 配置需求与基本原则

配置需求两种环境模式，即`development`开发环境与`production`产品环境。需要设置环境变量:

```sh
NODE_ENV=development # or production
```

为了兼容多个平台并方便使用，使用工具`cross-env`来定义环境变量。使用`npm`在全局安装:

```sh
npm i -g cross-env
```


## 基础配置

基础配置依照 webpack 最佳实践，配置时可依据最简原则，只配置需要配置的项，但需要确定如下配置项。

1. `mode`项的值和环境变量`NODE_ENV`一致;
2. 根据项目类型的不同，需要配置不同的`target`:
   * `web` - 浏览器端，包括PC端、Mobile端、微信公众平台和包含PWA等响应式设计（RWD）
   * `node` - 服务器端应用或各类以 nodejs 开发的工具
   * `electron` - electron 跨平台打包程序
   * `electron-render` - electron 应用、微信小程序
3. `devtools`设置为`inline-source-map`，开发环境下方便与 chrome devtool 联合调试

综上，webpack 的配置中需要出现如下配置：

```js
{
  mode: process.env.NODE_ENV,
  target: 'web', // or others
  devtools: 'inline-source-map'
}
```


## Web 应用打包流程

Web 应用是最常见的 webpack 打包需求。由于需求多样，打包方式也存在多种方式，下面会根据 `mode` 的不同分开介绍对应部分的配置。这里以单页应用（SPA）为主，也会涉及传统多页应用（MPA），离线应用与PWA的配置。在 web 前端开发中，通常会涉及四个部分的配置，分别是：

* `Javascript` - 主体代码
* `Styles` - 样式
* `Page` - 程序的入口点
* 静态资源与其他 - 图片、字体、图标、mp3声音文件等等


### Web 开发环境配置

开发环境需要配置环境为 `development`，以 React 框架为例说明配置时的要点。


#### 项目入口及导出配置

SPA入口的配置一般只有一个文件，但考虑到有打 ployfill 的必要或添加其他特殊前置依赖，这里使用数组代替单个元素：

```js
const name = process.env.ENTRY_NAME || 'main'
const prepend = process.env.PREPEND && process.env.PREPEND.split(',') || []

entry: {
  [name]: prepend.concat([
    path.resolve('src/boot.js')
  ])
}
```

使用 `boot.js` 而不是 `index.js` 的原因是SSR时的启动文件要特殊一些，这里在 `index.js` 中导出总组件会方便SSR引用。

由于是开发环境，项目导出由开发服务器接管，所以导出到根目录即可：

```js
const output = process.env.DEV_OUTPUT || '.'

output: {
  path: path.resolve(output),
  filename: '[name].js'
}
```


#### Loader 配置

loader 是 webpack 的核心应用，用于编译加载各类文件。由于所加载的文件通常是编译器编译之后产生的文件，所以在调试上有一定的难度，这就需要在开发时配置 SouceMap 来映射源码。常用编译器都已提供了该功能。除了调试功能外，加快 loader 编译效率也是需要在开发环境下做的，通过特殊编译选项或添加缓存，可以很好的提高编译效率，提高开发体验。

##### [babel-loader](https://github.com/babel/babel-loader) 配置

[babel](https://github.com/babel/babel) 是使用广泛的 js 编译器，通过使用 babel，可以将新的 js 语法（ES6+）编译到浏览器所支持的程度。babel-loader 的配置相对简单，但正如文档中所提到的 **babel is slow** 在开发时推荐添加 `cacheDirectory` 参数来缓存文件：

```js
{
  test: /\.js$/,
  loader: 'babel-loader?cacheDirectory'
}
```


#### 开发服务器与调试工具配置

在一般 web 开发中，通常需要启动一个开发服务器来调试代码，这里选用 `webpack-serve` 作为开发服务器，相比较 `webpack-dev-server` 而言，前者拥有更好的开发体验与丰富的日志信息。

绑定 server 的 `host` 到 `0.0.0.0`，方便局域网内使用手机调试，但由于一些蜜汁原因，还需要配置热更客户端的端口才可以正确热更：

```js
{
  host: '0.0.0.0',
  hot: {
    host: {
      server: '0.0.0.0',
      client: '127.0.0.1'
    }
  }
}
```

除了 host 的配置，通常为了调试接口，常需要将请求代理到后端接口，使后端开发保持一致性，避免后端配置跨域头，在SPA中通常还需要配置请求地址重定向等功能：

```js
{
  add(app, middleware, options) {
    app.use(convert(proxy('/api/v1', { target: 'http://localhost:3000' })))
    app.use(convert(history()))
  }
}
```

其中，`convert`、`proxy`、`history` 来自：

```js
import history from 'connect-history-api-fallback'
import proxy from 'http-proxy-middleware'
import convert from 'koa-connect'
```

为了在 chrome devtools 中调试源码，需要改写由 webpack 生成 sourcemap 的 domain 路径，默认是 `webpack:///` 为前缀的路径地址，只有改为和 chrome devtools workspace 中的地址一致，才可以成功映射到 workspace 中的文件。workspace 相关配置请参阅 chrome 修改 `output.devtoolModuleFilenameTemplate` 项：

```js
{
  devtoolModuleFilenameTemplate: info => {
    const override = path.resolve(info.resourcePath)
      .replace(/\\/g, '\/')
      .replace(/(\w):/, (_, a) => a.toUpperCase() + ':')

    const fmt = `file:\/\/\/${override}`

    return info.allLoaders.length && !info.allLoaders.startsWith('css')
      ? fmt + `?${info.hash}`
      : fmt
  }
}
```

上述配置可以兼容由 `css-loader` 和 `MiniCssExtractPlugin` 带来的不匹配问题，从而让 `css` 文件也能映射到源码。

配置完成后，检查 chrome devtools workspace 中的文件是否出现点亮（绿色圆点）标志，出现则表示配置成功。如果 css 文件也映射成功，可以在 `Elements` 面板右侧看到点亮标识。

![Workspace source file mapped](https://user-images.githubusercontent.com/5752902/42808406-d8a8b628-89e5-11e8-9012-833ccd3b4c47.png)

![Element style mapped](https://user-images.githubusercontent.com/5752902/42808480-00f23ca8-89e6-11e8-948f-53ee49e00793.png)
