# 配置项

目前 fang.js 提供 2.3.2 版本，提供了集成 jQuery 和未集成 jQuery 两个版本，引入方式非常简单。可以通过以下介绍进行了解：

## 2.3.2

该版本需要先引入fangjs以后再进行配置，开发者可根据页面的情况进行配置。

如下配置并引入 fang.js 以后执行 `fang('入口文件')` ， fang.js 会根据 `base` 的值到对应路径下加载代码。

* **未集成jQuery：**

```
http://static.soufunimg.com/common_m/pc_public/fangjs/build/fang2.3.2.js
```

* **集成jQuery：**

```
http://static.soufunimg.com/common_m/pc_public/fangjs/build/??fang2.3.2.js,jquery/jquery-3.js
```

参考以下代码：

```html
    <!-- 引入未集成jQuery的fangjs -->
    <script src="http://static.soufunimg.com/common_m/pc_public/fangjs/build/??fang2.3.2.js,jquery/jquery-3.js"></script>
    <script>
    // 配置fang.js
    fang.config({
        base: location.href + '/static/js',
        ver: '2017071501'
    });
    // 调用入口模块
    fang(['entry/entry_demo'], function () {
        console.log('entry_demo 执行完成');
    });
    </script>
```

## 更多

更多配置项请参考：<a href="#/api?id=fangconfigoptions">fang.config()</a>