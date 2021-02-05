# JavaScript 的变量声明

> 新时代的前端不会再用`var`声明变量啦，基本上都用后面两种方式进行声明。这篇文章主要介绍 JavaScript 的变量声明的三种方式,以及他们之前的区别。

## 一、var 声明

#### 1. 使用`var`声明的变量，会成为包含他的函数的**局部变量**

```javascript
function fn() {
  var str = "vino";
}
fn();
console.log(str); //报错
```

#### 2. 使用`var`声明的变量会发生声明提升

> 所谓的“提升”，就是把所有的变量声明都放到函数作用域的最顶部

```javascript
function fn() {
  console.log(str);
  var str = "vino";
}
fn(); // undefined

//上述代码等价于
function fn() {
  var str;
  console.log(str);
  str = "vino";
}
```

## 二、let 声明

#### 1. 与`var`声明最大的区别：`let`声明的范围是**块级作用域**，`var`声明的范围是函数作用域

```javascript
while (true) {
  var str = "vino";
  console.log(str); // vino
}
console.log(str); // vino

while (true) {
  let str = "vino";
  console.log(str); // vino
}
console.log(str); // 报错
```

所谓块级作用域：作用域由最近的一对包含花括号{}界定

## 2. 不允许重复声明

```javascript
let str = "vino";
let str = "mamba"; //报错
```

## 3. 不会发生声明提升

```javascript
function fn() {
  console.log(str);
  let str = "vino";
}
fn(); // 报错
```

## 三、const 声明

> const 与 let 只有一处不同，那就是在声明的时候必须同时对变量进行赋值，并且该值不可以被修改

```javascript
const str = "vino";
str = "mamba"; //报错
const obj = { name: "vino", age: 24 };
obj.name = "mamba";
console.log(obj); //{ name: "mamba", age: 24 }
```

不要疑惑为啥可以修改`obj.name` ，引用类型保存的是地址值

## 四、理解 JS 变量的「创建 create、初始化 initialize 和赋值 assign」

> 以下内容，都是[参考知乎方方的文章](https://zhuanlan.zhihu.com/p/28140450)的

#### 1. 当用`var`声明时

```javascript
function fn() {
  var str = "vino";
}
fn();
```

1. 首先创建了 fn 的环境
2. 找到`var`声明的变量，创建在`fn`的环境中
3. 将`str`**初始化** 为 undefined
4. 执行代码，将`str`赋值为 `"vino"`

#### 2. 当用`function`声明时

```javascript
fn();
function fn() {
  console.log(2);
}
```

1. 找到用`function`创建的变量，在环境中创建这些变量
2. 将这个变量进行初始化，并进行赋值
3. 执行 fn()

#### 3. 当用`let`声明时

```javascript
{
  let x = 1;
  x = 2;
}
```

1. 找到{}中声明的变量
2. 执行代码
3. 执行 `x=1` 进行**初始化**
4. 执行 `x=2` 进行**赋值**

#### 4. 当用`const`声明时

与`let`声明只有一个区别，那就是`const`没有**赋值**的过程
