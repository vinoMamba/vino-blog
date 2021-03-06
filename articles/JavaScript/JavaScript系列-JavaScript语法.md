# JavaScript 语法

[参考链接](https://wangdoc.com/javascript/basic/grammar.html)

## 一、表达式与语句

#### 表达式

- 1+2 表达式的值是 3
- add(1,2)**表达式**的值为函数的返回值（只有函数有返回值）

#### 语句

- var a = 1 是一个语句

#### 表达式与语句的区别

- 表达式一般都有值
- 语句一般会改变环境（声明、赋值）
- 上述并非绝对，是大部分情况

## 二、空格和回车

- 大部分空格和回车么有实际意义
- 唯独：**return 后面加回车会返回 undefined**

## 三、标识符

#### 规则

1. 第一个字符，可以是 unicode 字符或者\$、\_、中文
2. 后面的字符，除了上述所说，**还可以是数字**

## 四、如何写注释

- 踩坑注解
- 遇到奇怪的需求，导致写出的奇怪的代码，增加备注

## 五、条件语句

#### 1、if 语句

语法

> if(表达式){语句 1}else{语句 2}

最推荐的写法

```javascript
if (表达式) {
  语句;
} else if (表达式) {
  语句;
} else {
  语句;
}
//或者在函数里面
function fn() {
  if (表达式) {
    return 表达式;
  }
  if (表达式) {
    return 表达式;
  }
  return 表达式;
}
```
#### 3、问号冒号表达式（三元表达式）（☆）
> 表达式1 ? 表达式1 : 表达式2;
在实际项目中三元表达式的写法会很多
```javascript
if(status === 0){
	return "登录成功"
	}else{
	return "登录失败"
	}
//等价于
status === 0 ? "登录成功" : "登录失败"
```
** tips：不要再在你的项目里面写 `==` ，取而代之的是 `===`**。后面如果有计划会写一篇关于== 和 === 的博客
[关于== 和 === 的解释](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)

#### 4、&& 短路逻辑 （☆）

```javascript
A && B && C && D;
//  取第一个假值或者D;
console && console.log() && console.log('hi);
```
`&&`短路逻辑的应用场景经常用来判断这个函数是否存在，如果存在执行
```javascript
fn && fn()  //如果fn 函数存在，那么就执行
```
#### 5、|| 短路逻辑（☆）

```javascript
A || B || C || D;
//取第一个真值或者D;
a = a || 100;
//等价于
if (a) {
  a = a;
}lese {
    a = 100;
}
```
`||`短路逻辑会经常用于赋值
## 六、循环

#### 1、while 循环

```javascript
var a = 0.1;
while (a !== 1) {
  console.log(a);
  a += 0.1;
}
//浮点数相加的数字会变得不精确，导致怎么加都不会==1；所以会死循环
```

#### 2、for 循环

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i);
  });
}
// 打印出5个5
//将var 换成 let 可解决
for (let i = 0; i < 5; i++) {
  setTimeout(() => {
    console.log(i);
  });
}
//0,1,2,3,4
```

#### 3、break 和 continue

#### 4、label

```javascript
foo: {
  console.log(1);
  break;
  console.log("本行不会输出");
}
console.log(2);
```
