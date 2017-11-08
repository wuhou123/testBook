这是一个app\(android/iOS\)项目，但页面视图全部都用的是html5页，没有使用app的原生页面，项目可以直接在PC上运行html5页面。与服务端的交互全部都是走web api接口方式，接口统一配置在common.js的app.api下。

前端h5是基于[mui](http://dev.dcloud.net.cn/mui/) + [vue](http://cn.vuejs.org/v2/api/)+gulp+html5+css3的前端App多页面项目框架。

app打包技术是用[HBuilder IDE](http://www.dcloud.io/index.html)工具一键打包成APP，本项目使用了原生设备的的Storage和管理条码扫描。对于app的升级是html5资源在线升级更新,而不是整个APP更新。这些都是[dcloud](http://www.dcloud.io/index.html)提供一整套技术解决方案。

本项目只是一个技术框架，对于项目中具体的业务的东西只会大概的说明一下。

**说明：** 可能有些朋友不知道[signalR](http://signalr.net/)是什么东西，其实[signalR](http://signalr.net/)就是让客户端（Web页面）和服务器端可以互相通知消息及调用方法的前端JS，当WebSockets可用时（即浏览器支持Html5）[signalR](http://signalr.net/)使用WebSockets，当不支持时[signalR](http://signalr.net/)将使用其它技术来保证达到相同效果。

> 1. 前端UI的部分使用mui框架
>
> 2. app打包技术使用HBuilder IDE工具
>
> 3. 原生App对设备的调用
>
> 4. 使用vue-router2实现单页路由
>
> 5. 使用.vue文件进行页面功能组件化的开发
>
> 6. 使用vuex管理webApp的数据状态
>
> 7. 使用signalR实现客户端与服务端长时间通信
>
> 8. 使用webpack2实现对模块打包、压缩、混淆，预处理，热加载。
>
> 9. 自己实现了一套路由机制，但它只适用于app中使用h5的header。



