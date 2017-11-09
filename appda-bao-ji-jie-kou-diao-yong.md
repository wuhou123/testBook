## app打包及原生JS接口调用

- app打包技术是用eclipse工具通过manifest.json配置打包成android和iOS,具体教程参看dcloud提供的[文档](http://ask.dcloud.net.cn/docs/#//ask.dcloud.net.cn/article/94)。

- 原生设备的接口教程参看dcloud提供的[文档](http://www.html5plus.org/doc/h5p.html)。

- app的更新，dcloud提供三种解决方案。目前采用第一种解决方案，第二种方案作为备用。
    - 整包(apk/ipa)升级 
    - App资源在线升级更新（生成移动App资源升级包直接下载更新）
    - App资源在线差量升级更新（差量升级包是针对某个历史版本到新版本的差量，所以对于升级服务器来讲需要保留所有历史版本，并且分别生成每个历史版本到新版本的差量升级包。）
其更新的JS的代码如下

```
// src/mine/update.js
	//资源在线升级更新
	updateWgt(){
		plus.downloader.createDownload("http://demo.dcloud.net.cn/test/update/H5EF3C469.wgt", {filename:"_doc/update/"}, function(d,status){
			plus.nativeUI.showWaiting("下载wgt文件...");
	        if ( status == 200 ) { 
	            app.log.debug("下载wgt成功："+ d.filename);
	            plus.nativeUI.showWaiting("安装wgt文件...");
			    plus.runtime.install(d.filename, {} ,function(){
			        plus.nativeUI.closeWaiting();
			        app.log.debug("安装wgt文件成功！");
			        plus.nativeUI.alert("应用资源更新完成！",function(){
			            plus.runtime.restart();
			        });
			    },function(e){
			        plus.nativeUI.closeWaiting();
			        app.log.debug("安装wgt文件失败["+e.code+"]："+e.message);
			        plus.nativeUI.alert("安装wgt文件失败["+e.code+"]："+e.message);
			    });
	        } else {
	            app.log.debug("下载wgt失败！");
	            plus.nativeUI.alert("下载wgt失败！");
	        }
	        plus.nativeUI.closeWaiting();
	    }).start();
	},
	//整包更新
	updateApk(){
		if(app.Config.device.isAndroid){
			plus.downloader.createDownload("", {filename:"_doc/update/"}, function(d,status){
				plus.nativeUI.showWaiting("下载app文件...");
		        if ( status == 200 ) { 
		            app.log.debug("下载app成功："+ d.filename);
		            plus.nativeUI.showWaiting("安装app文件...");
				    plus.runtime.install(d.filename, {} ,function(){
				        plus.nativeUI.closeWaiting();
				        app.log.debug("安装app文件成功！");
				        plus.nativeUI.alert("应用资源更新完成！",function(){
				            plus.runtime.restart();
				        });
				    },function(e){
				        plus.nativeUI.closeWaiting();
				        app.log.debug("安装app文件失败["+e.code+"]："+e.message);
				        plus.nativeUI.alert("安装app文件失败["+e.code+"]："+e.message);
				    });
		        } else {
		            app.log.debug("下载wgt失败！");
		            plus.nativeUI.alert("下载wgt失败！");
		        }
		        plus.nativeUI.closeWaiting();
		    }).start();
		} else if(app.Config.device.isIOS){
			//iOS平台的ipa无法安装，此时需要跳转到appstore，提示用户自动点击升级更新，跳转到appstore的方法为打开应用的appstore地址
			var url='itms-apps://itunes.apple.com/cn/app/hello-h5+/id682211190?l=zh&mt=8';// HelloH5应用在appstore的地址
			plus.runtime.openURL(url);
		}
	}

```