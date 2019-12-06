* postMessage报错： `"An object could not be cloned" when promise is rejected with an error"`, 因为postMessage传递的结构体包含了无法克隆的数据，比如传递了一个函数。

* 禁止这种写法： `{item.image ? <img alt="" src={require(`sprites/${item.image}.png`)} /> : item.text}`  ，这样会把所有的图片打包进来

* 关于JS的match方法使用：
 如果正则表达式不包含`g`标志位，`str.match()`将会返回和`RegExp.exec()`一样的结果。返回的`Array`有一个额外的`input`属性，其中就包含了传进来匹配的原始字符串。另外还有`index`属性，用来表示匹配文本的起始字符在原始字符串中的其起始位置。如果包含了`g`标志位，返回的`Array`包含了所有匹配上的子字符串而不是匹配对象。捕获组不会被返回。我们举个例子：

```
const str = 'str1.str2.str3'
str.match(/str/g)
// (3) ["str", "str", "str"]
str.match(/str/)
// ["str", index: 0, input: "str1.str2.str3", groups: undefined]
```

* `document.querySelectorAll`返回的是伪数组`NodeList`, 需要使用`Array.from`或者`[...result]`来转为数组，才可以使用Array的各种原生方法

* 浮点数相加或者相乘或者Number一个很大的数字都会出现错误，关于这个JS的语言缺陷请参考这篇文章[javascript语言数字运算缺陷](http://blog.5udou.cn/blog/javascriptYu-Yan-Shu-Zi-Yun-Suan-Que-Xian-19)

* 箭头函数是没有`arguments`参数的。所以你这样使用会报错的：

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

* 统计字符串某个字符出现的次数:

最快速的办法是直接以该字符为分隔符:  `str.split('a').length - 1`

* 统计字符串出现次数最多的字符(各个字符出现的次数)：
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

* 转换字符串的第一个字母为大写字母

`str.charAt(0).toUpperCase() + str.slice(1)`

* 快速让一个数组乱序：

```
var arr = [1,2,3,4,5,6,7,8,9,10];
    arr.sort(function(){
        return Math.random() - 0.5;
    })
```

* 数组快速去重

` [...new Set([2,"12",2,12,1,2,1,6,12,13,6])]`

另外还有其他几种方法，大家可以自行网上查找。

* 用`toFixed`来做四舍五入是有bug的。1.005.toFixed(2) 返回的是 1.00 而不是 1.01。因为1.005其实存储后变成了1.00499999999999989，四舍五入后小数点全都被抹掉了。解决办法可以记住第三方库去做这个四舍五入

* 如何快速查找这种深层次的对象？

```
[{
  children: [{
    label: '',
      value: '',
      children: [{
        value:'',
        lable:''
      }
    ]
  }],
  label: '',
  value: ''
}]
```

比如现在已知有个`value=2957`的值，想要查找它对应的lable是什么？最快速的办法不是去一层层遍历，而是这样：

```
const result = JSON.stringify(b).trim()
const index = result.match(/{"value":"2975","label":\"(.*?)\"}/)
```

* 记住：**spread解构是浅拷贝**

* 二维数字数字快速变为一维数组的方法：

```
const arr=[[1,2,3],[3,4],[5]];
[].concat.apply([],arr)
```

* 为了解决安卓手机输入框聚焦的时候弹出软键盘遮住输入框的问题
```
window.onresize = () => {
  const textArea = document.querySelector('.dui-textarea')
  textArea.scrollIntoViewIfNeeded(true)
  textArea.scrollIntoView(true)
}
```

* 关于`console.log(...)`的报错

这种错误铁定是因为你没有在`console.log`后面加上分号，导致后面的方法与`console.log`链式调用了，比如：

```
console.log()
(function(){})
```

**所以说加上分号还是很有用的~~~**

* typeof

```
// 错误的写法
if (v) {
  // ...
}
// ReferenceError: v is not defined

// 正确的写法
if (typeof v === "undefined") {
  // ...
}
```
