## 项目目录说明


```
|-- node_modules                        // 依赖文件目录
|-- build                               // gulp打包后的文件目录
|-- lib                                 // 存放js插件资源文件
|   |-- fonts                           // 存放各种fonts文件目录
|   |-- mui.js                          // mui插件
|   |-- ...                             // 其他第三方JS插件
|-- src                                 // 源码目录
|   |-- common                          // 存放公共属性和方法的目录
|       |-- common.js                   // app一些共用方法文件
|       |-- common.css                  // 公共样式文件
|   |-- components                      // 存放公共组件的目录
|       |-- app-header.vue              // 页面头部导航栏公共组件
|       |-- ...                         // 其他公共组件
|   |-- pages                           // 页面视图文件目录
|       |-- .css                        // 对应单页样式文件 
|       |-- .html                       // 对应页面html文件 
|       |-- .js                         // 对应页面js文件
|       |-- ...                         // 其他静态资源（图片等）
|   |-- imgs                            // 存放各种图片文件目录
|   |-- nopack                          // 存放公共js库
|   |-- special                         // 存放公共参数配置、js库
|           |-- ....                    // 其他工具JS文件
|   		|-- background.png          // 页面背景图片
|   		|-- ...                     // 其他广告图片和样式文件
|-- unpackage                           // app编译包目录
|-- index.html                          // app的首页加载文件
|-- manifest.json                       // 打包app的配置文件
|-- package.json                        // 配置项目相关信息，通过执行 npm init 命令创建
```