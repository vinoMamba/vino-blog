# 手写 EventHub

## 手写思路

#### 1.首先要确定 API，首先要沟通好 API。跟面试官沟通好要实现的 API 是哪些

一般来讲，eventhub 的 API 包括下面三个

- EventHub#on 订阅事件
- EventHub#off 取消事件
- EventHub#emit 发布事件

## 第一版

```javascript
class EventBus {
  cache = {}; //添加缓存
  on(eventName, fn) {
    //初始化缓存
    if (!this.cache[eventName]) {
      this.cache[eventName] = [];
    }
    this.cache[eventName].push(fn);
  }
  emit(eventName) {
    if (!this.cache[eventName]) {
      this.cache[eventName] = [];
    }
    this.cache[eventName].forEach((fn) => {
      fn();
    });
  }
  off(eventName, fn) {
    if (!this.cache[eventName]) return;
    let index = undefined;
    for (let i = 0; i < this.cache[eventName].length; i++) {
      if (this.cache[eventName][i] === fn) {
        index = i;
      }
    }
    if (index === undefined) return;
    this.cache[eventName].splice(index, 1);
  }
}
export default EventBus;
```

## 第二版：对代码进行优化

```javascript
class EventBus {
    private cache = {}  // 属性私有,禁止外部访问
    on(eventName, fn) {
        //当this.cache[eventName] === undefined 时，则初始化成空数组
        this.cache[eventName] = this.cache[eventName] || []
        //将执行函数push到数组中
        this.cache[eventName].push(fn)
    }
    emit(eventName) {
        this.cache[eventName] = this.cache[eventName] || []
        // 遍历并执行函数
        this.cache[eventName].forEach(fn => fn());
    }
    off(eventName, fn) {
        //查找订阅的事件里面是否有这个函数,查找到则从数组中删除
        const index = indexOf(this.cache[eventName], fn)
        if (index === -1) return
        this.cache[eventName].splice(index, 1)
    }
}

export default EventBus
/**
 *  helper function
 * @param array
 * @param item
 * @returns
 */
function indexOf(array, item) {
    if (array === undefined) return -1
    let index = -1
    for (let i = 0; i < array.length; i++) {
        if (array[i] === item) {
            index = i
            break
        }
    }
    return index
}
```

## 第三版： 增加类型

```javascript
class EventBus {
    private cache: { [key: string]: Array<(data: unknown) => void> } = {} // 属性私有,禁止外部访问
    on(eventName: string, fn: (data: unknown) => void) {
        //当this.cache[eventName] === undefined 时，则初始化成空数组
        this.cache[eventName] = this.cache[eventName] || []
        //将执行函数push到数组中
        this.cache[eventName].push(fn)
    }
    emit(eventName: string, data?: unknown) {
        this.cache[eventName] = this.cache[eventName] || []
        // 遍历并执行函数
        this.cache[eventName].forEach(fn => fn(data));
    }
    off(eventName: string, fn: (data: unknown) => void) {
        //查找订阅的事件里面是否有这个函数,查找到则从数组中删除
        const index = indexOf(this.cache[eventName], fn)
        if (index === -1) return
        this.cache[eventName].splice(index, 1)
    }
}

export default EventBus
/**
 *  helper function
 * @param array
 * @param item
 * @returns
 */
function indexOf(array: Array<(data: unknown) => void>, item: (data: unknown) => void) {
    if (array === undefined) return -1
    let index = -1
    for (let i = 0; i < array.length; i++) {
        if (array[i] === item) {
            index = i
            break
        }
    }
    return index
}
```
