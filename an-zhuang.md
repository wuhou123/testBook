## 安装
- 下载[HBuilder IDE](http://www.dcloud.io/index.html)开发工具，其实HBuilder是dcloud 把eclipse的改造成一个专门应用于app打包、多种语言支持：php、jsp、ruby、python、nodejs等web语言，less、coffee等编译型语言均支持的开发工具

- 下载[node.js](https://nodejs.org/en/)，作为前端web的运行环境。我当前的node.js版本是6.10.0 npm版本是5.4.0


- app打包完全是基于manifest.json配置文件，它主要是用来配置app的基本信息（版本号、appid等）、图标(app的应用图标)、sdk配置、模块权限配置、页面引用关系、代码视图，具体参看dcloud提供的[文档](http://ask.dcloud.net.cn/docs/#//ask.dcloud.net.cn/article/94)。


## npm初始化
```
$ 下载项目在当前项目目录下打开命令行工具
```
![图片][./img/anzhuang1.png]

##### package.json内容如下

```
{
	"name": "Fans",
	"version": "1.0.0",
	"description": "粉丝煲",
	"main": "js/entrance.js",
	"keywords": "粉丝煲",
	"homepage": "",
	"bugs": {
		"url": "https://github.com/yujinjin/fans/issues",
		"email": "yujinjin9@126.com"
	},
	"author": {
		"name": "jinyu",
		"email": "yujinjin9@126.com",
		"url": "https://github.com/yujinjin"
	},
	"license": "MIT",
	"repository": {
		"type": "git",
		"url": "https://github.com/yujinjin/fans.git"
	},
	"scripts": {
		"R_DEV": "set NODE_RUN=1&&webpack-dev-server --progress --watch --inline --host=0.0.0.0  --port 8083",
		"B_DEV":"set NODE_ENV=dev&set NODE_RUN=0&webpack --progress --hide-modules",
		"lint": "eslint --ext .js,.vue src test/unit/specs test/e2e/specs"
	},
	"dependencies": {
		"vue": "^2.1.8",
		"vue-resource": "^1.0.3",
		"vue-router": "^2.0.1",
		"vue-html-loader": "1.2.3",
    	"vue-loader": "10.0.0",
    	"vue-style-loader": "^1.0.0",
    	"vue-template-compiler": "^2.1.0"
	},
	"devDependencies": {
		"vuex": "^2.0.0",
		"autoprefixer": "^6.4.0",
	    "babel-core": "^6.0.0",
	    "babel-eslint": "^7.0.0",
	    "babel-loader": "^6.0.0",
	    "babel-plugin-transform-runtime": "^6.0.0",
	    "babel-preset-es2015": "^6.0.0",
	    "babel-preset-stage-2": "^6.0.0",
	    "babel-register": "^6.0.0",
	    "babel-polyfill": "^6.22.0",
		"cross-env": "^1.0.6",
		"css-loader": "^0.25.0",
		"less": "^2.7.1",
		"less-loader": "^2.2.3",
		"file-loader": "^0.9.0",
		"html-loader": "^0.4.4",
		"html-webpack-plugin": "^2.24.1",
		"jshint": "^2.9.4",
		"jshint-loader": "^0.8.3",
		"style-loader": "^0.13.1",
		"url-loader": "^0.5.7",
		"extract-text-webpack-plugin": "^2.0.0-beta.4",
		"webpack": "^2.1.0-beta.25",
		"webpack-dev-server": "^2.1.0-beta.10",
		"webpack-require-http": "^0.4.0"
	},
	
	"engines": {
		"node": ">=5.0.0",
		"npm": ">=3.3.6"
	}
}