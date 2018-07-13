webpack 配置
------

## 配置需求

配置需求两种环境模式，即`development`开发环境与`production`产品环境。需要设置环境变量:

```sh
NODE_ENV=development
```

为了兼容多个平台并方便使用，可以使用工具`cross-env`来定义环境变量。使用`npm`在全局安装:

```sh
npm i -g cross-env
```

在`package.json`中添加如下脚本启动：

```json
{
  "scripts": {
    "start": "cross-env NODE_ENV=development npx webpack-serve",
    "build": "cross-env NODE_ENV=production npx webpack"
  }
}
```


## 基础配置

基础配置依照 webpack 最佳实践，配置时可依据最简原则，只配置需要配置的项。

1. `mode`项的值和环境变量`NODE_ENV`一致;
2. 根据项目类型的不同，需要配置不同的`target`:
   * `web` - 浏览器端，包括PC端（包含响应式设计RWD）、Mobile端、微信公众平台等
   * `node` - 服务器端
   * `electron` - electron 打包程序
   * `electron-render` - electron 应用、微信小程序
3. `devtools`设置为`inline-source-map`，方便PC端调试
