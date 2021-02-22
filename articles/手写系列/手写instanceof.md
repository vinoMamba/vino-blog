# 手写 instanceof

> instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链

## 原理

判断等号左边对象的`__proto__` 是否等于等号右边变量的`prototype`。否则继续沿着原型链去匹配。如果匹配失败，返回 false，成功则返回 true

```javascript
function newInstanceOf(left: Object, right: Function) {
  //如果用ts,是不是就不用判断是否是基础类型了？
  // 首先判断等号左边的是否是基础类型，如果是则直接返回false
  const baseType = ["string", "number", "boolean", "undefined", "symbol"];
  if (baseType.indexOf(typeof left) !== -1) {
    return false;
  }
  const rightPrototype = right.prototype;
  const leftProto = left.__proto__;
  while (true) {
    if (leftProto === null) {
      return false;
    }
    if (leftProto === rightPrototype) {
      return true;
    }
    leftProto = leftProto.__proto__;
  }
}
```
