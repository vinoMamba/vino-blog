# this

```javascript
function toUp() {
  return this.name.toUpperCase();
}
function speak() {
  let string = `hello,i am ${toUp.call(this)}`;
  console.log(string);
}

let obj1 = {
  name: "vino",
};

let obj2 = {
  name: "mamba",
};

toUp.call(obj1); //VINO
toUp.call(obj2); // MAMBA

speak.call(obj1); //hello, i am VINO
speak.call(obj2); //hello, i am MAMBA
```

先不急着去解释上述代码的 this 是什么，我们先想一下是否有东西可以替代掉 this

```javascript
function toUp(player) {
  return player.name.toUpperCase();
}
function speak(player) {
  let string = `hello,i am ${toUp(player)}`;
  console.log(string);
}

let obj1 = {
  name: "vino",
};

let obj2 = {
  name: "mamba",
};

toUp(obj1); //VINO
speak(obj1); //
```

显然我们可以通过参数显示传递，可以理解，this 其实是一种隐式传递

## this 到底啥

1. this 在任何情况下都不指向函数的词法作用域
2. this 是在运行的时候进行绑定的，并不是在编译的时候绑定，
3. this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式

## this 的调用位置

**调用位置**：调用位置就是函数在代码中**调用**的位置（不是函数的声明位置）

```javascript
function fn1() {
  //当前调用栈：fn1
  console.log("fn1");
  fn2(); //fn2的调用位置
}
function fn2() {
  //当前调用栈： fn1 --> fn2
  console.log("fn2");
  fn3(); //fn3调用位置
}
function fn3() {
  //当前调用栈：fn1-->fn2-->fn3
  console.log("fn3");
}
fn1(); //调用位置在全局环境
```

## this 绑定原则

1. 默认规则

```javascript
function fn1() {
  console.log(this);
}
fn1();
```

此时：this 指向的是 window

**默认规则**：即函数不带有任何修饰，在全局环境调用的时候，this 指向 window

2. 隐式绑定

```javascript
function fn() {
  console.log(this.user);
}

let obj = {
  user: "vino",
  fn: fn,
};

obj.fn();
```

这里 fn 是被 obj 调用的，当 fn 被调用时，他的落脚点在 obj 上面

**隐式绑定**： 当函数引用上下文对象时，隐式绑定会将函数调用中的 this 绑定到这个上下文对象上面

```javascript
function fn() {
  console.log(this.user);
}

let obj1 = {
  user: "vino",
  fn: fn,
};
let obj2 = {
  user: "mamba",
  obj1: obj1,
};

obj2.obj1.fn(); //vino
```

这里的 this 指向的是调用它的上下文对象，即 obj1

**隐式丢失** ：当隐式绑定丢失时，this 采用默认绑定规则，绑定到 window 上面

3. 显示绑定

```javascript
function fn() {
  console.log(thi.a);
}
let obj = {
  a: 2,
};

fn.call(obj);
```

- call
- apply
- bind

4. new 绑定

使用 new 来调用函数

- 首先会创建一个全新的对象
- 这个对象会被执行原型连接
- 这个新对象会绑定到函数调用中的**this**
- 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象

```javascript
function Fn(a) {
  this.a = a;
}
let fn = new Fn(2);
console.log(fn.a); //2
```

此时新对象是 fn,会绑定到函数 Fn 中的 this
