## 1、apply

[参考 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

#### 定义

apply() 方法调用一个具有给定 this 值的函数，以及以一个数组（或类数组对象）的形式提供的参数。

#### 语法

`func.apply(thisArg,[argsArray])`

1. thisArg: **必选**的。在 func 函数运行时使用的 this 值。
2. argsArray: 可选的。**一个数组或者类数组对象**，其中的**数组元素**将作为**单独的**参数传给 func 函数。如果该参数的值为 null 或 undefined，则表示不需要传入任何参数。

#### 用 apply 将数组各项添加到另一个数组

```JavaScript
    let arr1 = ['vino','ashin']
    let arr2 = ['kobe','mamba']
    //如果直接用push()
    arr1.push(arr2) // ['vino','ashin',['kobe','mamba']]
    //使用apply
    arr1.push.apply(arr1,arr2) //['vino','ashin','kobe','mamba']
```

上面的代码，为啥改用 apply 就可以了呢？因为 apply 方法，**将 arr2 数组元素作为单独的参数**push 到 arr1 的！！！

#### 求 N 个数中的最小值

```JavaScript
    let min = (numbers) => {
        if (numbers.length > 2) {
            return min([numbers[0], min(numbers.slice(1))]);
        } else {
            return numbers[0] < numbers[1] ? numbers[0] : numbers[1];
        }
    };
    console.log(min.apply(null, [[2, 33, 1, 56, 3]]));
```

同样的，上面的代码调用的时候，为啥要加双[[]]呢，其实很好理解，**因为函数接收的参数是整个数组，而 apply 要接收的参数必须是数组或者类数组形式**，因此需要用[] 将数组括起来

## 2、call

[参考 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

#### 定义

call() 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。

#### 语法

`function.call(thisArg, arg1, arg2, ...)`

1. thisArg:**可选的**。在 function 函数运行时使用的 this 值。请注意，this 可能不是该方法看到的实际值：**如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。**

2. arg1, arg2, ...:指定的参数列表。

#### 返回值

使用调用者提供的 this 值和参数调用该函数的返回值。**若该方法没有返回值，则返回 undefined。**

```JavaScript
    var sName = 'vino' //这里只能用var 用let是不能声明全局变量的
    function display() {
        console.log('hello. i am ' + this.sName)
    }
    display.call(null)
    //如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象
    //等价于
    display.call()
    //原因在于如果没有传递第一个参数，this 的值将会被绑定为全局对象。
```

看下面的例子

```JavaScript
let nbaStar = [
  { player: "Kobe", agName: "Mamba" },
  { player: "LeBron", agName: "King" },
];

for (let i = 0; i < nbaStar.length; i++) {
  !function (i) {
    this.print = () =>
      console.log("#" + i + " " + this.player + ": " + this.agName);
    this.print();
  }.call(nbaStar[i], i);
}
//#0 Kobe: Mamba
//#1 LeBron: King
nbaStar[0].print();
//#0 Kobe: Mamba
```

上面的代码，通过 call，指定了数组的每一项为 this 值，通过循环给每个对象添加了 print 方法

## 3、bind

[参考 MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bindg)

#### 定义

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

#### 语法

`function.bind(thisArg[, arg1[, arg2[, ...]]])`

1. **thisArg**: 调用绑定函数时作为 this 参数传递给目标函数的值。 如果使用 new 运算符构造绑定函数，则忽略该值。当使用 bind 在 setTimeout 中创建一个函数（作为回调提供）时，作为 thisArg 传递的任何原始值都将转换为 object。如果 bind 函数的参数列表为空，或者 thisArg 是 null 或 undefined，执行作用域的 this 将被视为新函数的 thisArg
2. **arg1, arg2, ...**:当目标函数被调用时，被预置入绑定函数的参数列表中的参数。

#### 返回值

返回一个原函数的拷贝，并拥有指定的 this 值和初始参数。

```JavaScript
this.x = 9;
let module = {
  x: 81,
  getX: function () {
    return this.x;
  },
};

console.log(module.getX()); // 81

let retrieveX = module.getX;
console.log(retrieveX());
// 返回 9 - 因为函数是在全局作用域中调用的

// 创建一个新函数，把 'this' 绑定到 module 对象
var boundGetX = retrieveX.bind(module);
console.log(boundGetX()); // 81

```
