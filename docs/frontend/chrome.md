chrome 配置
-----

## 添加 workspace

在开发web应用中，将项目源码添加至 chrome devtools workspace，可以获得更好的开发体验。通过配置sourcemap，在项目报错或是需要临时修改代码时可以直接定位到源码处，方便修改。

打开 `Devtools -> Sources -> Filesystem`，点击 `+ Add folder to workspace` 或将文件夹拖拽到面板中，授权成功会看到文件夹出现在面板左侧。

![FileSystem](https://user-images.githubusercontent.com/5752902/42796727-54ebb6c2-89be-11e8-9758-e8ce3c18c811.png)

![FileSystem on Drag](https://user-images.githubusercontent.com/5752902/42796741-75306f90-89be-11e8-81e2-a88d5e383017.png)

将临时文件夹、缓存文件夹、构建文件夹等（如 /flow-typed、/node_modules/.cache 等）源码不相关的文件夹排除出目录树。或可以通过在 `Devtools -> Settings -> Workspace` 处配置正则来自动排除某些文件夹。

![Workspace ignored](https://user-images.githubusercontent.com/5752902/42796840-e842d450-89be-11e8-9199-67c8e06a78a2.png)

在 `Devtools -> Settings -> Preferences` 处检查soucemap配置是否有开启（默认开启）。

![Sourcemap settings](https://user-images.githubusercontent.com/5752902/42796851-019aff5e-89bf-11e8-8ba1-2fdae8d26fd4.png)

## 配置 Network 面板

TODO
