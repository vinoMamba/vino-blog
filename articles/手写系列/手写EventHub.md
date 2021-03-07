# 手写EventHub


## 手写思路

#### 1.首先要确定API，首先要沟通好API。跟面试官沟通好要实现的API是哪些

一般来讲，eventhub的API包括下面三个

* EventHub#on 订阅事件
* EventHub#off 取消事件 
* EventHub#emit 发布事件

