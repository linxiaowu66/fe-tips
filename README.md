## 前言
前端行业的知识非常广泛，每一个方向都足够读者去花很多时间去学习。在平时的工作中，我们也会遇到很多稀奇古怪的问题，这篇文章的目的就是总结这些稀奇古怪的东西，以供大家学习，或许这里可以找到你想要的答案。

**本篇文章会持续更新，同步发表在[前端开发小知识点总结](https://blog.5udou.cn/blog/Qian-Duan-Kai-Fa-Xiao-Zhi-Shi-Dian-Zong-Jie-63)和[github](https://github.com/linxiaowu66/fe-tips/tree/master)**

### 5、nodejs
5.1 `app.listen()` vs `require('http').createServer(app)`

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

5.2 关于在nodejs中执行命令行，可能有些人没执行过，不清楚执行命令后参数的传入是如何的，这里演示一个例子，供参考：

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


### 6. typescript
 6.1 下面这种自定义错误的写法会报两种错误：

```
 const error = new Error(resp.statusText)
 (error as any).specResp = msg
 throw error
```
错误：
1.  `Block-scoped variable 'error' used before its declaration`

2. `Element implicitly has an 'any' type because type 'Error' has no index signature`

修改为正确的形式：
```
class CustomError extends Error {
  specResp: string
  constructor(m: string) {
    super(m)
    this.specResp = m
  }
}
...
const error = new CustomError(resp.statusText)
error.specResp = msg
```

6.2 typescript使用require报错：
`Cannot find name 'require'`
解决办法是：
将`declare var require: (filename: string) => any `写到`index.d.ts`

6.3 在async函数中也是没有`arguments`参数：
```
async function() {
      console.log('calling.....', arguments)
}
```
会报错：
```
The 'arguments' object cannot be referenced in an async function or method in ES3 and ES5. Consider using a standard function or method
```
解决办法是:

```
async function(...args) {
      console.log('calling.....', args)
}
```

6.4 一些外部第三方包没有类型定义，于是你引用的时候就会报错，这时你需要这么配置才可以消除这个报错：

在`tsconfig.json`中修改，比如yeoman-environment就没有类型定义：

```
"paths": {
  "yeoman-environment": ["./types/yeoman-environment.d.ts"]
}
```

然后在types目录下新建一个yeoman-environment.d.ts的文件，里面只需要声明如下即可：

```
declare module 'yeoman-environment';
```

### 7、HTML
7.1  可能用到meta标签
```
 <!-- 设置缩放 -->
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, minimal-ui" />
    <!-- 可隐藏地址栏，仅针对IOS的Safari（注：IOS7.0版本以后，safari上已看不到效果） -->
    <meta name="apple-mobile-web-app-capable" content="yes" />
    <!-- 仅针对IOS的Safari顶端状态条的样式（可选default/black/black-translucent ） -->
    <meta name="apple-mobile-web-app-status-bar-style" content="black" />
    <!-- IOS中禁用将数字识别为电话号码/忽略Android平台中对邮箱地址的识别 -->
    <meta name="format-detection"content="telephone=no, email=no" />

    其他meta标签
    <!-- 启用360浏览器的极速模式(webkit) -->
    <meta name="renderer" content="webkit">
    <!-- 避免IE使用兼容模式 -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
    <meta name="HandheldFriendly" content="true">
    <!-- 微软的老式浏览器 -->
    <meta name="MobileOptimized" content="320">
    <!-- uc强制竖屏 -->
    <meta name="screen-orientation" content="portrait">
    <!-- QQ强制竖屏 -->
    <meta name="x5-orientation" content="portrait">
    <!-- UC强制全屏 -->
    <meta name="full-screen" content="yes">
    <!-- QQ强制全屏 -->
    <meta name="x5-fullscreen" content="true">
    <!-- UC应用模式 -->
    <meta name="browsermode" content="application">
    <!-- QQ应用模式 -->
    <meta name="x5-page-mode" content="app">
    <!-- windows phone 点击无高光 -->
    <meta name="msapplication-tap-highlight" content="no">
```

7.2 input标签设置`type=number`在浏览器下留的坑，需求背景是input输入框只能输入数字，并且需要唤起数字键盘，于是想到用`type=number`和正则表达式来实现，但是浏览器偏偏不遂人意，当我输入诸如`.`或者`-`这类字符的时候便没有过滤掉，打印了当前的`e.target.value`的值竟然是空字符串，相关的bug这里有提及到： [传送门](https://github.com/facebook/react/issues/13752)。**最后终极解决办法便是使用`type=tel`这种.**

7.3 input输入需要正则表达式去过滤某些字符的时候，不能纯粹使用onInput去实现，而是要借助onCompositionStart和onCompositionEnd去实现，比如某个输入框只允许输入中文和·：

```
// js代码
handleEdit = (type, e) => {
  const result = e.target.value.replace(/[^·\u4E00-\u9FA5]/g, '')
}

// html代码
<input placeholder="请输入真实姓名"
  value={riderName}
  maxLength={20}
  onCompositionStart={() => { this.inputLock = true }}
  onCompositionEnd={(e) => {
    this.inputLock = false
    this.handleEdit(e)
  }}
  onInput={(e) => {
    if (!this.inputLock) {
      this.handleEdit(e)
      e.returnValue = false;
    }
  }}
/>
```


### 8、WEB安全
8.1 CSRF攻击

CSRF跨站点请求伪造(Cross—Site Request Forgery)，攻击者盗用了你的身份，以你的名义发送恶意请求，对服务器来说这个请求是完全合法的，但是却完成了攻击者所期望的一个操作，比如以你的名义发送邮件、发消息，盗取你的账号，添加系统管理员，甚至于购买商品、虚拟货币转账等

8.2 XSS攻击

XSS攻击全称跨站脚本攻击(Cross Site Scripting)。XSS是指恶意攻击者利用网站没有对用户提交数据进行转义处理或者过滤不足的缺点，进而添加一些代码，嵌入到web页面中去。使别的用户访问都会执行相应的嵌入代码。有3个步骤：

  * 攻击者对含有漏洞的服务器发起XSS攻击（注入JS代码）
  * 诱使受害者打开受到攻击的服务器URL
  * 受害者在Web浏览器中打开URL，恶意脚本执行

8.3 DDOS攻击

DDOS攻击是指分布式拒绝服务攻击(Distributed Denial of Service)，这种攻击的方式是控制分布在全世界各个地方的服务器，发出指令，让这些服务器同时攻击一个目标，使得被攻击的计算机信息系统耗尽网络带宽资源、性能资源，无法正常运行或提供服务

### 9、前端测试

9.1 如果你使用typescript写的代码，那么就会需要用到`ts-node`这个包，目前这个包会在ts编译出错的时候(尽管这个错误不影响代码，只是ts的类型报错)就退出，暂不清楚这个行为是否可以忽略掉

9.2 在nyc中结合Mocha测试的时候，如果没有在测试文件中引入你要测试的文件，那么：
```
----------|----------|----------|----------|----------|-------------------|
File      |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
----------|----------|----------|----------|----------|-------------------|
All files |        0 |        0 |        0 |        0 |                   |
----------|----------|----------|----------|----------|-------------------|
```
可测试的文件为0，不要感到惊讶！

9.3 如果使用typescript写的代码进行单元测试并且要知道覆盖率的话，建议使用nyc,具体配置可以参考这个[awesome-js](https://github.com/linxiaowu66/awesome-js)

### 10、rocketMq

10.1 启动多个mq客户端的时候，注意consumer-group的名称设置问题

10.2 关于mq的幂等性问题

### 11、socket.io

11.1 集群部署的问题

### 12、测试工具

12.1、使用nc(Netcat)工具测试的时候，不要使用localhost这个当做host，因为nc会将localhost解析为ipv4和ipv6地址，除非你指定`-4`这个参数，这样的话如果你的服务器不支持ipv6，那么测试UDP的时候往往通不过，因为在IPV6的ICMP包的时候会返回Destination Unreachable的错误。而TCP因为会降级到IPV4，所以没有这种测试失败的问题。参考这个回答：[Trouble with netcat over UDP](https://superuser.com/questions/1238038/trouble-with-netcat-over-udp)
