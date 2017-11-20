> fangfis-cli 是一套与 fang.js 完善结合的自动化代码构建工具。

fangfis-cli 是一个基于 Node 的前端构建工具，根据房天下前端开发需求进行了相应的定制，支持 CSS 的压缩、JS 的 ES6 转 ES5、合并、压缩、混淆等功能，通过 FangFIS 结合 fang.js，我们可以方便快速地处理前端静态资源文件，减少了多余的工作，提高开发效率和代码性能。

> 需要注意，本地开发的代码，必须先通过 fangfis-cli 进行构建才能发布到正式环境。

## 安装

fangfis-cli 基于 Node.js，因此在开始安装之前，确保你的系统安装了 Node.js 环境。(Nodejs >= 4.x, npm > 3.x)

```bash
$ npm install -g fangfis-cli
```

## 使用

输入项目名称，默认为空即在当前文件夹下初始化。
```bash
$ fangfis init
```

![](https://ws3.sinaimg.cn/large/006tNc79ly1fhv65yfla1j307f012dfm.jpg)

该文件夹下不为空提示，可以选择继续，不会删除已有文件。

![](https://ws4.sinaimg.cn/large/006tKfTcly1fj9k1qnrm6j30o007gmxj.jpg)

构建完成后默认自动安装所需要的依赖模块，如果自动安装失败，请进入该目录，手动安装，推荐使用cnpm安装 参考地址: [cnpm](https://npm.taobao.org/)

```bash
$ npm install
or
$ cnpm install
```

初始化完成后的结构


```
.
├── dev
│   ├── css
│   ├── images
│   └── js
├── fangfis.config.json
├── package-lock.json
├── package.json
└── static
5 directories, 3 files
```
注:
其中dev是开发源目录，其子目录有css样式目录，img图片目录和js文件目录。static为构建目录，构建工具以dev为源目录，把构建后的css,img,js放到static相应的目录中，package.json是Node包配置文件，node_modules是安装的Node包。

dev文件夹目前版本为构建工具预留目录，所有开发项目必须在该目录下开发，下面的css、js和images文件夹的名字不能更改。

### 开始编码

我们先创建一个`index.html`文件在项目根目录下

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=0" />
    <title>FangJS startup demo</title>
    <link rel="stylesheet" type="text/css" href="static/css/style.css">
</head>

<body>
    <div class="center">
        <button class="button" id="loadV">点我加载播放插件</button>
    </div>
    <div id="v" class="video"></div>
    <!-- 引入Fang.js -->
    <script src="//static.soufunimg.com/common_m/pc_public/fangjs/build/??fang2.3.2.js,jquery/jquery-3.js"></script>
    <script>
    // 配置Fang.js
    fang.config({
        base: './static/js',
        ver: '2017071501'
    });
    // 调用入口模块
    fang(['entry/entry_demo'], function () {
        console.log('entry_demo 执行完成');
    });
    </script>
</body>

</html>
```

然后在`dev/css`下创建一个`style.css`

```css
.pageTitle {
    text-align: center;
}
.video {
    width: 500px;
    margin: 0 auto;
}
.center {
    text-align: center;
}
.button {
    border: 0;
    background-color: #08c;
    color: #fff;
    margin-bottom: 20px;
    outline: 0;
}
.copyright {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    margin: 0;
    text-align: right;
    color: #aaa;
}
```

继续在`dev/js/entry/`下创建一个`entry_demo.js`

```javascript
define('entry/entry_demo', [
    'jquery',
    'modules/addTitle'
], function(require) {
    'use strict';
    console.log('demo_entry');
    let addTitle = require('modules/addTitle');
    addTitle.title('pageTitle', '房天下 HTML5 视频播放器');
    addTitle.copyRight('房天下前端 @ 2017');

    // 视频播放插件的异步调用
    $('#loadV').on('click', function () {
        // 加载播放器
        require.async('//static.soufunimg.com/common_m/pc_public/fangplayer/build/fangPlayer.js', function (fangPlayer) {
            // 视频地址
            let videourl = 'http://106.38.250.142/xdispatch/7xp6cu.dl1.z0.glb.clouddn.com/360.mp4';
            // 封面图
            let urlphoto = 'http://7xih9g.com1.z0.glb.clouddn.com/countdown-clock.png';
            // 视频id ，电商详情为空
            let playertype = 'html5';

            window.fangPlayer = new fangPlayer({
                // debug 模式，可选
                debug: true,
                // 插入播放器的DOM，必填
                holder: '#v',
                // html5视频封面图，可选
                poster: urlphoto,
                // html5视频播放地址，必填
                src: videourl,
                // 播放器类型
                type: playertype
            });
        })
    });
});
```

在`dev/js/modules/`下创建`addTitle.js`
```javascript
// addTitle.js
define('modules/addTitle', [
    'jquery'
], function (require) {
    'use strict';
    console.log('modules/addTitle');

    let util = {
        title: function (cls, txt) {
            $('body').prepend(`<h2 class="${cls}">${txt}</h2>`);
        },
        copyRight: function (txt) {
            $('body').append(`<p class="copyright">${txt}</p>`);
        },
        append: function (txt) {
            $('body').append(`<span>${txt}</span>`);
        }
    };
    return util;
});
```

### 构建配置

init自动生成默认配置文件fangfis.config.json,用户可以自定义配置选项以控制构建,配置结构如下:

```
{
    // 构建配置项
    "build": {
        // 自定义输入目录,相对路径,默认为dev 注: 自定义路径下需要有js,images,css文件夹
        "input": "./dev",
        // 自定义输出目录,默认为static
        "output": "./static",
        // 模块基本路径,构建工具会自动根据该基本路径进行寻找模块
        "base": "./dev/js",
        // 入口文件支持多种格式
        /* *main
            main,main2
            main,main2,ery*
            module/js/main
            module/js/main,module/js/main2
            module/js/main,module/js/main2,module/js/ery*
            main*
            *main
            main_*tool
        */
        "main": "main",
        // 模块合并配置项
        "combo": {
            // 忽略模块,默认jquery
            "ignore": [
                "jquery"
            ],
            // 需要额外处理的模块,格式同main
            "addMod": ["main2"],
            // fangjs配置
            "config": {
                "alias": {
                    "jquery": "jquery",
                    "util": "plugins/util"
                },
                "paths": {
                    "webim": "//js.soufunimg.com/upload/webim"
                }
            }
        }
    },
    // 上传ftp配置
    "upload": {
        // 默认配置
        "default": {
            // host
            "host": "localhost",
            // 用户名
            "user": "anonymous",
            // 密码
            "pass": "anonymous@",
            // 本地待上传目录
            "input": "./static",
            // ftp路径 相对于ftp根路径
            "output": "./static",
            // 端口
            "port": 21,
            // 连接超时时间 单位:分钟
            "idleTimeout": 100,
            // 最大连接数
            "maxConnections": 5
        },
        // 同default
        "online": {
            "host": "localhost",
            "user": "anonymous",
            "pass": "anonymous@",
            "input": "./static",
            "output": "./static",
            "port": 21,
            "idleTimeout": 100,
            "maxConnections": 5
        }
    }
}
```

### fangfis build

```bash
fangfis build
```

使用fangfis进行构建。

这条命令主要做了两件事：

1.把dev/css下的的css全部压缩，输出到static/css下

2.把dev/js下的js全部进行ES6 => ES5，混淆和压缩。另外所有`entry_`开头的js都会把对应的依赖项合并给自己，以减少JS文件的请求，加快页面加载速度。

具体这条命令其它用法，可以参考[FangFIS构建](deploy.md)部分的介绍。

接着我们在浏览器打开`index.html`就能够看到一个如图所示的界面：
本demo的路径为`//fangfis.github.io/fangfis-doc/demo/`。<a href="http://activities.m.fang.com/fangfis-doc/demo/">点此查看</a>

<img src="https://ws2.sinaimg.cn/large/006tNc79ly1fhv5n5wc9mj30m50ffq33.jpg" style="box-shadow: 0 2px 6px rgba(0,0,0,.2)">

> 需要注意的是，如果网页的目录（域名）与js的目录（域名）不相同，`base`就必须设置为一个独立的URI,而不是一个相对地址。

> 当然，如果你打开本地的`index.html`也是正常的，因此静态资源文件的路径并没有错。具体可以通过浏览器开发者工具的`Network`一栏查看具体加载情况和路径。

## 项目结构

此时我们可以看见，项目目录下的文件夹变成了如下结构：
其中，我们之前编写的代码都在`dev`下，但是页面引用的文件都在`static`下，是因为前面我们执行的`fang build`操作将`dev`对应的代码都编译并压缩到了`static`下。

其中`static/js/maps`下的文件为`sourcemaps`生成的记录文件，因为原始代码进行了`ES6 => ES5`并进行了压缩，如果加入`maps`方便对压缩代码进行调试。生产环境可以不传`sourcemaps`文件。

```
├── dev
│   ├── css
│   │   └── style.css
│   ├── images
│   └── js
│       ├── entry
│       │   └── entry_demo.js
│       └── modules
│           └── addTitle.js
├── index.html
├── package.json
└── static
    ├── css
    │   └── style.css
    └── js
        ├── entry
        │   └── entry_demo.js
        ├── maps
        │   ├── entry
        │   │   └── entry_demo.js.map
        │   └── modules
        │       └── addTitle.js.map
        └── modules
            └── addTitle.js
```

## 更多

以上便是一个简单的`Demo`例子。编写代码，编译，发布。此时线上的代码便是经过兼容编译和压缩过的`JS`和`CSS`。

相关FangFIS更多的使用方法，可以参考 [实例解读](https://fangfis.github.io/fangfis-doc/#/demo)





构建选项如下：

```bash
    -w, --watch           监听文件变化并自动构建
    -j, --js              压缩js文件到目标文件夹,入口文件`自动合并`所有依赖到目标文件夹,默认: static/js
    -c, --css             压缩所有css文件到目标目录,输入目录为`dev/css`,不可自定义,输出目录可自定义,默认: static/css
    -i, --img             拷贝所有img文件到目标目录,输入目录为`dev/images`,不可自定义,输出目录可自定义,默认: static/imgages
    -o, --output [value]  自定义输入目录,可自定义到一级,默认: ./static
    -m, --main [value]    自定义入口文件,只能传入正则表达式,默认: /^entry_.*\.js$/i
    -h, --help            帮助信息
```

例子：

```bash
$ fangfis b -o ./test1 -cjiw
or
$ fangfis build -o ./test1 -c -j -i -w
```

**建议使用默认输出目录**

### fangfis upload

使用fangfis 上传ftp

```bash
    -H, --host [value] FTP host, 默认为localhost
    -u, --user [value] FTP user, 默认为anonymous
    -p, --pass [value] FTP password, 默认为anonymous@
    -o, --output [value] FTP 上传目标路径,相对FTP根路径, 默认为./static
    -i, --input [value] FTP 上传源路径路径,相对本地路径, 默认为./static
    -t, --type [value] FTP连接类型,一般分为测试和正式. 默认为default
    -P, --port [value] FTP 端口, 默认为21
    -T, --idleTimeout [value] FTP连接超时时间, 默认100
    -m, --maxConnections [value] FTP最大连接数. 默认为5
```
### 页面调用
创建一个index.html, 加入以下代码进行调用。
```
 <!-- 引入未集成jQuery的fangjs -->
    <script src="http://static.soufunimg.com/common_m/pc_public/fangjs/build/??fang2.3.2.js,jquery/jquery-3.js"></script>
    <script>
    // 配置Fang.js, 其中base为js的基础目录，可以相对目录，也可以独立的域名。
    fang.config({
        base: './static/js',
        ver: '2017071501'
    });
    // 调用入口模块
    fang(['entry/entry_demo'], function () {
        console.log('entry_demo 执行完成');
    });
    </script>
```
entry_demo 示例代码如下：

```
  fang('entry/entry_demo', function () {
      console.log('调用entry_demo');
  });
```
