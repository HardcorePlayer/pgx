chrome 配置
-----

## 添加 workspace

在开发web应用中，将项目源码添加至 chrome devtools workspace，可以获得更好的开发体验。通过配置sourcemap，在项目报错或是需要临时修改代码时可以直接定位到源码处，方便修改。

打开 `Devtools -> Sources -> Filesystem`，点击 `Add folder to workspace` 或将文件夹拖拽到面板中，授权成功会看到文件夹出现在面板左侧。

将临时文件夹、缓存文件夹、构建文件夹等（如 /flow-typed、/node_modules/.cache 等）源码不相关的文件夹排除出目录树。或可以通过在 `Devtools -> Settings -> Workspace` 处配置正则来自动排除某些文件夹。

在 `Devtools -> Settings -> Preferences` 处检查soucemap配置是否有开启（默认开启）。
