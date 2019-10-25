下面罗列的js语言中那些让人抓狂混淆的概念，你遇到过几个？

**建议收藏此文，每当要面试的时候提前拿出来温习温习巩固巩固，多次下来，这些概念相信会永远印在你的脑海中~~**

*每一小节都会有一道对应的练习题供参考，如果你做出的答案和题目的答案一样，那么这一小节的内容相信你已经掌握了*

## 1、Array.prototype.slice和Array.prototype.splice

### 1.1、练习题
```
const months = ['Jan', 'March', 'April', 'June'];
const res1 = months.splice(1, 0, 'Feb');
const res2 = months.splice(4, 1, 'May');
const res3 = months.splice(3)

console.log(months, res1, res2, res3)

const fruits = ['apple', 'orange', 'cherry']
const res4 = fruits.slice(0, 2)

console.log(fruits, res4)

// 输出结果应该是：
// ['Jan', 'Feb', 'March'] [] ['June'] ['April', 'May']
// ['apple', 'orange', 'cherry'] ['apple', 'orange']
```
### 1.2、总结
这两个方法名字何其相似，想区分开来蛮费劲的，最后能够记住的就是口诀是：`放个p，就是不一样`，话糙理不糙~接下去我们好好总结一下二者的区别

二者共同点都是从数组中提取指定的一段范围内的数据，但是有三个不同点：

* 传参不一样，splice的入参是 start & count。而slice的入参是start & end，**但是记住end是开区间**
* splice是会将原数组改造掉，而slice是不会的
* splice还有另外一个用途，可以实现替换或者插入，其形参的第二个参数之后的都是替换或者插入的值，比如：`months.splice(1, 0, 'Feb')`
  * count是 0 或者负数，则不移除元素。这种情况下，至少应添加一个新元素
  * count大于0，那么替换掉对应个数的值

## 2、call和apply和bind

### 2.1、练习题
```
"use strict"; // 这个去掉和没去掉有什么区别？
function fn(type, name) {
  console.log(`I am ${name}, belongs to ${type}, what is this? Answer is: `, this);
}

const obj = {
  type: '电器',
  name: '吹风机'
}

fn('电器', '吹风机')
fn.call(obj, '水果', '苹果')
fn.apply(obj, ['水果', '苹果'])
fn.call(null, '水果', '苹果')
fn.call('水果', '水果', '苹果')

const bindFn = fn.bind(obj, '水果')
bindFn('苹果')

// 输出结果应该是：
// I am 吹风机, belongs to 电器, what is this? Answer is:  undefined
// I am 苹果, belongs to 水果, what is this? Answer is:  {type: "电器", name: "吹风机"}
// I am 苹果, belongs to 水果, what is this? Answer is:  {type: "电器", name: "吹风机"}
// I am 苹果, belongs to 水果, what is this? Answer is:  null
// I am 苹果, belongs to 水果, what is this? Answer is:  水果
// I am 苹果, belongs to 水果, what is this? Answer is:  {type: "电器", name: "吹风机"}
```
### 2.2、总结
call和apply是一对孪生兄弟，而bind是这对兄弟的”经纪人“。call和apply说是孪生兄弟一点都不假，因为他们实现的功能一模一样，都是为了被调用的函数指定好执行的上下文(也就是`this`)，唯一的区别是传参给被调用的函数的参数形式，call使用的是以数量取胜大法，有多少个参数就传多少个参数，而apply则换了套路，以简洁取胜，因为人家将所有参数都打包到一个数组里了。

**注意：**
* `call`和`apply`的this值不传的时候，在**严格模式**下是被解释为`undefined`，在**非严格模式**下是全局对象(浏览器环境下是window,nodejs环境下是global)。如果给传的是一些原始值(如：'string'、11之类的值)，那么这些值都会被转变为对象，转变规则是利用对应的构造函数进行`new`，这条规则在待会解释this指向很有用的！！比如：

  ```
  function test(){console.log(this)}
  test.call('test') // 打印结果是： String {"test"}， 等价于 new String("test")
  ```

* `call`特别适用于那些自己本身没有这个方法，但是又想用这个方法去完成一些东西，比如：`Array.prototype.slice.call(arguments)`、再比如`Object.prototype.toString.call([])`，都是利用call方法的特性，将`slice`方法和`toString`内部实现中的`this`指针篡改达到目的的。*

那么说bind是”经纪人“，又是为啥呢？其实从实现的话也就是`apply`的经纪人，因为它利用闭包原理，将apply包裹起来，对外输出一个高阶函数，最简单的实现版本是：

```
Function.prototype.bind = function (thisArgs) {
    var fn = this;
    var restArguments = Array.prototype.slice.call(arguments, 1)
    return function () {
        return fn.apply(thisArgs, restArguments.concat(Array.prototype.slice.call(arguments)));
    };
}
```

三者的一个区别总结如下：(取自[call&apply&bind的学习](https://blog.5udou.cn/blog/callapplybindDe-Xue-Xi-61))

1. call和apply是可以相互替换的，这仅仅是取决于你传递参数使用数组方便还是逗号分隔的参数列表方便。

2. call和apply很容易混淆掉，有时候会忘掉apply是使用数组还是列表，那么有一个简单的记住办法那就是apply的a和array的a是一致的，这样就记住了吧？

3. bind稍微不同，因为它返回的是一个函数，可以在任何你想要执行的时候执行，而前面两个函数都是立马执行的。因此总体来说bind的灵活性会比call和apply更好，适用的场景更多

## 3、this

### 3.1、练习题
```
// 使用"use strict"和不使用的区别？
const PersonA = {
  firstName: 'Lin',
  lastName: 'Xiaowu',
  displayName: function() {
    console.log(`My name is ${this.firstName}-${this.lastName}`)
  }
}
function concat(firstName, lastName, callback) {
  callback(`${firstName}-${lastName}`)
}
const PersonB = function(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
  this.displayName = function() {
    concat(this.firstName, this.lastName, function(fullName) {
      console.log(`My name is ${fullName}, this is equal to window? ${this === window}`)
    })
  }
  this.arrow = function() {
    const arr = () => console.log(this)
    return arr()
  }
}

const pureArrow = () => { return this }

PersonA.displayName();
const personA = PersonA.displayName
personA()

const personB = new PersonB('Lin', 'Xiaowu')
personB.displayName()
personB.arrow()

const personC = { firstName: "dou", lastName: "mi" }
personB.displayName.call(personC)
const personD = personB.arrow.bind(personC)
personD()
const personE = personB.arrow.bind(pureArrow())
personE()

// 打印结果如下：
// My name is Lin-Xiaowu
// My name is undefined-undefined
// My name is Lin-Xiaowu, this is equal to window? true
// PersonB {firstName: "Lin", lastName: "Xiaowu", displayName: ƒ, arrow: ƒ}
// My name is dou-mi, this is equal to window? true
// {firstName: "dou", lastName: "mi"}
// Window {parent: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …}
```

### 3.2、总结
`this`这个小妖精，曾经迷惑了多少人。这里的讲解主要是总结，也不需要生硬地背下来，毕竟是有规律可循的。以下解析借鉴于这篇文章，略有扩展：[this 的值到底是什么？一次说清楚](https://juejin.im/post/5857dad461ff4b00686cf97a)

文中的作者结合`call`，还是把this的一些指向说的蛮清楚的(在此基础上继续扩展)，总结出以下两种情况(涵盖了95%以上的场景)：

1. 所有的函数调用(非箭头函数)都可以归一化到`call`形式的调用，具体转换规则如下：
  * fn(args) => fn.call(undefined, args)
  * obj.fn(args) => obj.fn.call(obj, args)，此处的obj既可以是对象字面量，也可以是使用`new`出来的实例
  * fn.call(thisArgs, args) => 无须转换, this就是thisArgs
  * fn.bind(thisArgs, args) => 无须转换，this就是thisArgs，因为最后还是调用的apply
2. 箭头函数的`this`
  * 箭头函数的`this`保持与其外围的上下文环境的`this`一致。

根据以上规则，我们对上面的练习题进行解析如下：

```

// 根据规则，等价于这么调用：PersonA.displayName.call(PersonA)，所以此时this是等于PersonA,因此打印出：My name is Lin-Xiaowu
PersonA.displayName();
const personA = PersonA.displayName
// 根据规则，等价于这么调用：PersonA.call(undefined)，加上上一节提到的call的this参数原则，因此打印出：My name is undefined-undefined
personA()

const personB = new PersonB('Lin', 'Xiaowu')
// 这种new构造函数的形式也是符合我们上面提到的规则，所以等价于调用personB.displayName.call(personB)，因此打印：My name is Lin-Xiaowu,
// 接着在displayName里面又有一个function，此时使用规则1的第一条，所以这个时候的this是等于window
personB.displayName()
// 这个是同时使用两条规则进行判断，先使用规则1的第二条，等价于调用：personB.arrow.call(personB)，
// 于是arrow内部的this指向了personB，然后再用规则2的第一条，箭头函数的this随上下文，因此打印的this便是personB
personB.arrow()

const personC = { firstName: "dou", lastName: "mi" }
// 根据规则，并结合上面的一些分析，很容易得出答案：My name is dou-mi, this is equal to window? true
personB.displayName.call(personC)
const personD = personB.arrow.bind(personC)
// 根据规则，this指向了personC，再结合上面的分析，得出的打印结果是personC：{firstName: "dou", lastName: "mi"}
personD()
const personE = personB.arrow.bind(pureArrow())
// 这个搞懂pureArrow的this指针即可得到答案，因为符合规则1的第一条，所以this指向了window，于是打印出了window对象
personE()
```

这么讲解下来，this的指向懂了吗？

## 4、`__proto__`和`prototype`
### 4.1、练习题
```
function Fn() {
    this.x = 100;
    this.y = 200;
    this.getX = function () {
        console.log(this.x);
    }
}
Fn.prototype = {
    y: 400,
    getX: function () {
        console.log(this.x);
    },
    getY: function () {
        console.log(this.y);
    },
    sum: function () {
        console.log(this.x + this.y);
    }
};
Fn.prototype.getX = function () {
    console.log(this.x);
};
Fn.prototype.getY = function () {
    console.log(this.y);
};
var f1 = new Fn;
var f2 = new Fn;
console.log(f1.getX === f2.getX);
console.log(f1.getY === f2.getY);
console.log(f1.__proto__.getY === Fn.prototype.getY);
console.log(f1.__proto__.getX === f2.getX);
console.log(f1.getX === Fn.prototype.getX);
console.log(f1.constructor);
console.log(Fn.prototype.__proto__.constructor);
f1.getX();
f1.__proto__.getX();
f2.getY();
Fn.prototype.getY();
f1.sum();
Fn.prototype.sum();

// 打印结果如下：
false
// true
// true
// false
// false
// ƒ Object() { [native code] }
// ƒ Object() { [native code] }
// 100
// undefined
// 200
// 400
// 300
// NaN
```
### 4.2、总结
这两个概念牵扯到了原型和原型链，二者的区别以及需要注意的东西都体现在了下图，希望大家对原型链有这么一张图的印象(**也就是每当出现这种题目的话，脑子能够浮现对应的关系图**)，再结合下面总结的5条规律，深化印象，从而真正掌握住：

![](https://user-gold-cdn.xitu.io/2019/10/24/16dfce09099b53a8?w=1217&h=819&f=png&s=436226)

特此总结的一些规律贴在这里(下面提到的每一条规律都用特定的颜色在上图中标注一一对应)：

1、只要是构造函数(构造函数可以是原生的也可以是自定义的，看上图就知道了)都会有`prototype`属性，并且都是指向其原型对象

2、构造函数实例化后的实例都有`__proto__`属性，并指向其构造函数的原型对象

3、构造函数都有`__proto__`属性，统一指向了原生Function的原型对象

4、原型对象都有会一个`constructor`的属性，并且都是指向其构造函数

5、原型对象都有会一个`__proto__`的属性，并且都是指向Object的原型对象

6、**有一种很特殊的情况，那就是给原型对象重新赋值的时候，需要特别考虑，这一点没有在上图中体现，但是在练习题里体现了(Fn.prototype = {})**

## 5、typeof和instanceof
### 5.1、练习题
```
typeof Math.LN2 === 'number';
typeof Infinity === 'number';
typeof NaN === 'number';

typeof 42n === 'bigint';

typeof undefined === 'undefined';
typeof class C {} === 'function';


function C(){} // defining a constructor
function D(){} // defining another constructor

var o = new C();
o instanceof C; // true, because: Object.getPrototypeOf(o) === C.prototype
o instanceof D; // false, because D.prototype is nowhere in o's prototype chain
o instanceof Object; // true, 原型链查找
C.prototype instanceof Object // true

C.prototype = {};
var o2 = new C();
o2 instanceof C; // true
o instanceof C; // false, 参考前面的原型链一节

D.prototype = new C(); // use inheritance
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true
```

### 5.2、总结
#### 5.2.1、typeof
typeof用于基本数据类型的类型判断，返回值都为小写的字符串。如果是对象，除了function类型会返回“function”, 其他对象统一返回“object”。因此这也是typeof使用的一个缺陷，无法正确地告知具体的object。

typeof返回的结果整理如下：

| 类型                                                                           | 结果        | 备注                                                                                         |
|--------------------------------------------------------------------------------|-------------|----------------------------------------------------------------------------------------------|
| Undefined                                                                      | "undefined" |                                                                                              |
| Null                                                                           | "object"    | 历史原因导致的结果                                                                           |
| Boolean                                                                        | "boolean"   |                                                                                              |
| Number                                                                         | "number"    |                                                                                              |
| BigInt                                                                         | "bigint"    |                                                                                              |
| String                                                                         | "string"    |                                                                                              |
| Symbol                                                                         | "symbol"    |                                                                                              |
| Host object（宿主对象，概念参考[宿主对象定义](http://es5.github.com/#x4.3.8)） | 取决于实现  | 由编译器各自实现的字符串，但不是"undefined","number","boolean","number","string"。 |
| Function object                                                                | "function"  | 诸如 function a() {} 之类的                                                                  |
| Any other object                                                               | "object"    |                                                                                              |

因此基于`typeof`的使用，我们建议在用 typeof 来判断变量类型的时候，我们需要注意，最好是用 typeof 来判断基本数据类型（包括symbol），避免对 null 的判断。

#### 5.2.2、instanceof
`instanceof`是二元操作符，用来判断变量是否为某个对象的实例，返回值为`true`或`false`。操作符左边为对象，右边为构造函数。

`instanceof`主要的实现原理就是只要右边变量的`prototype`在左边变量的原型链上即可。因此，`instanceof`在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 prototype，如果查找失败，则会返回`false`，告诉我们左边变量并非是右边变量的实例。

实现的简约代码如下：

```
function new_instance_of(leftVaule, rightVaule) {
    let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值
    leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值
    while (true) {
    	if (leftVaule === null) {
          return false;
      }
      if (leftVaule === rightProto) {
          return true;
      }
      leftVaule = leftVaule.__proto__
    }
}
```

## 6、宏任务和微任务
### 6.1、练习题(nodejs环境, 版本node11以上)
```
const EventEmitter = require('events')
class EE extends EventEmitter {}
const yy = new EE()
console.log('测试开始')
yy.on('event', () => console.log('我是EventEmitter触发的事件回调'))
setTimeout(() => {
  console.log('0 毫秒后到期的定时器回调1')
  process.nextTick(() => console.log('我是0毫秒定时器1加塞的一个微任务'))
}, 0)
setTimeout(() => {
  console.log('0 毫秒后到期的定时器回调2')
  process.nextTick(() => console.log('我是0毫秒定时器2加塞的一个微任务'))
}, 0)
setImmediate(() => console.log('immediate 立即回调'))
process.nextTick(() => console.log('process.nextTick 的第一次回调'))
new Promise((resolve) => {
  console.log('我是promise')
}).then(() => {
  yy.emit('event')
  process.nextTick(() => console.log('process.nextTick 的第二次回调'))
  console.log('promise 第一次回调')
})
.then(() => console.log('promise 第二次回调'))
console.log('测试结束?')

/* 打印结果如下：
  测试开始
  我是promise
  测试结束?
  process.nextTick 的第一次回调
  0 毫秒后到期的定时器回调1
  我是0毫秒定时器1加塞的一个微任务
  0 毫秒后到期的定时器回调2
  我是0毫秒定时器2加塞的一个微任务
  immediate 立即回调
*/
```
### 6.2、总结
js把异步任务队列分为两种：宏任务(macro task)和微任务(micro task)，二者的区别是执行时机的不同。

异步队列是怎么执行这二者任务的？请看下图

![](https://user-gold-cdn.xitu.io/2019/10/24/16dfce090a1ba940?w=981&h=550&f=png&s=172697)

上图给的信息有以下几点：

* **先执行微任务的队列，再检查宏任务的队列**
* **在当前的微任务没有执行完成时，是不会执行下一个宏任务的。**
* **每次执行完一个宏任务之后，要检查微任务队列是否又有任务需要执行了(这个体现在上面的练习题中的超时后加塞的微任务队列)**

那么知道了执行的机制之后，剩下的一个问题就是任务类型的划分，整理如下一表，结合上面的问题，相信你心中有了答案了~

| 事件                       | 宏任务/微任务 | 浏览器 | nodejs |
|----------------------------|---------------|--------|--------|
| I/O                        | 宏任务        | ✅      | ✅      |
| setTimeout                 | 宏任务        | ✅      | ✅      |
| setInterval                | 宏任务        | ✅      | ✅      |
| setImmediate               | 宏任务        | ❌      | ✅      |
| requestAnimationFrame      | 宏任务        | ✅      | ❌      |
| process.nextTick           | 微任务        | ✅      | ✅      |
| MutationObserver           | 微任务        | ✅      | ❌      |
| Promise.then catch finally | 微任务        | ✅      | ✅      |
| EventEmitter               | 微任务        | ❌      | ✅      |

*Tips*

1. async函数在await之前的代码都是同步执行的，可以理解为await之前的代码属于new Promise时传入的代码，await之后的所有代码都是在Promise.then中的回调
2. **node11版本之前的打印和这里的不大一样，原因可以看这里的[MacroTask and MicroTask execution order](https://github.com/nodejs/node/issues/22257)**

## 7、Map和Weak Map
### 7.1、练习题
```
const weakMap = new WeakMap()
let weakKey = {}
weakMap.set(weakKey, 'weakValue')
console.log(weakMap.get(weakKey))
weakKey = null


const map = new Map()
let key = {}
map.set(key, 'value')
console.log(map.get(key))
key = null
// chrome浏览器的Memory一栏中点击一下“Collect garbage”后回来打印结果
console.log(weakMap, map)
```
### 7.2、总结
WeakMap结构与Map结构基本类似，唯一的区别是**它只接受对象作为键名（null除外），不接受其他类型的值作为键名**，而且键名所指向的对象，不计入垃圾回收机制。

WeakMap的设计目的在于，键名是对象的弱引用（垃圾回收机制不将该引用考虑在内），**所以其所对应的对象可能会被自动回收。当对象被回收后，WeakMap自动移除对应的键值对**。
典型应用是，一个对应DOM元素的WeakMap结构，当某个DOM元素被清除，其所对应的WeakMap记录就会自动被移除。基本上，WeakMap的专用场合就是，它的键所对应的对象，可能会在将来消失。WeakMap结构有助于防止内存泄漏。

WeakMap与Map在API上的区别主要是两个，

1. 没有遍历操作（即没有key()、values()和entries()方法），也没有size属性；
2. 无法清空，即不支持clear方法。这与WeakMap的键不被计入引用、被垃圾回收机制忽略有关。

**因此WeakMap只有四个方法可用：get()、set()、has()、delete()**。

而Map有9个方法可用：`get()`、`set()`、`has()`、`delete()`、`clear()`、`keys()`、`values()`、`entries()`、`forEach()`
## 7、express和koa
### 7.1、练习题
```
const express = require('express')

const app = express()

const sleep = (mseconds) => new Promise((resolve) => setTimeout(() => {
  console.log('sleep timeout...')
  resolve()
}, mseconds))

app.use(async (req, res, next) => {
  console.log('I am the first middleware')
  const startTime = Date.now()
  console.log(`================ start ${req.method} ${req.url}`, { query: req.query, body: req.body });
  next()
  const cost = Date.now() - startTime
  console.log(`================ end ${req.method} ${req.url} ${res.statusCode} - ${cost} ms`)
})
app.use((req, res, next) => {
  console.log('I am the second middleware')
  next()
  console.log('second middleware end calling')
})

app.get('/api/test1', async(req, res, next) => {
  console.log('I am the router middleware => /api/test1')
  await sleep(2000)
  res.status(200).send('hello')
})

app.listen(3000)
console.log('server listening at port 3000')

// 在Shell终端中请求： `curl 127.0.0.1:3000/api/test1`

// 打印结果为：
I am the first middleware
================ start GET /api/test1
I am the second middleware
I am the router middleware => /api/test1
second middleware end calling
================ end GET /api/test1 200 - 3 ms
sleep timeout...
```
### 7.2、总结
express框架和koa框架的中间件实现形式不一样，前者使用回调的形式，后者采用async/await模式。回调形式在处理异步的中间件的时候没能够很好处理执行流程，导致需要一些别的workaround。

更多区别参考：[再也不怕面试官问你express和koa的区别了](https://blog.5udou.cn/blog/Zai-Ye-Bu-Pa-Mian-Shi-Guan-Wen-Ni-expressHe-koaDe-Qu-Bie-Liao-98)

## 8、防抖和节流
### 8.1、练习题
```
 const _now = Date.now || function () {
  return new Date().getTime();
}

 const throttle = function (func, wait, options = {}) {
  let context, args, result;
  let timeout = null;
  let previous = 0;

  const later = function () {
    previous = options.leading === false ? 0 : _now();
    timeout = null;
    result = func.apply(context, args);

    if (!timeout) context = args = null;
  };

  return function () {
    // 记录当前时间戳
    const now = _now();

    if (!previous && options.leading === false) previous = now;

    const remaining = wait - (now - previous);
    context = this;
    args = arguments;

    if (remaining <= 0 || remaining > wait) {
      if (timeout) {
        clearTimeout(timeout);
        // 解除引用，防止内存泄露
        timeout = null;
      }
      previous = now;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    } else if (!timeout && options.trailing !== false) { // 最后一次需要触发的情况
      timeout = setTimeout(later, remaining);
    }
    // 回调返回值
    return result;
  };
}

// 函数去抖（连续事件触发结束后只触发一次）
// sample 1: debounce(function(){}, 1000)
// 连续事件结束后的 1000ms 后触发
// sample 1: debounce(function(){}, 1000, true)
// 连续事件触发后立即触发（此时会忽略第二个参数）
/* eslint-disable */
const debounce = function (func, wait, immediate) {
  let timeout, args, context, timestamp, result;

  const later = function () {
    const last = _now() - timestamp;

    if (last < wait && last >= 0) {
      timeout = setTimeout(later, wait - last);
    } else {
      timeout = null;
      if (!immediate) {
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      }
    }
  };

  return function () {
    context = this;
    args = arguments;
    timestamp = _now();
    const callNow = immediate && !timeout;
    if (!timeout) {
      timeout = setTimeout(later, wait);
    }
    if (callNow) {
      result = func.apply(context, args);
      context = args = null;
    }

    return result;
  };
};

document.querySelector('.throttle').addEventListener('click', throttle(function(){
	console.log('click event trigger')
}, 1000))

document.querySelector('.debounce').addEventListener('click', debounce(function(){
	console.log('click event trigger')
}, 500))
```

### 8.2、总结
二者顾名思义，防抖(debounce)的含义便是为了防止抖动造成的结果不准确，我们在抖动的过程中不去关注其中的变化，而是等到稳定的时候再处理结果。这种概念在硬件上一些电流或者电磁波图有着很多的应用。在电流中一般会有毛刺，而我们在计算结果的时候是不会去考虑这段异常的抖动，而是从整体上来评测结果，而在软件上来实现防抖，便是在抖动的过程中不去执行对应的动作，而是等到抖动结束趋于稳定的时候再来执行动作。

而节流(throttle)则是可以形象地描述为人为地设置一个闸口，让某一段时间内发生的时间的频率降低下来，这个频率可以由你决定。想象一下你在一条流动的小溪中设置了一个关卡，本来一小时流量有10立方米，但是因为你的节流导致流量变成了5立方米，这样我们就称为节流。

因此，

* 防抖是无论事件触发多少次都忽略，直到最后一次才调用回调函数。
* 节流是无论事件触发多少次，回调函数都是按照配置的触发间隔调用。

## 参考
1. [slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
2. [splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)
3. [new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
4. [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)
5. [this 的值到底是什么？一次说清楚](https://juejin.im/post/5857dad461ff4b00686cf97a)
6. [call&apply&bind的学习](https://blog.5udou.cn/blog/callapplybindDe-Xue-Xi-61)
7. [JavaScript的原型和原型链的前世今生(一)](https://blog.5udou.cn/blog/JavaScriptDe-Yuan-Xing-He-Yuan-Xing-Lian-De-Qian-Shi-Jin-Sheng-Yi-)
8. [防抖和节流的代码分析](https://blog.5udou.cn/blog/Fang-Dou-He-Jie-Liu-De-Dai-Ma-Fen-Xi-59)
9. [再也不怕面试官问你express和koa的区别了](https://blog.5udou.cn/blog/Zai-Ye-Bu-Pa-Mian-Shi-Guan-Wen-Ni-expressHe-koaDe-Qu-Bie-Liao-98)
10. [微任务、宏任务与Event-Loop](https://juejin.im/post/5b73d7a6518825610072b42b)
  
