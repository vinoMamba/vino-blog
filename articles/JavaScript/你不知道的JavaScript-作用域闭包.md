# 你不知道的JavaScript（上）- 作用域闭包

## 实质问题

> 当函数可以记住并访问所在的词法作用域时，就产生的闭包。即使函数是在当前作用域之外执行

```javascript
function foo() {
    var a = 2

    function bar() {
        console.log(a)
    }

    bar()
}

foo()
```

> 根据前面的定义，确切的说并不是一个闭包。从技术上来讲，他或许是

```javascript
function foo() {
    var a = 2

    function bar() {
        console.log(a)
    }

    return bar
}

var baz = foo()
baz() //2
```

> 1. 函数`bar`的词法作用域能够访问`foo`的内部作用域
> 2. 函数`foo`将`bar`所引用的函数对象本身作为返回值返回出去
> 3. `foo`执行之后，其返回值赋值给了变量`baz`，并有调用`baz`
>
> 闭包的神奇之处：组织垃圾回收器进行回收
>
> `bar`声明的位置，使他拥有涵盖`foo`内部作用域的闭包。使得该作用域能够一直存活

```javascript
function foo() {
    var a = 2

    function baz() {
        console.log(a)
    }

    bar(baz)
}

function bar(fn) {
    fn() // 这就是闭包
}

```

```javascript
var fn

function foo() {
    var a = 2

    function baz() {
        console.log(a)
    }

    fn = baz
}

function bar() {
    fn()  //这就是闭包
}

foo()
bar()
```

> 无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引 用，无论在何处执行这个函数都会使用闭包。













































