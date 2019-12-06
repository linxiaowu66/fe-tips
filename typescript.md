* 下面这种自定义错误的写法会报两种错误：

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

* typescript使用require报错：
`Cannot find name 'require'`
解决办法是：
将`declare var require: (filename: string) => any `写到`index.d.ts`

* 在async函数中也是没有`arguments`参数：
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

* 一些外部第三方包没有类型定义，于是你引用的时候就会报错，这时你需要这么配置才可以消除这个报错：

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
