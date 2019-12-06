* `:first-line`伪类可以用来指定文本第一行的样式

* `position:absolute;left/right/bottom/top`全为0的时候元素的高度和宽度会和父元素一致，有时候比`height:100%;width:100%`更有效果

* image的尺寸容器的设置技巧，有时候UI童鞋会要求你展示一张图片，展示宽高比为3:1，或者其他比例，这个时候你可以使用padding-top的百分比特性去实现，比如：

```
.img{
  width: 100px;
   padding-top: 33.3%;
   height: 0px;
   ......
}
```
* flex layout中出现position:absolute的时候一些兼容性问题:

<script async src="//jsfiddle.net/linxiaowu/5q77ubkd/embed/html,css,result/dark/"></script>

参考： https://www.w3.org/TR/css-flexbox-1/#flex-item

* `:before`和`:after`伪类对于`textarea`标签是没有效果的，包括其他那些无法容纳其他元素的标签(比如`img`/`input`等)也是没有作用的。

* 文本输入框，包括`input`、`textarea`的隐藏方式，使用样式`opacity: 0`，用户如果误触到该区域，会弹出键盘，且输入文本有效，只是不可见而已，机缘巧合下，导致提交表单出现问题。所以隐藏这类元素建议使用`display: none`

* 在IOS手机端中，`-webkit-overflow-scrolling:touch`与`position:fixed`一起使用，父元素设置了`-webkit-overflow-scrolling:touch`，子元素设置`position:fixed`，会出现滚动到页面的上下界限时，会有一个白色块遮住拥有`position:fixed`属性的元素，解决方法是： 将拥有position:fixed属性的元素和-webkit-overflow-scrolling:touch的元素放在同一个层级，转换为兄弟节点，而不是父子节点。

* 弹窗遮罩，底部跟随滚动，也就是滚动透传，解决办法是在弹出层元素添加onTouchMove事件，调用e.preventDefault()方法

* `flex: 1`并不等同于`flex-grow: 1`，为了有更好的兼容性，建议都使用flex这种写法，而不要单独去设置`flex-grow`/`flex-shrink`/`flex-basis`，因为你只写其中一个，浏览器会去推算其他几个相关值，从而导致兼容性问题。

* ios滚动不流畅的时候可以加一个样式试试看： `-webkit-overflow-scrolling:touch`

* 图文不可复制的CSS写法：

```
-webkit-user-select: none;
-ms-user-select: none;
-moz-user-select: none;
-khtml-user-select: none;
user-select: none;
```

* div包裹img，但是div总会比img大3px，这是因为img标签是inline-block，所以浏览器总会让img的下边缘与基线对齐，而不是紧贴容器下边缘。解决办法便是设置`display: block`或者设置`vertical-align:bottom;`也是可以的

* 使用css实现下面这种两边尖三角形的效果：

![](https://blogimages2016.oss-cn-hangzhou.aliyuncs.com/web%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7/special-arrow.png)

```
// HTML代码：
<div className="sub-title">集冰点值赢大奖</div>

// CSS代码
.sub-title{
  transform: translateY(px2rem(130));
  display: flex;
  justify-content: center;
  margin: px2rem(7) auto px2rem(5) auto;
  color: #F96001;
  font-size: px2rem(14);
  font-weight: bold;
  align-items: center;
  &:before{
    content: '';
    border-top: px2rem(3) solid #F96001;
    border-left: px2rem(77) solid transparent;
    margin-right: px2rem(10);
  }
  &:after{
    content: '';
    border-top: px2rem(3) solid #F96001;
    border-right: px2rem(77) solid transparent;
    margin-left: px2rem(10);
  }
}
```
