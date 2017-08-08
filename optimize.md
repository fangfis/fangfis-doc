## 模块间的互相依赖

在模块的开发过程中，我们难免会遇到模块互相依赖调用的问题。在程序看来这似乎是一个死循环。但实际上，我们可以通过Fang.js提供的emit和on两个API来实现和规避循环调用的问题。

例如：

我们有两个模块a.js和b.js。

其中a模块里需要调用b模块里的two()方法，所以a可以直接依赖b。

但是与此同时b模块也需要调用a模块里的one()方法，此时就不能b也依赖a了。

但我们可能通过emit和on方法来规避这一个问题。

```javascript
// a.js
define(['b'], function () {
    var b = require('b');
    b.two();

    function one(user) {
        console.log('one: ', user);
    }

    // 监听callA事件
    fang.on('callA', function (user) {
        console.log('fang.on: ', user);
        one(user);
    })
})

// b.js
define(function () {
    var user = {name: jack,age: 25};
    // 触发callA事件
    fang.emit('callA', user);

    function two() {
        console.log('two');
    }

    return {two: two}
})
```

## 定义模块时，可以省略模块ID和依赖项

我们知道，一个完整定义的模块应该包括id、依赖和函数体，例如一下

```javascript
define('entry/entry_A', ['modules/a'], function () {
    let a = require('modules/a');
    // code here
});
```

但有的时候我们又觉得每个模块都需要声明id和依赖项好麻烦。其实这里的id声明和依赖是可以省略的，FangFIS会自动帮助添加。
```javascript
define(function () {
    let a = require('modules/a');
    // code here
});
```
