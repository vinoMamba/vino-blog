# ES6 新增类型-Symbol

> 关键词： 唯一的

symbol 是 ES6 新增的一种数据类型，属于原始类型，且实例时唯一的，不可变的

## Symbol 的应用场景

实际开发中`Symbol`的应用场景不是很多,这里只举一个例子来说明

> 当设计展示不同 tab 页面内容时，经常会使用一个变量来表示当前页面，可以理解为当前页面的唯一的 KEY

```javascript
function toggle(tabType) {
  if (tabType === "tab1") {
    //给我展示tab1页面的内容
  }
  if (tabType === "tab2") {
    //给我展示tab2页面的内容
  }
}
```

上述代码中，其实我们并不关心`tabType` 具体是等于字符串 tab1 还是字符串 tab2,tab1 可以换成任何一个内容，只要保证不是字符串 tab2 就行，对于 tab2 也是这样。因此可以使用**Symbol** 来代替这些字符串

```javascript
const tabType = {
  tab1: Symbol(),
  tab2: Symbol(),
};
function toggle(tabType) {
  if (tabType === tabType.tab1) {
    //给我展示tab1页面的内容
  }
  if (tabType === tabType.tab2) {
    //给我展示tab2页面的内容
  }
}
```

Symbol 是唯一的，所以`tabType.tab1`永远也不会等于 `tabType.tab2`
