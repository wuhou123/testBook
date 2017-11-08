## 安装
- 下载[HBuilder IDE](http://www.dcloud.io/index.html)开发工具，其实HBuilder是dcloud 把eclipse的改造成一个专门应用于app打包、多种语言支持：php、jsp、ruby、python、nodejs等web语言，less、coffee等编译型语言均支持的开发工具

- 下载[node.js](https://nodejs.org/en/)，作为前端web的运行环境。我当前的node.js版本是6.10.0 npm版本是5.4.0


- app打包完全是基于manifest.json配置文件，它主要是用来配置app的基本信息（版本号、appid等）、图标(app的应用图标)、sdk配置、模块权限配置、页面引用关系、代码视图，具体参看dcloud提供的[文档](http://ask.dcloud.net.cn/docs/#//ask.dcloud.net.cn/article/94)。


### npm初始化
```
$1. 下载项目在当前项目目录下打开命令行工具输入

	npm install
	
	下载所有依赖，下载速度取决网速和硬件，下载完在执行后面的流程
```
![图片](http://osk1hpe2y.bkt.clouddn.com/17-11-8/27256342.jpg)
```
$2. 执行编译输入
	gulp dev 
```
![图片](http://osk1hpe2y.bkt.clouddn.com/17-11-8/43920328.jpg)


##### package.json内容如下

```
{
  "devDependencies": {
    "autoprefixer": "^7.1.1",
    "babel-core": "^6.25.0",
    "babel-loader": "^7.1.1",
    "babel-plugin-syntax-dynamic-import": "^6.18.0",
    "babel-preset-env": "^1.6.0",
    "css-loader": "^0.28.4",
    "gulp": "^3.9.1",
    "gulp-changed": "^2.0.0",
    "gulp-clean-css": "^3.0.3",
    "gulp-htmlmin": "^3.0.0",
    "gulp-load-plugins": "^1.5.0",
    "gulp-postcss": "^6.3.0",
    "gulp-pug": "^3.3.0",
    "gulp-stylus": "^2.6.0",
    "gulp-uglify": "^2.0.1",
    "minimist": "^1.2.0",
    "postcss-loader": "^2.0.6",
    "pug-loader": "^2.3.0",
    "stream-combiner2": "^1.1.1",
    "stylus-loader": "^3.0.1",
    "vinyl-named": "^1.1.0",
    "vue-loader": "^12.2.1",
    "vue-template-compiler": "^2.3.4",
    "webpack": "^2.6.1",
    "webpack-stream": "^3.2.0"
  },
  "browserslist": [
    "Android >= 4.4.3",
    "iOS >= 8"
  ],
  "dependencies": {
    "axios": "^0.16.2",
    "vue": "^2.3.4"
  }
}
```


## 调试
- chrome浏览器pc端调试和hbuilder真机调试，chrome调试比较方便(性能及查看代码显示比较好)，以下只以chrome浏览器讲解

### pc端开发调试
```
$1. 下载完所有依赖执行编译后打开build文件夹下login/login.html页面，打开chrome F12按键可以打开开发工具查看html的dom元素和样式以及js代码

$2. 命令行工具执行gulp dev监察代码改动，如果有报错，重新打开当前文件目录执行gulp dev，修改代码后，刷新页面就可以查看改动
```
### 移动端开发调试
```
$1. 用手机通过usb线连接电脑，打开hbuilder IDE的真机调试，可以查看数据输出和代码修改结果
```

![图片](http://osk1hpe2y.bkt.clouddn.com/17-11-8/78948281.jpg)