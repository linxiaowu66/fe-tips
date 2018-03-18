## 前言
前端行业的知识非常广泛，每一个方向都足够读者去花很多时间去学习。在平时的工作中，我们也会遇到很多稀奇古怪的问题，这篇文章的目的就是总结这些稀奇古怪的东西，以供大家学习，或许这里可以找到你想要的答案。

**本篇文章会持续更新，同步发表在[前端开发小知识点总结](http://blog.5udou.cn/blog/Qian-Duan-Kai-Fa-Xiao-Zhi-Shi-Dian-Zong-Jie-63)和[]**


### 1、浏览器
1.1 Chrome的device mode中鼠标左键点击的时候除了有touchStart事件，还会触发mouseDown事件，并且有300ms的延迟。这个时候如果你的某个元素检测mouseDown事件的话会被触发的

### 2、第三方库
2.1  [better-scroll]()无法滚动的原因大致有两点： scrollHeight <= wrapperHeight,  wrapper内部的数据发生改变但是代码没有调用refresh函数

2.2 [react]()使用数组的index作为key是一种反模式，所以强烈建议不要使用index作为key值，如下例子：


2.3 [pm2]()的配置文件中使用下面这个属性:

```
"interpreter": "/home/appdev/.nvm/versions/node/v8.9.4/bin/node",
```
必须与`"exec_mode": "fork"`搭配使用，如果使用`cluster`的话，这个解释器的配置是不生效的。

参考：  https://stackoverflow.com/questions/34682035/cluster-and-fork-mode-difference-in-pm2


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

### 4、兼容性
4.1 ios滚动不流畅的时候可以加一个样式试试看： `-webkit-overflow-scrolling:touch`


### 5. javascript
5.1 postMessage报错： `"An object could not be cloned" when promise is rejected with an error"`, 因为postMessage传递的结构体包含了无法克隆的数据，比如传递了一个函数。

5.2 禁止这种写法： `{item.image ? <img alt="" src={require(`sprites/${item.image}.png`)} /> : item.text}`  ，这样会把所有的图片打包进来

5.3 关于JS的match方法使用：
 如果正则表达式不包含`g`标志位，`str.match()`将会返回和`RegExp.exec()`一样的结果。返回的`Array`有一个额外的`input`属性，其中就包含了传进来匹配的原始字符串。另外还有`index`属性，用来表示匹配文本的起始字符在原始字符串中的其起始位置。如果包含了`g`标志位，返回的`Array`包含了所有匹配上的子字符串而不是匹配对象。捕获组不会被返回。我们举个例子：

```
const str = 'str1.str2.str3'
str.match(/str/g)
// (3) ["str", "str", "str"]
str.match(/str/)
// ["str", index: 0, input: "str1.str2.str3", groups: undefined]
```

5.4 `document.querySelectorAll`返回的是伪数组`NodeList`, 需要使用`Array.from`或者`[...result]`来转为数组，才可以使用Array的各种原生方法

5.5 浮点数相加或者相乘或者Number一个很大的数字都会出现错误，关于这个JS的语言缺陷请参考这篇文章[]()

5.6 箭头函数是没有`arguments`参数的。所以你这样使用会报错的：

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
