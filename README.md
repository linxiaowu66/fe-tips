## 前言
前端行业的知识非常广泛，每一个方向都足够读者去花很多时间去学习。在平时的工作中，我们也会遇到很多稀奇古怪的问题，这篇文章的目的就是总结这些稀奇古怪的东西，以供大家学习，或许这里可以找到你想要的答案。

**本篇文章会持续更新，同步发表在[前端开发小知识点总结](http://blog.5udou.cn/blog/Qian-Duan-Kai-Fa-Xiao-Zhi-Shi-Dian-Zong-Jie-63)和[github](https://github.com/linxiaowu66/fe-tips/tree/master)**


### 1、浏览器
1.1 Chrome的device mode中鼠标左键点击的时候除了有touchStart事件，还会触发mouseDown事件，并且有300ms的延迟。这个时候如果你的某个元素检测mouseDown事件的话会被触发的

### 2、第三方库
2.1  [better-scroll]()无法滚动的原因大致有两点： scrollHeight <= wrapperHeight,  wrapper内部的数据发生改变但是代码没有调用refresh函数

2.2 [react]()使用数组的index作为key是一种反模式，所以强烈建议不要使用index作为key值，如下例子：

**http://jsbin.com/wohima/edit?js,output**

2.3 [pm2]()的配置文件中使用下面这个属性:

```
"interpreter": "/home/appdev/.nvm/versions/node/v8.9.4/bin/node",
```
必须与`"exec_mode": "fork"`搭配使用，如果使用`cluster`的话，这个解释器的配置是不生效的。

参考：  https://stackoverflow.com/questions/34682035/cluster-and-fork-mode-difference-in-pm2

2.4 在webpack配置[uglifJs]()后打包报下面这种错误的时候：

```
xxx-a69de75d299773b2d645.js from UglifyJs
undefined
```

那是因为你在配置uglifyjs的时候把`parallel: true`  打开了，关掉之后详细的报错内容就会打印出来的。

```
xxx-a69de75d299773b2d645.js from UglifyJs
TypeError: sym.definition is not a function
    at may_modify (eval at <anonymous> (/root/SourceCode/dianwoda-shop-app/node_modules/uglify-es/tools/node.js:21:1), <anonymous>:8931:31)
......
```

### 3、CSS
3.1 `:first-line`伪类可以用来指定文本第一行的样式

3.2 `position:absolute;left/right/bottom/top`全为0的时候元素的高度和宽度会和父元素一致，有时候比`height:100%;width:100%`更有效果

3.3 image的尺寸容器的设置技巧，有时候UI童鞋会要求你展示一张图片，展示宽高比为3:1，或者其他比例，这个时候你可以使用padding-top的百分比特性去实现，比如：

```
.img{
  width: 100px;
   padding-top: 33.3%;
   height: 0px;
   ......
}
```
3.4 flex layout中出现position:absolute的时候一些兼容性问题:

<script async src="//jsfiddle.net/linxiaowu/5q77ubkd/embed/html,css,result/dark/"></script>

参考： https://www.w3.org/TR/css-flexbox-1/#flex-item

3.5 `:before`和`:after`伪类对于`textarea`标签是没有效果的，包括其他那些无法容纳其他元素的标签(比如`img`/`input`等)也是没有作用的。

3.6 文本输入框，包括`input`、`textarea`的隐藏方式，使用样式`opacity: 0`，用户如果误触到该区域，会弹出键盘，且输入文本有效，只是不可见而已，机缘巧合下，导致提交表单出现问题。所以隐藏这类元素建议使用`display: none`

3.7 在IOS手机端中，`-webkit-overflow-scrolling:touch`与`position:fixed`一起使用，父元素设置了`-webkit-overflow-scrolling:touch`，子元素设置`position:fixed`，会出现滚动到页面的上下界限时，会有一个白色块遮住拥有`position:fixed`属性的元素，解决方法是： 将拥有position:fixed属性的元素和-webkit-overflow-scrolling:touch的元素放在同一个层级，转换为兄弟节点，而不是父子节点。

3.8 弹窗遮罩，底部跟随滚动，也就是滚动透传，解决办法是在弹出层元素添加onTouchMove事件，调用e.preventDefault()方法

3.9 `flex: 1`并不等同于`flex-grow: 1`，为了有更好的兼容性，建议都使用flex这种写法，而不要单独去设置`flex-grow`/`flex-shrink`/`flex-basis`，因为你只写其中一个，浏览器会去推算其他几个相关值，从而导致兼容性问题。

3.10 ios滚动不流畅的时候可以加一个样式试试看： `-webkit-overflow-scrolling:touch`

### 4. javascript
4.1 postMessage报错： `"An object could not be cloned" when promise is rejected with an error"`, 因为postMessage传递的结构体包含了无法克隆的数据，比如传递了一个函数。

4.2 禁止这种写法： `{item.image ? <img alt="" src={require(`sprites/${item.image}.png`)} /> : item.text}`  ，这样会把所有的图片打包进来

4.3 关于JS的match方法使用：
 如果正则表达式不包含`g`标志位，`str.match()`将会返回和`RegExp.exec()`一样的结果。返回的`Array`有一个额外的`input`属性，其中就包含了传进来匹配的原始字符串。另外还有`index`属性，用来表示匹配文本的起始字符在原始字符串中的其起始位置。如果包含了`g`标志位，返回的`Array`包含了所有匹配上的子字符串而不是匹配对象。捕获组不会被返回。我们举个例子：

```
const str = 'str1.str2.str3'
str.match(/str/g)
// (3) ["str", "str", "str"]
str.match(/str/)
// ["str", index: 0, input: "str1.str2.str3", groups: undefined]
```

4.4 `document.querySelectorAll`返回的是伪数组`NodeList`, 需要使用`Array.from`或者`[...result]`来转为数组，才可以使用Array的各种原生方法

4.5 浮点数相加或者相乘或者Number一个很大的数字都会出现错误，关于这个JS的语言缺陷请参考这篇文章[javascript语言数字运算缺陷](http://blog.5udou.cn/blog/javascriptYu-Yan-Shu-Zi-Yun-Suan-Que-Xian-19)

4.6 箭头函数是没有`arguments`参数的。所以你这样使用会报错的：

```
const a = () => {console.log(arguments)}
a()
```
报错：
```
Uncaught ReferenceError: arguments is not defined
    at a (<anonymous>:1:30)
    at <anonymous>:1:1
```

4.7 统计字符串某个字符出现的次数:

最快速的办法是直接以该字符为分隔符:  `str.split('a').length - 1`

4.8 统计字符串出现次数最多的字符(各个字符出现的次数)：
```
const str = 'fhsdfhdsofsifjisffs'
const arr = str.split(''); //把字符串转换为数组
//首先进行排序，这样结果会把相同的字符放在一起，然后再转换为字符串
const str = arr.sort().join('')
const value = ''
const index = 0
//匹配字符，且重复这个字符，重复次数至少一次
const re = /(\w)\1+/g
str.replace(re, function ($0, $1) {
  console.log($0, $1)
  //如果index保存的值小于$0的长度就进行下面的操作
  if (index < $0.length) {
    index = $0.length; 
    value = $1; 
 }
})
```

以此类推，计算各个字符出现的次数

**Note**：

    replace方法,第二个参数使用的是function的话,如果正则表达式有g的标志,那么每次匹配上都会被调用一次. 然后函数里面的形参是:
    1. match: 本次匹配到的结果
    2. $1,...$9: 正则表达式中有几个()，就会传递几个参数，$1~$9分别代表本次匹配中每个()提取的结果，最多9个
    3. offset: 记录本次匹配的开始位置
    4. source:接受匹配的原始字符串

4.9 转换字符串的第一个字母为大写字母

`str.charAt(0).toUpperCase() + str.slice(1)`

4.10 快速让一个数组乱序：

```
var arr = [1,2,3,4,5,6,7,8,9,10];
    arr.sort(function(){
        return Math.random() - 0.5;
    })
```

4.11 数组快速去重

` [...new Set([2,"12",2,12,1,2,1,6,12,13,6])]`

4.12 toFixed 来做四舍五入是有bug的。1.005.toFixed(2) 返回的是 1.00 而不是 1.01。因为1.005其实存储后变成了1.00499999999999989，四舍五入后小数点全都被抹掉了。解决办法可以记住第三方库去做这个四舍五入

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

### 7、HTML
1. 可能用到meta标签
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
