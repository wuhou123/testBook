## gulpfile.js 配置说明


```
var autoprefixer = require('autoprefixer'),
	combiner = require('stream-combiner2'), //整合 streams 来处理错误
	gulp = require('gulp'),
	named = require('vinyl-named'),
	path = require('path'),
	plugins = require('gulp-load-plugins')(),
	webpack = require('webpack'),
	webpackStream = require('webpack-stream');

var dest = 'build', proj = '.';

gulp.task('build', function() {//build任务,执行styles、scriptsRelease、htmls、resources对应样式，js文件，html文件，静态资源图片等打包任务
	gulp.start('styles','scriptsRelease','htmls', 'resources');
});

gulp.task('dev', ['watch'], function() {//监察任务，代码改动执行资源文件打包
	gulp.start('styles','scripts','htmls', 'resources');
});

gulp.task('xiaodou', function() {//其他业务任务
	proj = '../cheways_xiaodou';
	dest = '../cheways_xiaodou/build';
	gulp.start('copy','styles','scripts','htmls', 'resources');
});

gulp.task('copy',function(){
	gulp.src('common/**/*.*')
		.pipe(plugins.changed(proj+'/common'))
		.pipe(gulp.dest(proj+'/common'));
	gulp.src('lib/**/*.*')
		.pipe(plugins.changed(proj+'/lib'))
		.pipe(gulp.dest(proj+'/lib'));
	gulp.src('index.html')
		.pipe(plugins.changed(proj))
		.pipe(gulp.dest(proj));
});

gulp.task('watch',function(){
	gulp.watch('src/**/*.@(css|styl)',['styles']);
	gulp.watch('src/**/*.@(html|pug)',['htmls']);
	gulp.watch('src/**/*.@(png|jpg|ico|svg|gif)',['resources']);
	gulp.watch(proj+'/special/*.styl', ['styles']);
});

gulp.task('styles',function(){//styles命令处理css样式文件
	gulp.src('src/**/*.css')
		.pipe(plugins.changed(dest))
		.pipe(plugins.postcss([ autoprefixer({cascade: false}) ]))
		.pipe(plugins.cleanCss())
		.pipe(gulp.dest(dest));
	gulp.src('src/**/*.styl')
		.pipe(plugins.changed(dest,{extension:'.css'}))
		.pipe(plugins.stylus({paths: [proj+'/special']}))
		.pipe(plugins.postcss([ autoprefixer({cascade: false}) ]))
		.pipe(gulp.dest(dest));
	gulp.src('common/common.styl')
		.pipe(plugins.changed(proj+'/common',{extension:'.css'}))
		.pipe(plugins.stylus({paths: [proj+'/special']}))
		.pipe(gulp.dest(proj+'/common'));
});

gulp.task('scriptsRelease',function(){//scriptsRelease命令处理js文件压缩
	gulp.src('src/**/*.js')
		.pipe(named(function(file) {
			return file.relative.slice(0, -path.extname(file.path).length)
		}))
		.pipe(webpackStream({
			output: {
				publicPath: '../',
				chunkFilename: '[name].bundle.js',
			},
			resolve: {
				alias: {
					'vue$': 'vue/dist/vue.common.js',
					'cheways-vue$': '../../nopack/js/cheways-vue.js',
					'special': path.resolve(proj, 'special'),
				}
			},
			module: {
				rules: [
					{test: /\.vue$/, loader: 'vue-loader', options: {
						postcss: [require('autoprefixer')()]
					}},
					{test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" },
				]
			},
			plugins: [
				new webpack.DllReferencePlugin({
					context: __dirname,
					manifest: require('./lib/vendors-manifest.json')
				})
			],
		}, webpack))
		.pipe(plugins.uglify())
		.pipe(gulp.dest(dest));
});

gulp.task('scripts',function(){
	gulp.src('src/**/*.js')
		.pipe(named(function(file) {
			return file.relative.slice(0, -path.extname(file.path).length)
		}))
		.pipe(webpackStream({
			output: {
				publicPath: '../',
				chunkFilename: '[name].bundle.js',
			},
			resolve: {
				alias: {
					'vue$': 'vue/dist/vue.common.js',
					'cheways-vue$': '../../nopack/js/cheways-vue.js',
					'special': path.resolve(proj, 'special'),
				}
			},
			module: {
				rules: [
					{test: /\.vue$/, loader: 'vue-loader', options: {
						postcss: [require('autoprefixer')()]
					}},
					{test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" },
				]
			},
			plugins: [
				new webpack.DllReferencePlugin({
					context: __dirname,
					manifest: require('./lib/vendors-manifest.json')
				})
			],
			watch: true,
		}, webpack))
		.pipe(gulp.dest(dest));
});

gulp.task('htmls',function(){//htmls命令处理html文件压缩等
	gulp.src('src/**/*.html')
		.pipe(plugins.changed(dest))
		.pipe(plugins.htmlmin())
		.pipe(gulp.dest(dest));
	var combined = combiner.obj([
		gulp.src('src/**/*.pug'),
		plugins.changed(dest,{extension:'.html'}),
		plugins.pug({}),
		gulp.dest(dest)
	]);
	combined.on('error', console.error.bind(console));
});

gulp.task('resources',function(){//resources命令处理资源图片
	gulp.src('src/**/*.@(png|jpg|ico|svg|gif)')
		.pipe(plugins.changed(dest))
		.pipe(gulp.dest(dest));
});

gulp.task('dist', function() {
	var androidProject = 'C:/Users/XL/workspace/cheways-common3/assets/apps/HelloH5/www/'
	gulp.src('build/**/*.*')
		.pipe(plugins.changed(androidProject+'build'))
		.pipe(gulp.dest(androidProject+'build'))
	gulp.src('lib/**/*.*')
		.pipe(plugins.changed(androidProject+'lib'))
		.pipe(gulp.dest(androidProject+'lib'))
	gulp.src('special/**/*.*')
		.pipe(plugins.changed(androidProject+'special'))
		.pipe(gulp.dest(androidProject+'special'))
	gulp.src('manifest.json')
		.pipe(plugins.changed(androidProject+'www'))
		.pipe(gulp.dest(androidProject+'www'))
})

gulp.task('distas', function() {
	var androidProject = 'C:/Users/XL/AndroidStudioProjects/cheways-common3/app/src/main/assets/apps/HelloH5/www'
	gulp.src('build/**/*.*')
		.pipe(plugins.changed(androidProject+'build'))
		.pipe(gulp.dest(androidProject+'build'))
	gulp.src('lib/**/*.*')
		.pipe(plugins.changed(androidProject+'lib'))
		.pipe(gulp.dest(androidProject+'lib'))
	gulp.src('special/**/*.*')
		.pipe(plugins.changed(androidProject+'special'))
		.pipe(gulp.dest(androidProject+'special'))
	gulp.src('manifest.json')
		.pipe(plugins.changed(androidProject+'www'))
		.pipe(gulp.dest(androidProject+'www'))
})

```