这是一个app\(android/iOS\)项目，但页面视图全部都用的是html5页，没有使用app的原生页面，项目可以直接在PC上运行html5页面。与服务端的交互全部都是走web api接口方式，接口统一配置在common.js的app.api下。

前端h5是基于[mui](http://dev.dcloud.net.cn/mui/) + [vue](http://cn.vuejs.org/v2/api/)+gulp+html5+css3的前端App多页面项目框架。

app打包技术是用到了eclipse工具打包成APP，本项目使用了原生设备的的拍照、定位功能和原生插件（信鸽推送）。对于app的升级是html5资源整个APP更新。这些都是[dcloud](http://www.dcloud.io/index.html)提供一整套技术解决方案。

本项目需要开发者了解基本的html5、css3、js、vue、gulp的知识。

**说明：** 开发工具用到dcloud的Hbuilder IDE，eclipse IDE，打包工程文件有固定的文件目录一般不需要增改，打包流程[点击这个](http://ask.dcloud.net.cn/article/38)。

> 1. 前端UI的部分使用mui框架
>
> 2. app打包技术使用eclipse工具
>
> 3. 原生App对设备的调用参考dcloud的html5+文档
>
> 4. 使用tap点击事件跳转到目标页面，该页面是mui自带的窗口创建打开，非h5原生的a链接跳转
>
> 5. 使用.vue文件进行页面功能组件化的开发
>
> 6. 使用ajax管理数据加载
>
> 7. 使用信鸽实现消息推送
>
> 8. 使用gulp实现对模块打包、压缩。
>
> 9. pug模板语言、stylus css预处理器




