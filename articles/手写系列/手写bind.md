# 手写bind

> 源代码以及测试用例可访问 [手写bind源代码链接](https://github.com/vinoMamba/bind) 。 文章中代码只截取源代码关键部分。

## 前置知识点

1. 了解 bind 的基本使用
2. 能判断 this
3. 了解 call 的用法 和 apply 的用法
3. 了解 polyfill
4. 原型链
5. 知道 new 做了些啥

#### MDN上面关于bind的定义

> `bind()`方法会创建一个新函数。当这个新函数被调用时，bind()的第一个参数将作为它运行时的`this`，而剩下的参数将作为新函数的参数，供调用时使用。

## 第一版

从定义可以看出`bind`函数将返回一个原函数的拷贝，并且拥有指定的`this`和初始参数。从定义出发开始写第一版。为防止和js原生的`bind`的冲突，这里我取名为`bind2`

```javascript
/** src/index.js
 *  初始代码
 ***/
function bind() {

    return function bf() {

    }
}

module.exports = bind
```

##### 测试用例一：bind函数可以绑定this

```javascript
/**  test/index.js
 *   测试用例1:bind函数可以绑定this
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function () {
    return this
}
const newFn = fn.bind2({name: 'vino'})
console.assert(newFn().name === 'vino')
```

##### 源代码

```javascript
/** src/index.js
 *  bind函数可以绑定this
 */

function bind(asThis) {
    // 这里的this就是指向调用bind的fn 
    const fn = this
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf() {
        //call 绑定第一个参数
        return fn.call(asThis)
    }
}

module.exports = bind
```

##### 测试用例二：bind函数可以绑定this,并且可以传递参数

```javascript
/**  test/index.js
 *   测试用例2:bind函数可以绑定this,并且可以传递参数
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function (p1, p2) {
    return [this, p1, p2]
}
const newFn = fn.bind2({name: 'vino'}, 8, 24)
console.assert(newFn()[0].name === 'vino')
console.assert(newFn()[1] === 8)
console.assert(newFn()[2] === 24)
```

##### 源代码

```javascript
/** src/index.js
 *  bind函数可以绑定this,并且可以传递参数
 */

function bind(asThis, ...args) {
    const fn = this
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf() {
        //将传入的其他参数添加到fn上面
        return fn.call(asThis, ...args)
    }
}

module.exports = bind
```

##### 测试用例三：bind函数可以绑定this,在调用时再传递参数

```javascript
/**  test/index.js
 *   测试用例2:bind函数可以绑定this,在调用时再传递参数
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function (p1, p2) {
    return [this, p1, p2]
}
const newFn = fn.bind2({name: 'vino'})
console.assert(newFn(8, 24)[0].name === 'vino')
console.assert(newFn(8, 24)[1] === 8)
console.assert(newFn(8, 24)[2] === 24)
```

##### 源代码

```javascript
/** src/index.js
 *  bind函数可以绑定this,并且可以传递参数
 */

function bind(asThis, ...args) {
    const fn = this
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf(...args2) {
        //这里bf函数接收调用时的参数，并添加到fn上
        return fn.call(asThis, ...args, ...args2)
    }
}

module.exports = bind
```

##### 测试用例四：bind函数可以绑定this,可先传递一个参数,在调用时再传递另一个参数

```javascript
/**  test/index.js
 *   测试用例2:bind函数可以绑定this,在调用时再传递参数
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function (p1, p2) {
    return [this, p1, p2]
}
const newFn = fn.bind2({name: 'vino'}, 8)
console.assert(newFn(24)[0].name === 'vino')
console.assert(newFn(24)[1] === 8)
console.assert(newFn(24)[2] === 24)
```

##### 源代码

```javascript
/** src/index.js
 *  bind函数可以绑定this,并且可以传递参数
 */

function bind(asThis, ...args) {
    const fn = this
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf(...args2) {
        //这里bf函数接收调用时的参数，并添加到fn上
        return fn.call(asThis, ...args, ...args2)
    }
}

module.exports = bind
```

至此，表面上看已经实现了bind函数。但实际上目前bind的还不支持`new`操作，并且此版本严格来讲在实际使用场景中存在问题。 很容易看出，上述代码都使用了比较新的ES语法，如`const`,`...`
等，如果bind在实际情况中不能使用，那么这些新语法同样不可以使用。 为了兼容更低的版本，不得不将代码中使用的新语法替换掉。

## polyfill

```javascript
/** src/index.js
 *  polyfill
 */
var slice = Array.prototype.slice

function bind(asThis) {
    var fn = this
    //arguements 是伪数组
    var args = slice.call(arguments, 1)
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf() {
        var args2 = slice.call(arguments, 0)
        //apply 代替 call,apply 第二个参数可以使用数组
        return fn.apply(asThis, args.concat(args2))
    }
}

module.exports = bind

if (!Function.prototype.bind) {
    Function.prototype.bind = bind
}
```

## 支持new

> 当一个绑定函数是用来构建一个值的，原来提供的 this 就会被忽略。不过提供的参数列表仍然会插入到构造函数调用时的参数列表之前

上面的描述取自[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind),
换句话说就是：通过`bind`绑定的函数也能通过`new`关键字来创建对象，值得注意的是**此时`bind`提供的`this`会被忽略。其他参数可正常提供给函数**。

#### 使用new关键字创建对象时做了哪些操作

在实现支持new之前，我们先来探讨下`new`关键字具体做了那些事

> 以new方式调用构造函数会执行如下操作。
>
>   1. 在内存中创建一个新对象。
>   2. 这个新对象内部的[[Prototype]]特性被赋值为构造函数的 prototype 属性。
>   3. 构造函数内部的 this 被赋值为这个新对象（即 this 指向新对象）。
>   4. 执行构造函数内部的代码（给新对象添加属性）。
>   5. 如果构造函数返回非空对象，则返回该对象；否则，返回刚创建的新对象。

以上解释来自于【JavaScript高级程序设计】这本书,下面使用代码来进一步说明

```javascript
function Person(name, age) {
    this.name = name
    this.age = age
}

const obj = new Person("vino", 24)
// 1. temp = {} 创建临时对象
// 2. temp.__proto__ = People.prototype 临时对象的 __proto__ 指向 Person的 prototype
// 3. this = temp  把this指向这个临时变量temp 
// 4. 执行Person函数的代码，vino赋值给name,24赋值给age  
// 5. return temp  new最后把temp 返回出去
```

##### 测试用例五：bind函数支持new操作

```javascript
/**  test/index.js
 *   测试用例5:bind函数支持new操作
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function (p1, p2) {
    this.p1 = p1
    this.p2 = p2
}
const newFn = fn.bind2(undefined, 8, 24)
const obj = new newFn()
console.assert(obj.p1 === 8)
console.assert(obj.p2 === 24)
```

在写代码之前，要搞清楚一个问题：如何判断一个函数是普通调用还是使用`new`关键字调用。

根据上述对`new`操作的描述可以得到： 通过`new`得到的对象，该对象的[Prototype]特性指向了构造函数的prototype属性。换句话说：
`new`得到的对象是构造函数的一个实例，即` obj instanceof People`的结果是true

##### 源代码

```javascript
/** src/index.js
 * bind函数支持new
 */
var slice = Array.prototype.slice

function bind(asThis) {
    var fn = this
    var args = slice.call(arguments, 1)
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }
    return function bf() {
        var args2 = slice.call(arguments, 0)
        // 这里的this如果是使用new关键字调用，指向的就是生成的对象。
        return fn.apply(this instanceof fn ? this : asThis, args.concat(args2))
    }
}

module.exports = bind

if (!Function.prototype.bind) {
    Function.prototype.bind = bind
}
```

##### 测试用例六：bind支持new操作,并且可以调用原型上的方法

```javascript
/**  test/index.js
 *   测试用例6:bind支持new操作,并且可以调用原型上的方法
 */
const bind = require("../src/index.js")
Function.prototype.bind2 = bind

const fn = function (p1, p2) {
    this.p1 = p1
    this.p2 = p2
}
fn.prototype.doSomething = function () { /*do something*/
}
const newFn = fn.bind2(undefined, 8, 24)
const obj = new newFn()
console.assert(obj.p1 === 8)
console.assert(obj.p2 === 24)
//期待obj可以使用doSomething方法
console.assert(typeof obj.doSomething === 'function')
```

在new的操作中,得到的对象能够使用构造函数原型链上的方法，但是我们目前写的代码还不支持。

原因很简单,通过`new`关键字调用之后得到对象`obj`。`obj` 现在是函数`bf`的一个实例,所以指向的是`bf.protptype`。我们要的结果是他指向`newFn.prototype`
。所以只用改变`bf.prototype = fn.prototype`即可

##### 源代码

```javascript
/** src/index.js
 * bind支持new操作,并且可以调用原型上的方法
 */
var slice = Array.prototype.slice

function bind(asThis) {
    var fn = this
    var args = slice.call(arguments, 1)
    if (typeof fn !== 'function') {
        throw new Error('fn不是一个函数')
    }

    function bf() {
        var args2 = slice.call(arguments, 0)
        // 这里的this如果是使用new关键字调用，指向的就是生成的对象。
        return fn.apply(this instanceof fn ? this : asThis, args.concat(args2))
    }

    //改变bf.prototype 为 fn.prototype(fn 就是调用bind的那个函数)
    bf.prototype = fn.prototype
    return bf
}

module.exports = bind

if (!Function.prototype.bind) {
    Function.prototype.bind = bind
}
```

到这里，手写bind的代码基本上已经完成了。如果要得到更准确的结果。那就继续写更多的测试用例并通过即可。












