# 前端知识体系
## 1 JS
### 1.1 JS基础知识
#### 执行上下文|作用域|闭包
> 执行上下文：当代码运行时，会对应一个执行环境，在此环境或者范围内所有变量会被提出来（变量提前），直接赋值或者undefined，代码开始从上至下执行，这叫做执行上线文。

```sh
  JS有三种运行环境：
  1.全局环境
  2.函数环境
  3.eval函数（就是将字符串转换为Js执行）
```
> 执行上下文作用域链：假设一个函数a，在A声明一个参数X，然后执行B函数，函数B在声明一个参数Z，那么此时的作用域链就是a->b

> 闭包简单地说就是函数返回函数，返回的函数调用了父函数的参数或内部变量，如果该函数返回函数被执行了就产生了闭包。

> 作用域：函数作用域，块级作用域，作用域转换，call,apply,bind


####  call,apply,bind

> JS代码中的this：
- 首先JS的执行是按照执行栈顺序来的先进先出，没执行一个栈就会创建一个上下文
- 在ES5定义this的值为执行上下文中的ThisBinding，ThisBinding在执行时被设置为某个对象的引用
- this 分为全局This，eval（）This， eval有自己的上下文，函数中的this

> call的实现方法:特点就是入参为枚举参数，如：a.call(window, 1,2,3,4)
```sh
  const slice = Array.prototype.slice
  Function.prototype.mcall = function(ctx) {
    if (typeof this !== 'function') {
      throw new TypeError('not function')
    }
    ctx = ctx || window
    ctx.fn = this
    var args = slice.call(arguments, 1)
    var f = ctx.fn(...args)
    delete ctx.fn
    return f
  }
```

> apply的实现方法：特点传入数组参数，如：a.apply(this, [1,2,3])

```sh
  Function.prototype.mpply = function(ctx) {
    if (typeof this !== 'function') {
      throw new TypeError('not function')
    }
    ctx = ctx || window
    ctx.fn = this
    let f = null
    # 判断有没有入参，有的话传入没有直接执行
    if (arguments[1]) {
      f = ctx.fn(...arguments[1])
    } else {
      f = ctx.fn()
    }
    delete ctx.fn
    return f
  }
```
> bind的实现方法：特点是返回一个可执行的方法，传参为枚举
```sh
  Function.prototype.mbind = function(ctx) {
    if (typeof this !== 'function') {
      throw new TypeError('not function')
    }
    let _this = this
    let args = [...arguments].slice(1)
    # 返回一个函数
    return function F() {
      # 因为返回一个函数，可以new F()，所以需要判断
      if (this instanceof F) {
        return new _this(...args, ...arguments)
      }
      return _this.apply(ctx, args.concat(...arguments))
    }
  }
```

### 基础数据类型
> 基础数据类型有六种：Symbol, Boolean, Number, String, null, undefined</br>
> 复杂类型为： Object

#### 数据类型判断

1. typeof 
> typeof无法区分引用数据类型和null，所谓引用类型就是array，new Date,new Set, new map,这些会判断为Object，[]与{}会被判断类型相似都为object

2. instanceof
> intanceof用来判断函数的prototype属性是否在某个实例的原型链上的，语法：object（实例对象） instanceof constructor（构造函数），返回true

3. Object.prototype.toString.call()
> 此方法很有效，会返回真实的数据类型，可以封装成方法如下
```sh

  function getType(attr) {
    if (attr === null) return String(attr)
    return typeof attr === 'object' ? Object.prototype.toString.call(attr).replace('[object', '').replace(']', '') : typeof obj
  }

```

4. contructor
> 每一个对象实例都可以通过 constrcutor 对象来访问它的构造函数 。JS 中内置了一些构造函数：Object、Array、Function、Date、RegExp、String等。我们可以通过数据的 constrcutor 是否与其构造函数相等来判断数据的类型。但是有一个弊端，constructor的只想可以修改，例如：
```sh
  function Name(name) {
    this.name = name;
  }
  
  function Stuent(age) {
    this.age = age;
  }
  # 将构造函数Name的实例赋给Student的原型，Student的原型的构造函数会发生改变，将不再指向自身。
  Stuent.prototype = new Name('张三');
  Stuent.prototype.constructor === Name; // true
  Stuent.prototype.constructor === Stuent; // false
```
5. Array.isArray
> 只能判断是不是数组

#### 数据类型转换
> 类型转换分为显式和隐式<br/>
> 原理：但是不管是隐式转换还是显式转换,都会遵循一定的原理,由于JavaScript是一门动态类型的语言,可以随时赋予任意值,但是各种运算符或条件判断中是需要特定类型的,因此JavaScript引擎会在运算时为变量设定类型.  

![](./转换.png)


### 原型链以及继承
