## 开发环境依赖模块说明

```
vue                            //构建用户界面的
vue-resource                   //vue 的http ajax请求插件（本项目没有用它，暂时保留）
vue-html-loader                //vue html加载器
vue-loader                     //vue加载器
vue-style-loader               //vue的样式加载器
vue-template-compiler          //vue的模板编译器
autoprefixer                   //css  浏览器兼容性问题处理
babel-core                     //ES6  代码转换器
babel-eslint                   //ES6的代码检查
babel-loader                   //ES6  代码转换器，webpack插件
babel-plugin-transform-runtime //和polyfill类似，替换助手函数
babel-preset-es2015            //ES6  代码编译成现在浏览器支持的ES5
babel-preset-stage-2           //ES6  ES7要使用的语法阶段
babel-register                 //用于改写require命令,为它加上一个钩子。此后,每当使用require加载.js、.jsx、.es和.es6后缀名的文件,就会先用Babel进行转码。
babel-polyfill                 //Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，babel-polyfill就是为当前环境提供一个垫片。解决一些浏览器不能识别的语法，比如：Promise
css-loader                     //css  生成
file-loader                    //webpack的文件加载器，主要用于字体  将字体文件打包
html-loader                    //webpack的html加载器，主要用于html文件的加载
pug-loader                     //pug模板语言加载器，主要用于.pug文件的加载
stylus-loader                  //css预处理加载器，主要用于.styl文件的加载
html-webpack-plugin            //html  文件编译
style-loader                   //webpack的style加载器，主要用于css  插入到style标签
url-loader                     //webpack的url加载器，主要用于图片加载及限制
webpack                        //用来构建打包程序
webpack-dev-server             //开发环境下，设置代理服务器
webpack-require-http           //webapck打包环境下的requrire加载http文件的插件
```
