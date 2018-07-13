webpack 配置
------

## 配置需求与基本原则

配置需求两种环境模式，即`development`开发环境与`production`产品环境。需要设置环境变量:

```sh
NODE_ENV=development # or production
```

为了兼容多个平台并方便使用，使用工具`cross-env`来定义环境变量。使用`npm`在全局安装:

```sh
npm i -g cross-env
```

在`package.json`中添加如下启动脚本：

```json
{
  "scripts": {
    "start": "cross-env NODE_ENV=development npx webpack-serve",
    "build": "cross-env NODE_ENV=production npx webpack"
  }
}
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
* `Html` - 程序的入口点
* 静态资源与其他 - 图片、字体、图标、mp3声音文件等等


### Web 开发环境配置 development

#### Web 开发服务器配置

在一般 web 开发中，通常需要启动一个开发服务器来调试我们的代码，这里选用 `webpack-serve` 来作为开发服务器，相比较 `webpack-dev-server` 而言，前者拥有更好的开发体验。

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

除了 host 的配置，通常为了调试接口，常需要将请求代理到后端接口，避免跨域请求，在SPA中通常还需要配置地址重定向等功能：

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
