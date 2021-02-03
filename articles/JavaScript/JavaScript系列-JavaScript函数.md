# JavaScript 的函数（函数是一个对象）

## 定义一个函数

1. 具名函数

```JavaScript
    function fn(x,y) {
        return x+y
    }
```

2. 匿名函数（也叫**函数表达式**，指的是等号右边的部分）

```JavaScript
    let fn = function(x,y){
        return x+y
    }
```

**考点**

```JavaScript
    let a = function fn(x,y) {
        return x+y
    }
    fn(1,2) //报错，如果具名函数在等号右边，出了这个作用域，函数名将无效
    a(1,2) // 3
```

3. **箭头函数**

```JavaScript
    let f1 = x => x*x

    let f2 = (x,y) => x*y

    let f3 = (x,y) => {
        console.log(x,y)
        return x * y
    }

    let f4 = x =>{name:x} //花括号优先会认为是一个块，所以这种写法是错误的
    let f4 = x => ({name:x}) //用括号括起来

```

4. 用构造函数

- 基本没人用
- 所有函数都是由 Function 构造出来的，包括 Object，Array，Function 也是

```JavaScript
    let f5 = new Function('x','y','return x+y')
```

5. tips： fn 和 fn()

```JavaScript
    let fn = () => console.log('vino')
    let fn2 = fn
    fn2()
```

- 首先定义了一个匿名函数，将函数的地址赋值给了 fn
- 再将 fn 的值赋值给了 fn2，其实就是将匿名函数的地址值复制给了 fn2,
- 因此执行 fn()和 fn2()都会调用函数，但 fn 和 fn2 都是函数，真正的函数是等号右边的一坨

## 函数的要素

1. 调用的时机

```JavaScript
    let i = 0
    for(i = 0; i< 6; i++){
        setTimeout(() => console.log(i),0)
    }
    //6个6
   for(let i = 0; i< 6; i++){
        setTimeout(() => console.log(i),0)
    }
    // 0,1,2,3,4,5
    //因为for 和 let 一起用的时候会加点东西；每次循环都会多创建一个i
```

2. 作用域

- 每个函数都会创建一个作用域
- 全局变量和局部变量
  - 在顶级作用域下声明的变量是全局变量
  - window 的属性是全局变量
  - 剩下的都是局部变量
- 静态作用域：跟函数执行没有关系的作用域
  - JS 的作用域：**作用域的确定跟函数执不执行么有关系**

```JavaScript
    window.c = 2
    function fn() {
        console.log(c)
    }
    fn() //2
    function fn1(){
        window.b =3 // 就算不在顶级作用域下面，window.b 仍然生效
    }
    function fn3() {
        console.log(b)
    }
    fn3() // 3
```

- 作用域的嵌套

3. 闭包

如果一个函数用到了外部的变量，那么这个函数加这个变量就叫做**闭包**

```JavaScript
    function fn1() {
        let a = 1
        function fn2(){
            //这里就形成了闭包： a 和 fn3 组成闭包
            let a = 2
            function fn3(){
                console.log(a)
            }
            a =22
            console.log(a)
            fn3()
        }
        console.log(a)
        a = 100
        fn2()
    }
    fn1()
```

4. 形式参数

- 形式参数的意思就是非实际参数，简称形参
- 形参的本质是**变量声明**

```JavaScript
    function fn(x,y) {
        return x + y
    }
    fn(1,2)
    //这里 x,y就是形参，1，2是实参，会赋值给x,y
    //上面的函数近似等价于
    function fn() {
        var x=arguments[0]
        var y=arguments[1]
        return x + y
    }
    //因此形参的本质就是变量声明
```

5. 返回值

- 每一个函数都有返回值
- 只有函数执行了才有返回值
- 只有函数有返回值
- 没有 return，那么返回值就是 undefined

```JavaScript
    function fn() {
        return console.log('hi')
    }
    //返回值是console.log('hi')的值，console.log() 也是个函数，该函数没有返回值，所以函数fn的返回值是undefined
```

6. 调用栈

- JS 引擎在调用一个函数之前
- 需要把函数所在的环境 push 到一个数组里面
- 这个数组叫做调用栈
- 然后等这个函数执行完了，就把环境 pop 出来
- 然后 return 到之前的环境，继续后面的代码

```JavaScript
    console.log(1)
    console.log(add(1,2))
    console.log(3)
```

- 递归函数

```JavaScript
    function sum(n) {
        return n !== 1 ? n + sum(n-1) : 1
    }
    console.log(sum(3))
```

7. 函数提升

8. arguments 和 this

- arguments：伪数组
- 每个函数都有 arguments 和 this ，除了**箭头函数**
- **this** ：如果不给任何条件，this 默认指向 window
- 如果传的 this 不是对象，JS 会帮你封装成对象

```JavaScript
    function fn() {
        console.log(this)
        console.log(arguments)
    }
```

- 传递 this：fn.call(xxx,1,2,3) 传 this 和 arguments
- xxx 会自动转换成对象
- this 是一个隐藏的参数，arguments 是普通参数

```JavaScript
    let person = {
        name: 'vino',
        sayHi() {
            console.log(this.name)
        }
    }

```

两种调用

- 小白调用

```JavaScript
    person.sayHi()
    //会自动把person传到函数里面，作为this
```

- 大师调用

```JavaScript
    person.sayHi.call(person)
```

重写 forEach

```JavaScript
    Array.prototype.forEach2 = function(fn){
        for(let i = 0; i< this.length; i++){
            fn(this[i],i)
        }
    }
    let arr = [1,2,3,4,5]
    arr.forEach2.call(arr,(item) => console.log(item))
```

- **绑定 this** bind()

```JavaSCript
    function f1(p1,p2) {
        console.log(this,p1,p2)
    }
    let f2 = f1.bind({name:'vino'})
    //等价于
    f1.call({name:'vino'})
```

9. 箭头函数

**没有 arguments 和 this**

- 里面的 this，就是外面的 this
- 就算加 call 都没有

10. 立即执行函数（用的比较少了）

```JavaScript
    ! function(){
        var a = 2
        console.log(a)
    }()
    //推荐使用加!号
    (function() {
        var a = 2
        console.log(a)
    }())
    //不推荐使用，如果这样用，上一条语句要加上分号；这也是代码中唯一要写分号的地方
```
