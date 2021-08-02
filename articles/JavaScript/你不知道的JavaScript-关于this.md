# 你不知道的JavaScript（上）- 关于this

## 为什么要用this

```javascript
function identify() {
    return this.name.toUpperCase()
}

function speak() {
    const greeting = `hello,i am ${identify.call(this)}`
    console.log(greeting)
}

const me = {
    name: 'vino'
}
const you = {
    name: 'reader'
}
identify.call(me)
identify.call(you)
speak.call(me)
speak.call(you)
```

> 上述代码如果不使用`this`，就要显示的传入一个上下文对象
>
> 然而，`this`提供了一种更优雅的方式来**隐式**“传递”一个对象引用，因此可以将API设计得更加简洁 并且易于复用

### 对于this的误解

#### 1. 指向自身

```javascript
function foo(num) {
    console.log(`foo:${num}`)
    this.count++
}

foo.count = 0

for (let i = 0; i < 10; i++) {
    foo(i)
}
console.log(foo.count) //0
```

> 实际结果是`foo.count`的值仍然是0，这就能看出this指向自身是错误的理解

为了得到正确的结果，可以将代码改成如下形式，用`foo`代替掉`this`

```javascript
function foo(num) {
    console.log(`foo:${num}`)
    foo.count++
}

foo.count = 0

for (let i = 0; i < 10; i++) {
    foo(i)
}
console.log(foo.count) //10
```

> 这种做法规避掉了对this的使用，并且完全依赖于变量foo的词法作用域。

```javascript
function foo(num) {
    console.log(`foo:${num}`)
    this.count++
}

foo.count = 0

for (let i = 0; i < 10; i++) {
    foo.call(foo, i)
}
console.log(foo.count) //10
```

#### 2. 它的作用域

> 需要明确的是，this在任何情况下都不指向函数的词法作用域。在JavaScript内部，作用域确实和 对象类似，可见的标识符都是它的属性。但是作用域“对象”无法通过JavaScript代码访问，它存在于 JavaScript引擎内部。

```javascript
function foo() {
    var a = 2
    this.bar() // 不可通过this访问bar
}

function bar() {
    console.log(this.a)
}

foo()  // error
```

#### 3. this到底是什么

> 1.this是在**运行时**进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件
>
> 2.this的绑定和函数声明的位置没有任何关系，只取决于**函数的调用方式**。
>
> 3.**当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）**。这个记录会包含函数在 哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this就是记录的其中一个属性，会在 函数执行的过程中用到。

## this全面解析

### 1. 调用位置

> 最重要的是要分析调用栈（就是为了到达当前执行位置所调用的所有函数）。我们关心的调用位置 就在当前正在执行的函数的前一个调用中。

```javascript
function baz() {
    //调用栈：baz
    //因此当前调用位置就是全局作用域
    console.log("baz")
    bar()
}

function bar() {
    //调用栈：baz > bar
    //因此当前调用位置在baz中
    console.log("bar")
    foo()
}

function foo() {
    //调用栈：baz > bar > foo
    //因此当前调用位置在bar中
    console.log("foo")
}

baz() // baz的调用位置
```

### 2. 绑定规则

#### 1.默认绑定

```javascript
function foo() {
    console.log(this.a)
}

var a = 2
foo()  //2
```

> 首先要介绍的是最常用的函数调用类型：**独立函数调用**。可以把这条规则看作是无法应用其他规 则时的默认规则。
>
> 通过分析调用位置来看foo()是如何调用的。在代码中，foo()是直接使用不带任何修饰的函数引用进行调用的，因此只能使用默认绑定，无法应用其他规则

#### 2. 隐式调用

```javascript
function foo() {
    console.log(this.a)
}

var obj = {
    a: 2,
    foo: foo
}
obj.foo() //2
```

> `foo()`被调用时，它的落脚点指向`obj`对象。当函数引用有上下文对象时，隐式绑定规则会把函数调用中的this绑定到这个上下文对象。因为调用foo()时this被绑 定到obj，因此this.a和obj.a是一样的
>
> 对象属性引用链中只有最顶层或者说最后一层会影响调用位置

```javascript
function foo() {
    console.log(this.a)
}

var obj1 = {
    a: 8,
    foo: foo
}
var obj2 = {
    a: 24,
    obj1: obj1
}
obj2.obj1.foo() //8
```

##### 隐式丢失

```javascript
function foo() {
    console.log(this.a)
}

var obj = {
    a: 24,
    foo: foo
}
var bar = obj.foo
var a = "global"

bar() // global
```

> 这里会触发默认绑定规则

```javascript
function foo() {
    console.log(this.a)
}

function doFoo(fn) {
    fn()
}

var obj = {
    a: 24,
    foo: foo
}
var a = "global"
doFoo(obj.foo)  //global
```

> 参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一个例子一样

#### 3. 显示绑定

> 使用 `call` 或者 `apply` 方法可以显示绑定this

```javascript
function foo() {
    console.log(this.a)
}

const obj = {
    a: 2
}
const obj2 = {
    a: 3
}
foo.call(obj) // 2
```

##### 硬绑定：解决丢失绑定的问题

```javascript
function foo() {
    console.log(this.a)
}

var obj = {
    a: 24,
}

var bar = function () {
    foo.call(obj)
}
var a = 8
bar() //24
```

> 我们创建了函数`bar()`，并在它的内部手动调用了`foo.call(obj)`，因此强制把`foo`的`this`绑定到了`obj`。无论之后如何调用函数`bar`，它总会手动 在`obj`上调用`foo`。这种绑定是一种显式的强制绑定，因此我们称之为**硬绑定**。

##### Function.prototype.bind

> 由于硬绑定是一种非常常用的模式，所以在ES5中提供了内置的方法`Function.prototype.bind`

```javascript
function foo(something) {
    console.log(this.a, something)
    return this.a + something
}

var obj = {
    a: 2
}
var bar = foo.bind(obj)
var b = bar(3) //2  3
console.log(b) //5
```

#### 4. new 绑定

##### 1.重新定义JavaScript中的“构造函数”

> 构造函数只是一些使用new操作符时被调用的函数。
>
> 它们并不会属于某个类，也不会实例化一个类。
>
> 实际上，它们甚至都不能说是一 种特殊的函数类型，它们只是被new操作符调用的普通函数而已。

**实际上并不存在所 谓的“构造函数”，只有对于函数的“构造调用”**

##### 经典面试题：new对函数做了什么

1. 创建一个全新的对象
2. 这个对象会被执行**原型**链接
3. 这个新对象会绑定到函数调用的this
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象

```javascript
function foo(a) {
    this.a = a
}

var bar = new foo(2)

console.log(bar.a) //2
```

### 3. 优先级

```javascript
function foo() {
    console.log(this.a)
}

var obj1 = {
    a: 2,
    foo: foo
}

var obj2 = {
    a: 24,
    foo: foo
}
obj1.foo()  //2
obj2.foo() //24
obj1.foo.call(obj2) // 24
obj2.foo.call(obj1) // 2
```

> 显示绑定 > 隐式绑定

```javascript
function foo(something) {
    this.a = something
}

var obj1 = {
    foo: foo
}
var obj2 = {}
obj1.foo(2)
console.log(obj1.a) //2

obj1.foo.call(obj2, 3)
console.log(obj2.a)  // 3

var bar = new obj1.foo(4)
console.log(obj1.a) //2
console.log(bar.a)  //4
```

> new绑定 > 隐式绑定

```javascript
function foo(something) {
    this.a = something
}

var obj1 = {}
var bar = foo.bind(obj1)
bar(2)
console.log(obj1.a) //2

var baz = new bar(3)
console.log(obj1.a) //2
console.log(baz.a)  //3
```

> 代码会判断硬绑定函数是否是被new调用，如果是的话就会使用新创建的this替换硬绑定的this
>
> 主要目的是预先设置函数的一些参数，这样在使用new进行初始化时就可以只传入其余的参数。
> bind(..)的功能之一就是可以把除了第一个参数（第一个参数用于绑定this）之外的其他参数都传给下层的函数（这种技术称为“部分应用”，是“柯里化”的一种）

#### 判断this

1. 函数是否在`new`中调用。如果是则`this`绑定的是新创建的对象

2. 函数是否通过`call`,`apply`显示绑定，如果是this绑定的是指定的对象

3. 函数是否在某个上下文对象中调用，如果是this就是那个上下文对象

4. 如果都不是的话，this 通过默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象


























