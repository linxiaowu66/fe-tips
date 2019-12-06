*  可能用到meta标签
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

* input标签设置`type=number`在浏览器下留的坑，需求背景是input输入框只能输入数字，并且需要唤起数字键盘，于是想到用`type=number`和正则表达式来实现，但是浏览器偏偏不遂人意，当我输入诸如`.`或者`-`这类字符的时候便没有过滤掉，打印了当前的`e.target.value`的值竟然是空字符串，相关的bug这里有提及到： [传送门](https://github.com/facebook/react/issues/13752)。**最后终极解决办法便是使用`type=tel`这种.**

* input输入需要正则表达式去过滤某些字符的时候，不能纯粹使用onInput去实现，而是要借助onCompositionStart和onCompositionEnd去实现，比如某个输入框只允许输入中文和·：

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
