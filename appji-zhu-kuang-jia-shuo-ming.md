## App技术框架说明
##### 1. 首页加载页面（index.html）
app的入口加载文件，也是app的初始化。主要做app UI的初始化、VUE的初始化、app的常用JS加载、业务自动登录等

```
window.vm = new Vue({//vue实例
	el: '#app',	//vue 挂载对象
	mounted: function() {//生命周期回调函数，当前为dom加载完全后执行属性和方法
		var vm = this;
		mui.init({//mui 初始化
			preloadPages: [pageConfig['browser_main']] //预加载内置浏览器
		});
		mui.plusReady(function() {//mui plus对象初始化
			plus.runtime.getProperty(plus.runtime.appid, function(wgtinfo) {
				localStorage.version = wgtinfo.version;
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
							mui.alert(vm.$t("index.noInter[0]"), vm.$t("index.noInter[1]"), vm.$t("index.noInter[2]"), function() {
								localStorage.removeItem('alerting');
							});
						}
						break;
				}
			}, false);
```
##### 2. 单页面（src目录下例：login.html）
```
window.vm = new Vue({//vue实例
	el: '#app2',//vue 挂载对象	
	data: {//数据变量
		lang: navigator.language.slice(0, 2),
		showLogin: false,
		userName: '',
		password: '',
		type: 3, //3表示普通用户，2表示管理员
	},
	mounted: function() {//生命周期回调函数，当前为dom加载完全后执行属性和方法
		var _self = this;
		window.addEventListener('pagebeforeshow', this.pageBeforeShow, false);

		mui.init({});
		mui.later(function() {
			_self.showLogin = true;
		}, 2000);
		mui.plusReady(function() {
			//手动关闭启动页
			plus.navigator.closeSplashscreen();
			plus.navigator.setStatusBarBackground('#fff');
			plus.navigator.setStatusBarStyle('dark');
		});
		//一秒内连续点击两次，退出应用，仅安卓有效；
		mui.back = this._back;
		window.addEventListener('fleetLogin', function(event) {
			_self.fleetLogin(event.detail);
		});
		window.addEventListener('lastAutoLogin', function(event) {
			var ll = JSON.parse(localStorage.lastLogin)
			_self.autoLogin({mobile: ll.name, password: ll.pwd});
		});
		window.addEventListener('autoLogin', function(event) {
			_self.autoLogin(event.detail);
		});
	},
	computed: {//计算属性
		canLogin: function() {
			return this.userName.trim().length > 0 && (this.password.trim().length >= 6 || (this.type==2 && this.password.trim().length >= 1));
		},
	},
	methods: {//业务方法
		autoLogin: function(d) {
			this.userName = d.mobile;
			this.password = d.password;
			this.login();
		},
		pageBeforeShow: function() {
			this.userName = JSON.parse(localStorage.lastLogin||'{}').name||'';
			localStorage.removeItem('userData');
		},
		login: function() {
			var _self = this,
				userName = _self.userName.trim(),
				password = _self.password.trim(),
				cid ="";
				
			if(!userName || !password) {
				mui.toast(this.$t("login.notEmpty"));
				return;
			}
			//若是手机号，传上次的设备号给后端（用于后端确定token）
			if (app.Reg.IS_MOBILE.test(userName)) {
				cid = localStorage["bind:" + userName]||'';
			}
			mui('#pw')[0].blur();
			var loginData = {
				name: _self.userName,
				pwd: _self.password,
				type: _self.type,
				cid:cid,
				packageName: localStorage.appId,
			};
			app.ajax({
				url: app.api['login2'],
				data: loginData,
				showWaiting: this.$t("login.waiting"),
				closeWaiting: false,
				success: function(data, message) {
					app.closeWaiting();
					if (data.prompt == 1) {  //设备号已激活但手机号未注册过
						mui.confirm(message, localStorage.appName, [_self.$t("login.goRegister[0]"), _self.$t("login.goRegister[1]")], function(e) {
							if (e.index == 0) {
								mui.openWindow({url: "../register/register.html?mobile="+(data.clink||'')});
							} else {
								continueLogin();
							}
						})
					} else {
						continueLogin();
					}
					
					function continueLogin() {
						_self.logins = data;
						localStorage.lastLogin = JSON.stringify(loginData);
						_self.password = '';
						
						if(!data.device) {  //管理员
							app.closeWaiting();
							localStorage.adminToken = data['access_token'];
							vm.openWindow(pageConfig['device-list']);
							return;
						}
						
						if (data.contents == '4') {
							localStorage.activateToken = data['access_token'];
							app.closeWaiting();
							mui.alert(_self.$t("login.goActivate[0]"), '', _self.$t("login.goActivate[1]"), function(e) {
								if (e.index == 0)
									vm.openWindow({url: '../activate/activate.html', styles: {top: 0}});
							});
						} else {
							_self.userLogin(data);
						}
					}
				},
				error: function() {
					app.closeWaiting();
				}
			});

		},
		fleetLogin: function(item) {
			localStorage.setItem('token', localStorage.adminToken);
			localStorage.setItem('user', new User(item.cid, "admin", item.clink));
			app.ajax({
				url: app.api.decideGetPush,
				data: {cid: item.cid},
				success: function() {}
			});
			mui.openWindow(pageConfig['main']);
		},
		findPassword: function() {
			vm.openWindow(pageConfig['find-password']);
		},
		switchToggle: function(event) {
			this.type = event.detail.isActive ? 2 : 3;
		},
		userLogin: function(data) {
			//刷新手机号绑定的最新登录设备，用于手机号登录
			localStorage.setItem("bind:" + data.ctel, data.device[0]);
			localStorage.token = data['access_token'];
			var u = new User(data.device[0], 'normal', data.clink);
			localStorage.user = JSON.stringify(u);
			mui.openWindow(pageConfig['main']);
		},
		_back: function() {
			var self = this;
			if(!self.firstTime) {
				self.firstTime = new Date().getTime();
				plus.nativeUI.toast(this.$t("login.doubleExit"));
				setTimeout(function() {
					self.firstTime = null;
				}, 1000);
			} else {
				if(new Date().getTime() - self.firstTime < 1000) {
					var main = plus.android.runtimeMainActivity();
    				main.moveTaskToBack(false);
				}
			}
		}
	}
});
```
##### 3. app打包配置（manifest.json）
app打包配置的应用名称，appid，横竖屏等

![图片](http://osk1hpe2y.bkt.clouddn.com/17-11-9/78267342.jpg)