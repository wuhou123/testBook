## app配置以及其他方法（src/common.js）

#### 一、app公用ajax方法
根据业务逻辑定义了公共ajax方法，token注入、请求体处理，以及返回消息处理
```
	 * ajax方法
	 * @param {Object} setting
	 */
	app.ajax = function(setting) {
		var defaultSetting = {
			dataType: setting.dataType || 'json', //服务器返回json格式数据
			type: (setting.url.lastIndexOf('.json') > -1 ? 'get' : (setting.type || 'post')),
			timeout: setting.timeout || 15000, //超时时间设置为15秒；
			async: true,
			showWaiting: setting.showWaiting || false,
			success: function(resp, status, xhr) {
				app.log(/\/([\w\.]+)$/.exec(setting.url)[1] + '返回数据:' + JSON.stringify(resp));
				switch(resp.flag) {
					case '2000':
						mui.isFunction(setting.success) && setting.success(resp.result, resp.message);
						break;
					case '777': //token自动续期
						app.ajax({
							url: app.api['login'],
							data: JSON.parse(localStorage.lastLogin),
							success: function(data) {
								if(data.contents == '3') { //未激活
									localStorage.activateToken = data['access_token'];
									app.closeWaiting();
									mui.confirm(lang.activate[0], '', [lang.activate[1], lang.activate[2]], function(e) {
										if(e.index == 0) {
											userLogin(data);
										} else if(e.index == 1) {
											vm.openWindow({ url: '../activate/activate.html', styles: { top: 0 } });
										}
									});
								} else if(data.contents == '4') {
									localStorage.activateToken = data['access_token'];
									app.closeWaiting();
									mui.alert(lang.activate[0], '', lang.activate[2], function(e) {
										if(e.index == 0)
											vm.openWindow({ url: '../activate/activate.html', styles: { top: 0 } });
									});
								} else {
									userLogin(data);
								}

								function userLogin(data) {
									if(localStorage.adminToken != null)
										localStorage.adminToken = data['access_token'];
									else localStorage.token = data['access_token'];
									app.ajax(setting);
								}
							},
							error: function() {
								mui.toast(lang.loginFailed)
								if (window.plus) mui.fire(plus.webview.getWebviewById('browser_main'), 'logout');
							}
						});
						break;
					case '778':
						alert(resp.message);
						if (window.plus)
							mui.fire(plus.webview.getWebviewById('browser_main'), 'logout');
						else mui.plusReady(function() {
							mui.fire(plus.webview.getWebviewById('browser_main'), 'logout');
						})
						break;
					case '401':
					case '200':
					case '204':
					case '400':
					case '404':
					case '429':
					case '500':
					default:
						if (resp.message.indexOf("Token无效")>-1) {
							app.ajax({
								url: app.api.getDevices,
								data: {
									psize: 100,
								},
								success: function(d) {
									var devices = d.items.map(function(item) {
										return item.cid
									});
									if (devices.indexOf(app.getUser().uid)==-1) {
										mui.toast(lang.lastCid+app.getUser().uid+lang.removed+devices[0]);
										var u = JSON.parse(localStorage.user);
										u.uid = devices[0];
										localStorage.user = JSON.stringify(u);
									}
								}
							})
						} else if(resp.message != '访问过于频繁')
							mui.toast(resp.message);
						mui.isFunction(setting.error) && setting.error(resp.result);
						break;
				}
			},
			complete: function(xhr, status) {
				if(setting.showWaiting && setting.closeWaiting != false) {
					app.closeWaiting();
				}
				var resp = xhr.response;
				if(status === 'success' && defaultSetting.dataType.toLowerCase() === 'json') {
					resp = JSON.parse(resp);
				}
				if(typeof setting.complete == 'function') setting.complete(resp);
			},
			error: function(xhr, type, errorThrown) {
				var errorMsg = {
					'timeout': lang.timeout,
					'error': lang.error,
					'parsererror': lang.parsererror
				};
				mui.toast((errorMsg[type] || lang.ajaxError));
				if(typeof setting.error == 'function') setting.error(xhr, type, errorThrown);
			}
		};
		setting.beforeSend && (defaultSetting.beforeSend = setting.beforeSend);
		defaultSetting.data = setting.data || {};

		if(setting.url && skipAPI.indexOf(setting.url) == -1 && !defaultSetting.data.cid) {
			var user = app.getUser();
			user && (defaultSetting.data.cid = user.uid);
		}
		for (var i in defaultSetting.data) {
			if (defaultSetting.data[i]==null) delete defaultSetting.data[i];
		}
		defaultSetting.headers = {
			flag: app.config.flag,
			lang: navigator.language.slice(0, 2)
		};
		if(localStorage.adminToken != null) {
			defaultSetting.headers.token = localStorage.adminToken;
		} else if(localStorage.token != null) {
			defaultSetting.headers.token = localStorage.token;
		}
		if(setting.headers) mui.extend(true, defaultSetting.headers, setting.headers);
		if(setting.showWaiting) app.showWaiting(typeof setting.showWaiting === 'string' ? setting.showWaiting : undefined);
		app.log('token:' + defaultSetting.headers.token);
		app.log('正在请求api:' + setting.url);
		app.log('发送数据:' + JSON.stringify(defaultSetting.data));
		mui.ajax(setting.url, defaultSetting);
	};
```

#### 二、信鸽方法配置

```
//信鸽推送
mui.plusReady(function() {
	var _BARCODE = 'XGPush', B = window.plus.bridge;
	window.plus.XGPush = {
		unregister: function() {
			return B.execSync(_BARCODE, "unregister", []);
		},
		getToken: function() {
			return B.execSync(_BARCODE, "getToken", []);
		},
		//arg123是内容，分三行显示，extra是额外信息用于点击后处理
		createMessage: function(arg1, arg2, arg3, title, extra) {
			return B.execSync(_BARCODE, "createMessage", [arg1, arg2, arg3, title, extra]);
		},
	};
	if (mui.os.android) {
		window.plus.XGPush.register = function(sc, ec) {
			return B.exec(_BARCODE, "register", [B.callbackId(sc, ec)]);
		}
	} else {  //ios下register为关键词
		window.plus.XGPush.register = function(sc, ec) {
			return B.exec(_BARCODE, "registers", [B.callbackId(sc, ec)]);
		}
	}
})
```

#### 三、特定对象获取定义
```
Object.defineProperty(window, 'lsUserInfo', {
	get: function() {
		return localStorage.userInfo ? JSON.parse(localStorage.userInfo) : {
			"info": {
				"cowner": "",
				"usex": 0,
				"ubirthday": "",
				"ephoto": './userimg.png',
				"bgphoto": './back.png',
				"clink": "",
				"cendtime": '',
			},
			"devList": {
				"list": []
			}
		};
	}
});
```
#### 四、定义全局变量
```
window.User = function (uid, role, clink) {
	this.uid = uid;
	this.role = role || 'normal';
	this.clink = clink || '';
}
```
#### 五、定义全局函数方法
```
	/**
	 * 获取User
	 */
	app.getUser = function() {
		return JSON.parse(localStorage.user || '{}');
	};

```
##### 六、公共样式（src/common.css）
stylus预处理器设置的全局变量处理
```
//调小英文字体
html:lang(en) {
	font-size: 110%;
}
```
##### 七、接口统一配置
配置app的所有接口
```
if(app.config.isDebug) {
		//app.config.apiDomain = 'http://che.ijiashequ.cn:8010/loveApi';
		//app.config.apiDomain = 'http://192.168.33.81/loveApi'; //API吴长
		//app.config.apiDomain ='http://192.168.33.152:8088/api';//API刘本地
		//app.config.apiDomain ='http://192.168.33.93:8080/api';//API郑本地
		app.config.apiDomain = 'http://192.168.33.158:8007/loveApiIn'; //API裴本地
		//app.config.apiDomain = 'http://192.168.33.114/api';//谢API本地
		//app.config.apiDomain = 'http://192.168.33.92:8080/api'; //张磊测试
	}
	//常用正则表达式
	app.Reg = {};
	app.Reg.MODULE_NAME = /([\w-]+)\.html/; //匹配模块名
	app.Reg.HTTP_URL = /^((https|http)?:\/\/)/; //校验http url
	app.Reg.IS_MOBILE = /^1[3|4|5|7|8]\d{9}$/; //手机号码
	app.Reg.EMAIL = /^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/; //邮箱
	//所有api接口 http://192.168.33.8:7070/svnpro/weixin/car_guard/doc/车卫士通用版业务接口.doc
	app.api = {
		'getcaruserinfo': '/carguard/user/getCarUserInfo.do', //?name=030650611(车辆用户信息)
		'updatecaruserinfo': '/carguard/user/updateCarUserInfo.do', //?id=16775&num=15001914584&name=德忠
		'getcarusermap': '/carguard/user/getCarUserMapInfo.do', //?name=030619991(首页地理位置+幻灯片宣传信息)
		'getRealTimePosition': '/cheway/point/getRealTimePosition.do', //?cid=030650611(获取实时位置信息)
		'getcardaydistance': '/carguard/user/getCarTance.do', //?cid=030619991(今日历程<单位：千米>)
		'getlastwarninfo': '/carguard/warn/searchLastWarn.do', //?name=030619991(告警记录列表)
		'getwarninfo': '/carguard/warn/searchWarn.do', //?name=030619991&lastId=11111<告警最大内码>(告警记录列表)
		'setwarninfo': '/carguard/warn/setCarWarnByHaved.do', //?id=4907999(设置已读的告警记录)
		'getAlertsRecord': '/cheway/alert/getAlertsRecord.do', //获取一天的告警记录列表
		'saveSuperCare': '/carguard/other/saveSuperCare.do', //?cid=030619991&val=0<0 撤防 1 设防>(设置超级设防和撤防状态)
		'getSuperCare': '/carguard/other/getSuperCare.do', //?cid=030619991(獲取超级设防和撤防状态)
		'saveNormalCare': '/carguard/other/saveNormalCare.do', //?cid=030619991&type=0<0:设防,1:撤防>(设置设防和撤防状态)
		'getNormalCare': '/carguard/other/getNormalCare.do', //?cid=030619991(獲取设防和撤防状态)
    }
```