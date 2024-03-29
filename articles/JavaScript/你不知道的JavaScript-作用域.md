# 你不知道的JavaScript（上）- 作用域

## 第一章：作用域是什么

> 设置一套良好的规则来存储变量，并且之后可以很方便找到这些变量。这套规则称之为“作用域”

```javascript
var a = 2
```

1. 引擎：从头到尾负责整个JavaScript程序编译以及执行过程

2. 编译器：引擎的好朋友之一，负责`语法分析`和`代码生成`等脏话累活

3. 作用域：负责手机并维护所有声明的标识符（变量）组成的一系列查询，并实施一套非常严格的规则，**确定当前执行的代码对这些标识符的访问权限**

### 执行的过程

#### 1. 编译器首先将程序分解为**词法单元**,然后将词法单元解析成一个**树结构**

> 1. 遇到`var a`，编译器会询问作用域当前是否已经有一个名叫`a`的变量存在**同一个作用域**中。
>
>* 如果有：编译器就会忽略本次的声明，继续进行编译
>* 如果没有：编译器就会在当前作用域的集合中声明一个`a` 的变量

> 2. 接下来编译器会为**引擎**生成运行时所需要的代码，这些代码用来处理`a = 2`这个赋值操作
>
> 引擎运行时会询问**作用域**，当前的作用域集合是否存在一个`a`的变量。如果有就会使用这个变量，没有就会继续查找改变量
>
> 如果最终引擎找到了变量`a`，就会将值`2`赋值给他，没有则抛出异常

##### 总结

> **变量的赋值操作会执行两个动作**
>
> 1. 首先编译器会在当前**作用域集合**中声明一个变量
> 2. 然后在运行时**引擎**会在作用域集合中查找该变量，如果能找到就赋值给他

#### 2. 编译器有话说

##### 1. 编译器的`LHS`查询和`RHS`查询

> 上述例子中，引擎会为变量`a`进行`LHS`查询
>
> 1. 当变量出现在赋值操作的左侧时进行`LHS`查询
> 2. `RHS`查询与简单的查找某个变量的值别无二致，而`LHS`查询是试图找到变量的容器本身，从而可以对其进行赋值
> 3. `RHS`并不是真正意义上的“赋值操作的右侧”，更准确的说是“非左侧”
> 4. `RHS` 可以理解成得到某某的值

```javascript
// RHS 引用
console.log(a)
// LHS 引用
a = 2
```

分析如下代码

```javascript
function foo(a) {
    console.log(a)
}

foo(2)
```

1. `foo(2)`的调用需要对`foo`进行**RHS**引入，意味着去找到foo的值
2. `foo(2)` 存在 `a = 2`的隐式操作，是一次**LHS**查询
3. 还有对`a`的**RHS**引用,将值传给了`console.log()`
4. `console.log()`中`console`对象会进行一次**RHS**查询，并且检查查找值中是否有一个叫`log`的方法
5. ....

#### 3. 嵌套作用域

> 当一个块或者函数嵌套在另一个函数中，就会发生作用域的**嵌套**，因此在当前作用域中找不到某个变量时，引擎就会在外侧嵌套的作用域中继续查找，直到找到该变量，或抵达最外层作用域（全局作用域）

#### 4. 异常

> 1. 如果**RHS**查询在所嵌套的作用域中都找不到所需的变量，引擎就会抛出`ReferenceError`异常
> 2. 相比之下，**LHS**如果在顶层的作用域中也无法找到目标变量，全局作用域中就会创建一个具有该名称的变量，并返回给引擎
> 3. **RHS** 如果查询到了一个变量，但是尝试对这个变量的值进行不合理的操作，比如对一个非函数类型的变量进行调用，就会抛出`TpeError`的异常



























