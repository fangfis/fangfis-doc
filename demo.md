**在上一节中我们已经尝试着开发了一个Demo<a href="/fangfis-doc/demo/">点此查看</a>，[源代码](https://github.com/fangfis/fangfis-demos)**

## 预览

<img src="https://ws2.sinaimg.cn/large/006tNc79ly1fhv5n5wc9mj30m50ffq33.jpg" style="box-shadow: 0 2px 6px rgba(0,0,0,.2)">

## 文件结构

```
├── dev                              // 开发目录
│   ├── css
│   │   └── style.css               // 样式
│   ├── images
│   └── js
│       ├── entry
│       │   └── entry_demo.js       // 入口文件
│       └── modules
│           └── addTitle.js         // 一些功能模块
└── index.html                      // 页面文件
├── package.json
└── static                          // 生产目录
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

## 功能点

**这个 Demo 主要有如下几个功能点：**

1.页面加载完成后插入标题`房天下 HTML5 视频播放器`和版权页脚`房天下前端 @ 2017`。[entry_demo.js#L7](https://github.com/fangfis/fangfis-doc/blob/master/demo/dev/js/entry/entry_demo.js#L7)

2.点击按钮时异步载入视频播放器并执行播放器的初始化。[entry_demo.js#L13](https://github.com/fangfis/fangfis-doc/blob/master/demo/dev/js/entry/entry_demo.js#L13)

## 关键实现
**下面来介绍关键代码实现**

1.引入Fang.js。[index.html#L18](https://github.com/fangfis/fangfis-doc/blob/master/demo/index.html#L18)

2.适用`fang.config()`方法设置`base`(js基本路径,一般指至http://***.com/**/static/js/)与 `ver` 版本号。[index.html#L21](https://github.com/fangfis/fangfis-doc/blob/master/demo/index.html#L21)

3.通过`fang()`调用页面入口文件。[index.html#L26](https://github.com/fangfis/fangfis-doc/blob/master/demo/index.html#L26)

4.入口文件中声明的模块ID必须与调用时一致，否则不会执行。例如`Demo`中的`entry/entry_demo`。[index.html#L26](https://github.com/fangfis/fangfis-doc/blob/master/demo/index.html#L26)应该与[entry_demo.js#L1](https://github.com/fangfis/fangfis-doc/blob/master/demo/dev/js/entry/entry_demo.js#L1)一致。

5.根据需要引入相对应的模块并执行。[entry_demo.js#L7](https://github.com/fangfis/fangfis-doc/blob/master/demo/dev/js/entry/entry_demo.js#L7)。

6.需要注意的是，正常通过require引入的依赖，需要在头部依赖列表里进行声明，否则无法正确获取到依赖。[entry_demo.js#L3](https://github.com/fangfis/fangfis-doc/blob/master/demo/dev/js/entry/entry_demo.js#L3)
