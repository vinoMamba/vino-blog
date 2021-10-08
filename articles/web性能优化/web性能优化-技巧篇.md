# web 性能优化-技巧篇

## web 性能优化指标

按下鼠标之后：

- 有内容出现时

- DOM ready 事件发生

- 页面可交互时

- onLoad 事件发生

- 动态资源加载完毕

## DNS 优化

### 1. DNS 预解析

> 对域名提前进行解析，减少等待的时间

- 方法一：head 添加 dns-prefetch

```html
<head>
  <link rel="dns-prefetch" href="xxx.com" />
</head>
```

- 方法二： 响应头添加 dns-prefetch

```
Link: <https://abc.com >; rel=dns-prefetch
```

## TCP 优化

### 1. 连接复用(http 的 keep-alive 实现 TCP 连接的复用)

```
Connection:keep-alive
```

`http`请求头添加`keep-alive`，告诉服务器不要关闭`TCP`连接，后面还要请求其他的内容。如果服务器同意，则会在响应头添也添加`kee-alive`

```
KeepAlive: timeout=5,max=100
```

同样的`http`请求头添加`KeepAlive`，告诉服务器，5s 内不要关闭`TCP`连接，最多请求 100 次。服务器如果同意，则在响应头也添加`KeepAlive`字段。并且，服务器可以调整具体的请求时间和请求次数。浏览器和服务器协商以服务器的为准。

**Tip**

> 1. 前端此功能不需要优化，因为浏览器基本上都会配置 keep-alive（IE：你直接报我身份证得了）
>
> 2. 服务端需要配置字段

### 2. 并发连接

> 建立多个 TCP 连接，一般浏览器最多可建立 6 个连接(不同浏览器的最大值不同)

```html
<head>
  <link href="xxx/1.com" />
  <link href="xxx/2.com" />
  <link href="xxx/3.com" />
</head>
```

### 3. 管道化(不好用)

> 1. 必须按照与请求相同的顺序回送 HTTP 响应。HTTP 报文中没有序列号标签，因此如果收到的响应失序了，就没办法将其与请求匹配起来。
> 2. HTTP 客户端必须做好连接会在任意时刻关闭的准备，还要准备好重发所有未完成的管道化请求。如果客户端打开了一条持久连接，并立即发出了 10 条请求，服务器可能只处理了 5 条请求之后就关闭连接，剩下的 5 条请求会失败，客户端必须能够应对这些过早关闭连接的情况，重新发出这些请求。
> 3. HTTP 客户端不应该用管道化的方式发送会产生副作用的请求(比如 POST)。总之，出错的时候，管道化方式会阻碍客户端了解服务器执行的是一系列管道化请求中的哪一些。由于无法安全地重试 POST 这样的非幂等请求，所以出错时，就存在某些方法永远不会被执行的风险。

## HTTP/2

> 数据帧

### 1. 多路复用(解决了 http/1.0 管道化的问题)

1. HTTP/2 引入了数据帧（Frame）的概念，每一帧包含 `Length`,`Type`,`Flags`,`StreamId`,`Payload`，五个部分。前四个部分是固定的长度为 9 个字节，第五部分的最大长度 16kb 到 16Mb 之间。
2. 保留了请求和响应的概念，请求头和响应头会被发送方压缩后，分成几个连续的 Frame 传输。头字段会出现在这些`Frame`的`Payload`中。接收方拼接这些`Frame`之后，解压缩即可得到真正的请求头或者响应头。
3. 引入了流（Stream）的概念，一个`Stream`由双向传输的连续有序的`Frame`组成，一个`TCP`连接可以同时包含多个`Stream`，一个`Stream`只用于一次请求或者响应。`Stream` 之间互不影响

4. 服务端可以先发响应，客户端拿到响应结果后可以保存，之后就不需要再发对应的请求。

5. 引入了伪头部字段的概念，出现在头部字段的前面，必须以冒号开头。

6. 不允许大写，全部使用小写

## 资源合并

### 1. CSS 精灵图

> CSS 精灵图已过时 --> Icon font 代替 --> SVG Symbols

- SVG 相对于 Icon Font ，可以实现**渐变**，编辑 SVG 也比 font 容易

## 资源内联

### 1. 小图片内联： DataUrl

> 通过 webpack 配置，变成 HTML 内部的内容

### 2. 小 CSS 内联

> 通过 webpack 配置，实现 `<style>内容</style>` css 内联

### 3. 小 JS 内联

> 通过 webpack 配置，实现 `<script>内容</script>` js 内联

## 资源压缩

> gzip

### 1. nginx

```
gzip on
gzip-type: xxxx
gzip_min_length: 1000
```

## 资源精简

1. HTML --> 删空格，自闭和标签
2. CSS --> 删除未使用的 CSS
3. JS --> 改变量名，treeShaking
4. SVG --> 删除无用标签，属性
5. 图片 --> 减少图片体积（有损/无损)

## 减少 Cookie 的体积

> cookie 最大可以达到 4Mb

1. 通过政策约束开发者禁止使用 cookie 传递数据，改用 localStorage 或者 sessionStorage
2. 改用新域名(cookie-free)

## CDN 内容分发网络

> 把内容分发到世界各地的网络，物理缩短距离，优化性能

DNS 负载均衡,DNS 可以返回不同的 IP，根据你所在的位置返回最近的 IP
