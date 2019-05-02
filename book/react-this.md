## React 为啥要绑定this
> onClick={this.fn}.bind(this)

在react中我们总是看到事件后面跟着一个`.bind(this)`,也就是绑定this，如果你尝试不写绑定，那react就会报错`Cannot read property 'setState' of undefined`，意思是说`this`是`undefined`。

在官方文档上是这样解释的：
> 这并不是 React 的特殊行为；它是函数如何在 JavaScript 中运行的一部分。通常情况下，如果你没有在方法后面添加 () ，例如 onClick={this.handleClick}，你应该为这个方法绑定 this。   

意思就是说这个现象跟react没关系，是js本身就是这样的特性。

既然官方这么说了,那我们看下js的this：

### js中的this
```
let a = '全局变量'
const obj = {
	a:'局部变量',
	fn:function(){
		console.log("现在得到的变量'a'是:"+this.a)
	}
}
obj.fn()  //现在得到的变量'a'是:局部变量

const fn2 = obj.fn
fn2()  现在得到的变量'a'是:undefined
```

上面这段代码其实是这样：
```
let a = '全局变量'
const obj = {
	a:'局部变量',
	fn:function(){
		console.log("现在得到的变量'a'是:"+this.a)
	}
}
obj.fn().call(obj)  //现在得到的变量'a'是:局部变量

const fn2 = obj.fn
fn2().call(window)  现在得到的变量'a'是:undefined
```

我们知道js的`this`不是在函数里面定义的，是要看调用者是谁，`obj.fn()`在调用时，`obj`是调用者。而`fn2()`的调用者是谁？不知道是谁那就是`window`。

当然你也可以使用`call`给它指派this。
```
let a = '全局变量'
const obj = {
	a:'局部变量',
	fn:function(){
		console.log("现在得到的变量'a'是:"+this.a)
	}
}
obj.fn()  //现在得到的变量'a'是:局部变量

const fn2 = obj.fn
fn2()  //现在得到的变量'a'是:undefined
fn2().call(obj)  // 现在得到的变量'a'是:局部变量
```

至于为什么是`undefined`，因为js在严格模式下this不会自动绑定到全局对象(window)上。非严格模式下this就是window。

### React
回到react。当我们写一个事件时`onClick={this.fn}`,事实上`fn`函数是作为一个回调函数传给了`addEventListener()`。

基本就是下面这个意思：
```
//还是刚才的代码 只是变成了回调
let a = '全局变量'
const obj = {
	a:'局部变量',
	fn:function(){
		console.log("现在得到的变量'a'是:"+this.a)
	}
}
obj.fn()  //现在得到的变量'a'是:局部变量

function fn2(cb){ //回调
    cb()
}

fn2(obj.fn)  //现在得到的变量'a'是:undefined
fn2(obj.fn.bind(obj)) //现在得到的变量'a'是:局部变量

```

因为上面说了，如果函数找不到调用者是谁，那他就是`undefined/window`,`cb()`在回调的时候并没有调用者,所以我们需要使用`bind`来为它手动绑定一个。


