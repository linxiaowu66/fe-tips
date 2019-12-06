* `app.listen()` vs `require('http').createServer(app)`

其中的app是： `const app = express()`

第一种形式是会返回一个http服务器实例，你再使用这个实例去实现一些定制化的东西，而第二种形式是你主动去创建一个http服务器实例，这种写法在你想要重用http服务器实例的时候特别有用。

比如使用`socket.io`来创建长连接服务器可以有下面两种写法(推荐第一种)：

```
var express = require('express');
var app     = express();
var server  = require('http').createServer(app);
var io      = require('socket.io').listen(server);
...
server.listen(1234);
```
和

```
var express   = require('express');
var app       = express();

// app.use/routes/etc...

var server    = app.listen(3033);
var io        = require('socket.io').listen(server);

io.sockets.on('connection', function (socket) {
  ...
});
```

* 关于在nodejs中执行命令行，可能有些人没执行过，不清楚执行命令后参数的传入是如何的，这里演示一个例子，供参考：

**test.js**

```
#!/usr/bin/env node

console.log(process.argv)
```

那么当我执行`./test.js init -t abc -b add`的时候，`process.agrv`打印如下：

```
[ '/usr/local/Cellar/node/9.7.1/bin/node',
  '/Users/linxiaowu/SourceCode/panther/bin/panther.js',
  'init',
  '-t',
  'abc',
  '-b',
  'add' ]
```
