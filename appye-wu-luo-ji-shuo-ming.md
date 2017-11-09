## app自己的业务
目前app自己所特有的业务逻辑有自动登录、登出、设备号类型判断、站点本地存储等一些业务处理。具体代码如下：
```
//设备手机信息本地存储，切入后台和前台事件处理，网络监听变化
		mui.plusReady(function() {
			plus.runtime.getProperty(plus.runtime.appid, function(wgtinfo) {
				var info = {
					model: plus.device.model, //手机型号
					vendor: plus.device.vendor, //手机厂家
					osName: plus.os.name, //操作系统名称
					osVersion: plus.os.version, //操作系统版本
					osLanguage: plus.os.language, //操作系统语言
					appid: plus.runtime.appid, //程序包名
					appName: wgtinfo.name, //程序名称
					appVersion: wgtinfo.version, //程序版本号
					appDescription: wgtinfo.description, //程序简介
					uuid:plus.device.uuid
					//clientid: plus.push.getClientInfo().clientid, //推送服务令牌（设备唯一标识）
				};
				localStorage.version = wgtinfo.version;
				localStorage.setItem('info', JSON.stringify(info));
				if (cnt.cnt == 0 || localStorage.lastVersion == localStorage.version) {
					vm.go();
				} else
					vm.getIntro();
			});
			//无网络提示
			document.addEventListener('netchange', function() {
				var nt = plus.networkinfo.getCurrentType();
				switch(nt) {
					case plus.networkinfo.CONNECTION_ETHERNET:
					case plus.networkinfo.CONNECTION_WIFI:
					case plus.networkinfo.CONNECTION_CELL2G:
					case plus.networkinfo.CONNECTION_CELL3G:
					case plus.networkinfo.CONNECTION_CELL4G:
						break;
					default:
						//防止多个出现警告
						if (!localStorage.alerting) {
							localStorage.alerting = 'true';
							mui.alert('当前无网络连接，您将无法收到推送告警', '警告', '我知道了', function() {
								localStorage.removeItem('alerting');
							});
						}
						break;
				}
			}, false);
			app.log('启动耗费时间：' + plus.runtime.launchLoadedTime);
			//切入后台事件
			document.addEventListener('pause', function() {
				//发送数据start
				if(localStorage.appId == 'com.birdsh.cheways.dys') {
					logeye.emit({
						events: 'dys_index_pause'
					})
				}
				if(localStorage.appId == 'com.birdsh.cheways.lf') {
					logeye.emit({
						events: 'lf_index_pause'
					})
				}
				//发送数据end
			}, false)
			//切入前台事件
			document.addEventListener('resume', function() {
				//发送数据start
				if(localStorage.appId == 'com.birdsh.cheways.dys') {
					logeye.emit({
						events: 'dys_index_resume'
					})
				}
				if(localStorage.appId == 'com.birdsh.cheways.lf') {
					logeye.emit({
						events: 'lf_index_resume'
					})
				}
				//发送数据end
			}, false)
			//设置只支持竖屏显示
			plus.screen.lockOrientation('portrait-primary');
		});

 //app自动登录
 	methods: {
		getIntro() {
			for (var i = 1; i <= cnt.cnt; i++) {
				this.introImgs.push({src: '../../special/introduce/'+i+'.png'})
			}
			localStorage.lastVersion = localStorage.version;
		},
		go() {
			if(localStorage.adminToken != null) {
				mui.openWindow(pageConfig['device-list']);
			} else if(localStorage.token != null) {
				mui.openWindow(pageConfig['main']);
			} else
				mui.openWindow(pageConfig['login']);
		}
	}


//默认配置
var defaultConfig = {
	hasCare: false, //是否有保养记录
	hasCarsPush: false, //是否有车队推送我的消息
	hasMyCard: false, //是否有我的卡证
	hasMyInsu: false, //是否有我的保险
	hasMyMotorcade: false,  //是否有我的车队
	hasRenew: false, //弹出框是否有续费
	hasRenewAndDeal: false, //我的页面是否有去续费和交易记录
	hasShare: false, //是否有分享
	hasWeiXinPay: false, //是否有微信支付
	hideMyCar: false, //是否隐藏我的爱车
	hideVoltage: false,  //是否隐藏首页和位置里的电压
	hideInsuInPay: false,  //支付和交易记录界面是否隐藏保险文字
	isMotor: false,  //首页是否只显示摩托车图标
	showServiceEnd: false,  //我的页面是否显示服务期止
	www: 'http://m.wapreach.com/GetItem?channelid=1661&itemid=4920&cpid=372',  //实时监控跳转链接
}

//app退出登录
window.addEventListener('logout', function() {
	var loginURL = '../template/login.html?hideFlip=1';
	mui.openWindow({
		url:loginURL,
		id: 'login',
		show: {
			aniShow: 'slide-in-left'
		},
	});
	function close(id) {
		var wv = plus.webview.getWebviewById(id);
		if (wv) wv.close('none');
	}
	close('main');
	close('device-list');
	close('setting');
	close('user-info');
	localStorage.removeItem('encrypt');
	localStorage.removeItem('adminToken');
	localStorage.removeItem('activateToken');
	localStorage.removeItem('token');
	localStorage.removeItem('user');
	localStorage.removeItem('lastCheckPay');
	plus.XGPush.unregister();
});           
```