# 你不知道的JavaScript（上）- 提升

## 先有鸡(赋值)还是先有蛋(声明)

> 结论： 现有声明，再有赋值

```javascript
var a = 2
```

> 上述代码JavaScript会看成两个声明
>
>   1. `var a` 定义声明，在编译阶段进行
>   2. `a = 2` 赋值声明，会被保留在原地等待执行阶段

```javascript
foo()

function foo() {
    console.log(a) //undefined
    var a = 1
}
```

> 这里`foo`函数的声明被提升了，所以第一行的调用可正常执行。函数里面的变量声明也提升了，所以得到的是undefined
>
> 可以被理解为下面的代码

```javascript
function foo() {
    var a
    console.log(a)
    a = 1
}

foo()
```

> 函数声明可以被提升，但是函数表达式不会被提升

```javascript
foo()  // TypeError
var foo = function bar() {
    //...do something
}
```

> 标识符`foo`变量提升，但此时foo 是 undefined ，因此调用的时候会TypeError。
>
> 与此同时与记住：即使是具名函数表达式，名称标识符在赋值之前也无法在所在的作用域使用

```javascript

foo()  // TypeError
bar() //ReferenceError
var foo = function bar() {
    //...do something
}
```

## 函数优先

> 函数会被首先提升，其次是变量

```javascript
foo() //vino
var foo

function foo() {
    console.log(1)
}

function foo() {
    console.log('vino')
}

foo = function () {
    console.log(2)
}
```

> 后面的函数声明会覆盖前面的。这听起来是一些无用的学院理论。但是在同一个作用域中重复定义是非常糟糕的

```javascript
foo()  // b
var a = true

if (a) {
    function foo() {
        console.log('a')
    }
} else {
    function foo() {
        console.log('b')
    }
}

```






























