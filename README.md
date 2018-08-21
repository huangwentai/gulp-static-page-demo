# gulp-static-page-demo
静态页面开发demo,包含less编译，自动刷新页面，引入文件，自动补足css兼容


## 安装
    npm install
    
## 使用   
    gulp

## gulpfile.js
```js
var gulp = require('gulp');
var fileinclude = require('gulp-advanced-file-include');
var less = require('gulp-less');
var connect = require('gulp-connect');
var autoprefixer = require('gulp-autoprefixer');
var gulpSequence = require('gulp-sequence');
var gulpHtmlVersion = require('gulp-html-version');
//include文件,完成后自動刷新頁面
gulp.task('concat',function() {
    return gulp.src([
    	"./content/*.html",
    	])
    .pipe(fileinclude({
        prefix: '@@',
        basepath: './content',
        context: {
            footerBetter: 'block',
            level1:"",
            level2:"",
            hasMsg:""
        }
    }))
    .pipe(gulpHtmlVersion({
        paramName: 'ver',
        paramType: 'timestamp',
        suffix: ['css', 'js', 'jpg', 'png']
    }))
    .pipe(gulp.dest('./'));
});
//刷新页面
gulp.task('refresh', function () {
    gulp.src(['./*.html','./assets/css/*.css'])
    .pipe(connect.reload());
});
//同步執行concat，refresh，[]里是異步執行
gulp.task('concat-list-refresh', function(cb) {
    gulpSequence('concat',['refresh'],cb);
});
//當content下的html改變時，執行concat-list-refresh任務
gulp.task("concatWatcher", function() {
    gulp.watch(['./content/*','./content/*/*'], ['concat-list-refresh']);
});
//同步執行less，refresh，[]里是異步執行
gulp.task('less-list-refresh', function(cb) {
    gulpSequence('less',['refresh'],cb);
});
//編譯less文件,autoprefixer自動添加css3前綴,完成后自動刷新頁面
gulp.task('less', function () {
    gulp.src(['assets/css/*.less'])
        .pipe(less())
        .pipe(autoprefixer({browsers:[
            'last 4 versions',
            'Android >= 4.0',
            'Firefox >= 10',
            'last 4 Explorer versions',
            'iOS 7', 
            'last 8 Safari versions',
            'Opera >= 10',
            '> 95%'
        ]}))
        .pipe(gulp.dest('assets/css'));
});
//當assets/css下的html改變時，執行less-list-refresh任務
gulp.task('testWatch', function () {
    gulp.watch('assets/css/*.less', ['less-list-refresh']);
});

//搭建webServer，http://localhost:8881，根目錄是gulpfile文件所在目錄
gulp.task('connect',function() {
    connect.server({
        root: './',
        livereload: true,
        port: 8882
    });
});

//default
gulp.task('default',["concat","concatWatcher","testWatch",'connect']);


```
