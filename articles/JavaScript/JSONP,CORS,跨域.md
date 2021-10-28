# 跨域

要搞清楚什么是跨域，需要get以下几个知识点

## 1.同源策略

浏览器规定，如果JS运行在源A里面，那么就只能获取所在源的数据，而不能获取其他源的数据。这就是同源策略。 其实就是不允许**跨域**。其目的是为了保障网络安全，保护用户的隐私。

## 2. CORS（跨域资源共享机制）

浏览器的同源策略，就阻止了不同源之间的数据传输，但是现实是，有时候又必须要实现不同源之间的数据传递，即**跨域**。 跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器 让运行在一个 origin (domain)
上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

* CORS实现跨域

```javascript
    //只需要在后端加上
response.setHeader('Access-Control-Allow-Origin', '*')
//或者指定地址    
response.setHeader('Access-Control-Allow-Origin', 'http://qq.com')
```

## 3.JSONP

CORS的确能够实现跨域，并且写起来简单，也能应用于很多场合，但是唯独一点不行，**在IE上不行，因为IE不支持CORS**。于是为了向IE妥协，便有了JSONP。

* 需求：就是实现跨域兼容IE
* 思路：我们可以随意的引用js文件（比如引入jQuery库）。于是可以把数据写到js文件里面，然后通过引入这个JS文件来获取数据！

直接上代码

```javascript
    //后端代码
if (path === '/friends.js') {
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/json;charset=utf-8')
    // 定义一个字符串
    //window.xxx()这是个函数呀
    const string = `window.xxx ({{data}})`
    // 读取数据库里面的数据，并转换成字符串
    const data = fs.readFileSync('public/friends.json')
    //将数据替换到字符串string里面
    const string2 = string.replace('{{data}}', data)
    response.end(string2)
}
//前端
//对数据进行操作
window.xxx = (data) => {
    console.log(data)
}
// 定义一个script标签，插入到body里面，这样就可以操作里面的数据
//这里将数据包裹在一个全局变量xxx里面
const script = document.createElement("script")
script.src = 'http://localhost:8888/friends.js'
document.body.appendChild(script)
```

1. 但是这里定义一个xxx全局变量并不好，比较容易冲突。其实，这里定义的变量名并不重要。只要保证这里的变量名和js文件里面的函数 名相同就可以了。
2. 怎么保证变量名不会冲突呢，这里可以设置一个随机数来保证，再不行还可以设置字符串+随机数来确保

```javascript
   //前端，设置随机数
const random = Math.random()
window[random] = (data) => {
    console.log(data)
}
const script = document.createElement("script")
script.src = 'http://localhost:8888/friends.js'
document.body.appendChild(script)
```

1. 这样又有新的问题。怎么讲这串随机数字传给后端，从而保证能对应上呢？
2. 可以在src上面设置查询参数，将参数传给http://localhost:8888

```javascript
   //前端，设置随机数
const random = Math.random()
window[random] = (data) => {
    console.log(data)
}
const script = document.createElement("script")
script.src = `http://localhost:8888/friends.js?callBack=${random}`
document.body.appendChild(script)

// 后端
else
if (path === '/friends.js') {
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/json;charset=utf-8')
    //这里是指读取查询参数
    const random = query.callBack
    const string = `window['{{random}}']({{data}})`
    const data = fs.readFileSync('public/friends.json').toString()
    const string2 = string.replace('{{data}}', data).replace('{{random}}', random)
    response.end(string2)
}
```

这样基本上就大功告成了，这就是JSONP。其实跟json没啥关系，你可以理解为：数据（json）+内边距（p），内边距将数据包裹起来放在 js文件里面，提供其他源来引用。

## 封装JSONP

封装之前先做一点优化。为了防止不断的项body里面插入script。这里可以设置，当script加载完成之后就删除掉。

```javascript
   //前端，设置随机数
const random = Math.random()
window[random] = (data) => {
    console.log(data)
}
const script = document.createElement("script")
script.src = `http://localhost:8888/friends.js?callBack=${random}`
script.onload = () => {
    script.remove()
}
document.body.appendChild(script)
```

使用promise进行封装

```javascript
const jsonP = (url) => {
    return new Promise((resolve, reject) => {
        const random = Math.random()
        window[random] = (data) => {
            resolve(data)
        }
        const script = document.createElement("script")
        script.src = `${url}?callBack=${random}`
        script.onload = () => {
            script.remove()
        }
        script.onerror = () => {
            reject()
        }
        document.body.appendChild(script)
    })
}
jsonP('http://localhost:8888/friends.js').then(data =>
    console.log(data)
)
```
