### 常用命令

```bash
npm install -g hexo-cli
npm update -g hexo-cli
npm install hexo-deployer-git --save  # 安装git部署插件

hexo init  # 初始化
hexo n [layout] ""   # 新建文件  layout-布局方式
hexo cl  # cl==clean 清楚缓存
hexo publish 
hexo generate  # 生成静态网页
hexo server  # 启动服务并预览
hexo deploy  # 部署

hexo d -g # 生成并部署
hexo s -g #生成并预览
```

layout布局方式有三种
- post  创建的是markdown文件
- page  创建的是网站的菜单项
- drad  草稿布局

在_config.yml文件中添加如下
```bash
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: https://github.com/username/username.github.io.git
  branch: master
```

### 添加RSS功能

#### 安装插件 hexo-generator-feed

> 在hexo 目录运行命令：

```
npm install hexo-generator-feed --save
```

#### 配置`站点配置文件`, hexo目录下`_config.yml`文件中配置

> 在站点配置文件添加：

```
# rss配置
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
```

#### 配置Next`主题配置文件`

> 主题配置文件默认没有rss字段，需要自己手动添加

```
# Set rss to false to disable feed link.
# Leave rss as blank to use site's feed link, and install hexo-generator-feed: `npm install hexo-generator-feed --save`.
# Set rss to specific value if you have burned your feed already.
rss: /atom.xml
```

#### 测试

> 此时运行hexo g已经可以看到

```
INFO  Generated: atom.xml
```

> 事实上已经配置成功,但是侧边栏没有RSS按钮.
>

#### 在侧边栏添加RSS按钮

> 在Hexo/themes/next/layout/_partials/sidebar文件夹，打开site-overview.swig文件
>
> 在`- if theme.site_state `前加入代码:

```
{% if theme.rss %}
   <div class="feed-link motion-element">
     <a href="{{ url_for(theme.rss) }}" rel="alternate">
       <i class="fa fa-rss"></i>
       RSS
     </a>
   </div>
 {% endif %}
```

> 执行hexo g && hexo s 即可查看效果

### 安装配置gulp

1、安装gulp
`npm install --global gulp-cli`

2、安装gulp模块

```shell
npm install gulp --save
npm install gulp-htmlclean gulp-htmlmin gulp-clean-css gulp-uglify gulp-imagemin --save
npm install gulp-babel babel-preset-env babel-preset-mobx --save
npm install -D @babel/core @babel/preset-react @babel/preset-env --save
```

最终生成的package.json为：

```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "build": "hexo generate",
    "clean": "hexo clean",
    "deploy": "hexo deploy",
    "server": "hexo server"
  },
  "hexo": {
    "version": "4.2.0"
  },
  "dependencies": {
    "babel-preset-env": "^1.7.0",
    "babel-preset-mobx": "^2.0.0",
    "gulp": "^4.0.2",
    "gulp-babel": "^8.0.0",
    "gulp-clean-css": "^4.2.0",
    "gulp-htmlclean": "^2.7.22",
    "gulp-htmlmin": "^5.0.1",
    "gulp-imagemin": "^7.1.0",
    "gulp-uglify": "^3.0.2",
    "hexo": "^4.0.0",
    "hexo-generator-archive": "^1.0.0",
    "hexo-generator-baidu-sitemap": "^0.1.6",
    "hexo-generator-category": "^1.0.0",
    "hexo-generator-feed": "^2.2.0",
    "hexo-generator-index": "^1.0.0",
    "hexo-generator-searchdb": "^1.2.0",
    "hexo-generator-sitemap": "^2.0.0",
    "hexo-generator-tag": "^1.0.0",
    "hexo-neat": "^1.0.4",
    "hexo-renderer-ejs": "^1.0.0",
    "hexo-renderer-marked": "^2.0.0",
    "hexo-renderer-stylus": "^1.1.0",
    "hexo-server": "^1.0.0"
  },
  "devDependencies": {
    "@babel/core": "^7.8.4",
    "@babel/preset-env": "^7.8.4",
    "@babel/preset-react": "^7.8.3"
  }
}
```

4、在hexo目录创建gulpfile.js，内容为：

```js
let gulp = require('gulp')
let cleanCSS = require('gulp-clean-css')
let htmlmin = require('gulp-htmlmin')
let htmlclean = require('gulp-htmlclean')
let babel = require('gulp-babel') /* 转换为es2015 */
let uglify = require('gulp-uglify')
let imagemin = require('gulp-imagemin')

// 设置根目录
const root = './public'

// 匹配模式， **/*代表匹配所有目录下的所有文件
const pattern = '**/*'

// 压缩html
gulp.task('minify-html', function() {
  return gulp
    // 匹配所有 .html结尾的文件
    .src(`${root}/${pattern}.html`)
    .pipe(htmlclean())
    .pipe(
      htmlmin({
        removeComments: true,
        minifyJS: true,
        minifyCSS: true,
        minifyURLs: true
      })
    )
    .pipe(gulp.dest('./public'))
})

// 压缩css
gulp.task('minify-css', function() {
  return gulp
    // 匹配所有 .css结尾的文件
    .src(`${root}/${pattern}.css`)
    .pipe(
      cleanCSS({
        compatibility: 'ie8'
      })
    )
    .pipe(gulp.dest('./public'))
})

// 压缩js
gulp.task('minify-js', function() {
  return gulp
    // 匹配所有 .js结尾的文件
    .src(`${root}/${pattern}.js`)
    .pipe(
      babel({
        presets: ['env']
      })
    )
    .pipe(uglify())
    .pipe(gulp.dest('./public'))
})

// 压缩图片
gulp.task('minify-images', function() {
  return gulp
    // 匹配public/images目录下的所有文件
    .src(`${root}/images/${pattern}`)
    .pipe(
      imagemin(
        [
          imagemin.gifsicle({ optimizationLevel: 3 }),
          imagemin.jpegtran({ progressive: true }),
          imagemin.optipng({ optimizationLevel: 7 }),
          imagemin.svgo()
        ],
        { verbose: true }
      )
    )
    .pipe(gulp.dest('./public/images'))
})

gulp.task('default', gulp.series('minify-html', 'minify-css', 'minify-js'))
```

4、执行压缩
`gulp`

#### 命令精简

使用了gulp时候，构建发布需要四个命令：

```shell
hexo clean
hexo g
gulp
hexo d
```

这四个命令，可以都写在package.json。

```javascript
"scripts": {
  "build": "hexo clean && hexo g && gulp",
  "deploy": "hexo clean && hexo g && gulp && hexo d"
}
```

构建只需要执行`npm run build`，构建发布只需要执行`npm run deploy`。