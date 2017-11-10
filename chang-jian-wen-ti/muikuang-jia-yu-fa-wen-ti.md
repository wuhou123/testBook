> 1.mui移动端click事件使用tap（避免延迟），不是原生click，如：
>
> ```
> import Vue from 'vue'
> <div @tap='submit'>submit</div>
> ```

> 2.a标签链接跳转无法点击跳转：
>
> mui使用的时候会将所有的a标签的href屏蔽，跳转页面使用mui.openWindow\({}\),如：
>
> ```
> mui.openWindow({
>     url:new-page-url,
>     id:new-page-id,
>     styles:{
>       top:newpage-top-position,//新页面顶部位置
>       bottom:newage-bottom-position,//新页面底部位置
>       width:newpage-width,//新页面宽度，默认为100%
>       height:newpage-height,//新页面高度，默认为100%
>       ......
>     },
>     extras:{
>       .....//自定义扩展参数，可以用来处理页面间传值
>     },
>     createNew:false,//是否重复创建同样id的webview，默认为false:不重复创建，直接显示
>     show:{
>       autoShow:true,//页面loaded事件发生后自动显示，默认为true
>       aniShow:animationType,//页面显示动画，默认为”slide-in-right“；
>       duration:animationTime//页面动画持续时间，Android平台默认100毫秒，iOS平台默认200毫秒；
>     },
>     waiting:{
>       autoShow:true,//自动显示等待框，默认为true
>       title:'正在加载...',//等待对话框上显示的提示内容
>       options:{
>         width:waiting-dialog-widht,//等待框背景区域宽度，默认根据内容自动计算合适宽度
>         height:waiting-dialog-height,//等待框背景区域高度，默认根据内容自动计算合适高度
>         ......
>       }
>     }
> })
> ```
>
> 3.页面上下拖动，页面超出不出现滚动条，无法拖动：
>
> 页面内容续写在mui-scroll-wrapper的mui-scroll下，如：
>
> ```
> <div class="mui-scroll-wrapper">
>     <div class="mui-scroll">
>         ...页面内容
> </div>
> </div>
> ```
>
> 4.plus is undefined:  
> 调用plus对象需在初始化完成后调用，方法写在mui.plusReady\({}\)下，示例：
>
> ```
>   mui.plusReady(function(){
>     //关闭等待框
>     plus.nativeUI.closeWaiting();
>     //显示当前页面
>     mui.currentWebview.show();
>   });
> }
> ```
>
> 5.



