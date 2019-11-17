*  [better-scroll](https://github.com/ustbhuangyi/better-scroll)无法滚动的原因大致有两点： scrollHeight <= wrapperHeight,  wrapper内部的数据发生改变但是代码没有调用refresh函数

* [react](https://github.com/facebook/react)使用数组的index作为key是一种反模式，所以强烈建议不要使用index作为key值，如下例子：

    **http://jsbin.com/wohima/edit?js,output**

* [pm2](https://github.com/Unitech/pm2)的配置文件中使用下面这个属性:

    ```
    "interpreter": "/home/appdev/.nvm/versions/node/v8.9.4/bin/node",
    ```
    必须与`"exec_mode": "fork"`搭配使用，如果使用`cluster`的话，这个解释器的配置是不生效的。

    参考：  https://stackoverflow.com/questions/34682035/cluster-and-fork-mode-difference-in-pm2

* 在webpack配置[uglifJs](https://github.com/mishoo/UglifyJS2)后打包报下面这种错误的时候：

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

* 在[Mobx](https://github.com/mobxjs/mobx)中，下面这行代码可以轻松的大致理解mobx的机制：

    ```
    const obj = observable({
        a: 1,
        b: 2
    })

    autoRun(() => {
        console.log(obj.a)
    })

    obj.b = 3 // 什么都没有发生
    obj.a = 2 // observe 函数的回调触发了，控制台输出：2
    ```

    参考：https://zhuanlan.zhihu.com/p/25585910?refer=purerender

* 要是有人问你react的事件和原生的addEventListner有啥区别?怎么回答好？

    在React中，如果像在`onClick`中所做的那样在JSX中指定事件时，是不能直接处理常规的`DOM`事件的，而是处理React特定的事件类型`SyntheticEvent`。你的事件处理器不能得到原生的事件参数类型`MouseEvent`、`KeyboardEvent`等等，而是总是得到封装了浏览器的原生事件的事件参数类型`SyntheticEvent`。

    这样的做法有两个好处：1、浏览器兼容性，2、性能提升

    通过将所有本地事件封装为一个`SyntheticEvent`类型的对象，React让我们从处理最终不得不处理的事件处理怪癖中解脱出来。

    在复杂的UI中，事件处理器越多，应用程序占用的内存就越多。虽然手动处理这并不难，但是这有点枯燥，因为你得设法将事件组织到一个共同的父之下。有时，这是不可能的。有时，麻烦超过了好处。而 React 就做的很聪明。React从不会将事件处理器直接绑定到 DOM 元素。它在文档的根部使用一个事件处理器，来负责监听所有事件，并按需调用合适的事件处理器

    `SyntheticEvent`是一个跨浏览器封装的浏览器原生事件。它和浏览器原生事件有相同的接口，包括`stopPropagation`和`preventDefault`。如果你因为某些原因需要底层浏览器事件，可以简单地使用nativeEvent属性去获取。

    React的合成事件名字是驼峰型的，另外一个区别是react事件无法使用`return false`去阻止默认行为。而是需要明确地调用`preventDefault`。
