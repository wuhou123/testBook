#### 视图组件
app等待框和关闭等待框，页面调用app.showWaiting('...'),关闭app.closeWaiting()
```
	/** * 显示等待框* @param {String} message*/
app.showWaiting = function(message) {
		if(window.plus) {
			return plus.nativeUI.showWaiting(message || lang.waiting);
		}
	};
	/***关闭等待框*/
app.closeWaiting = function(waitingObj) {
		if(window.plus)(waitingObj && waitingObj.close()) || plus.nativeUI.closeWaiting();
	};
```
#### VUE JS组件
下面是src/components目录JS组件文件的截图，主要是存放app的头部，按钮等组件

![image](http://osk1hpe2y.bkt.clouddn.com/17-11-9/91435765.jpg)