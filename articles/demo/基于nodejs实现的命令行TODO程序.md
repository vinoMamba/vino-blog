# 实现简单的命令行 TODO 程序

## 项目介绍

node-todo 是基于`commander.js`,`inquire.js`实现的命令行 TODO 小程序。写这个项目的目的在于加深对`node.js`的文件模块的理解。

## 实现思路

借助`commander.js`和`inquire.js`库的功能实现在命令行操作，通过`node.js`的文件模块，将任务内容写入到`~/.todo`文件中。实现本地存储。

## 项目实现

#### 1. 初始化项目

```
yarn init -y
```

此时创建出`package,json`文件,值得注意的是，把版本号改成`0.0.1`

```json
{
  "name": "node-todo",
  "version": "0.0.1",
  "main": "index.js",
  "license": "MIT"
}
```

#### 2.创建`cli.js`文件

创建文件之后，引入`commander.js`。`commander.js`是完整的`node.js`命令行解决方案,详细内容见[官网](https://github.com/tj/commander.js)

```
npm install commander
```

`cli.js`关键代码如下。别问哪来的这些代码，问就是直接抄官网示例代码再进行修改的

```javascript
const { program } = require("commander");

program
  .command("add")
  .description("add a task")
  .action(() => {
    //实现添加任务
  });
program
  .command("clear")
  .description("clear all tasks")
  .action(() => {
    //清除所有任务
  });
program
  .command("showAll")
  .description("show all tasks")
  .action(() => {
    //展示所有任务
  });
program.parse(process.argv);
```

最终实现在命令行运行 `node cli`的时候，出现操作提示命令

```
node cli.js

Usage: cli [options] [command]

Options:
  -h, --help      display help for command

Commands:
  add             add a task
  clear           clear all tasks
  showAll         show all tasks
  help [command]  display help for command
```

至此，项目雏形搭建完成，输入命令执行对应的操作。如当在命令行输入 `node cli add <taskName>` 则是添加任务

#### 3.实现主要功能，创建`index.js`文件

`index.js`文件中，主要作用是实现添加任务，清除任务，展示所有任务的功能

##### 1.添加任务实现思路

添加任务的整体思路可以分成以下步骤

1. 读取本地`~/.todo`文件内容，如果没有则创建该文件(`~/.todo`路径是设置 home 目录)
2. 写入最新任务，`~/.todo`文件中的内容是一个数组，每当创建任务，就是往数组里面 push 一条数据
3. 将文件内容再重新写回到文件中去

通过上面的分析，这里需要使用到`node.js`的文件模块，来进行读写数据的操作。我将文件读写的操作封装在`db`对象上面。部分代码如下，完整代码见[github](https://github.com/vinoMamba/node-todo)

```javascript
const homedir = require("os").homedir();
const home = process.env.HOME || homedir;
const path = require("path");
const dbPath = path.join(home, ".todo");
const fs = require("fs");

const db = {
  read(path = dbPath) {
    return new Promise((resolve, reject) => {
      fs.readFile(path, { flag: "a+" }, (error, data) => {
        if (error) return reject(error);
        let list;
        try {
          list = JSON.parse(data.toString());
        } catch (error2) {
          list = [];
        }
        resolve(list);
      });
    });
  },
  write(list, path = dbPath) {
    return new Promise((resolve, reject) => {
      const string = JSON.stringify(list);
      fs.writeFile(path, string, (error) => {
        if (error) return reject(error);
        resolve();
      });
    });
  },
};

module.exports = db;
```

##### 2. 清除所有任务

清除所有任务，可以看做是将空数组写入到`~/.todo`文件中。本质还是进行写入操作`db.write([])`

##### 3. 展示所有任务

展示所有任务除了展示以外，还需要对任务进行操作，这块就需要再引入`inquire.js`库，详情可查完[官网](https://github.com/SBoudrias/Inquirer.js/)

```
npm install inquirer
```

下载好依赖之后可以直接在官网找可运行的实例，在实例的基础进行修改，来达到自己想要的效果。可参考这个[实例](https://github.com/SBoudrias/Inquirer.js/blob/master/packages/inquirer/examples/list.js)。完整代码见[github](https://github.com/vinoMamba/node-todo)

## 项目部署

项目最终发布到`npm.org`。部署步骤如下

#### 1. 修改`package.json`

添加 `bin` 和 `files`

```json
{
  "name": "vino-todo",
  "version": "0.0.1",
  "main": "index.js",
  "bin": {
    //将 node cli 映射成命令 vn
    "vn": "cli.js"
  },
  //files是指哪些文件需要打包上去
  "files": ["cli.js", "db.js", "index.js"],
  "license": "MIT",
  "dependencies": {
    "commander": "^7.1.0",
    "inquirer": "^7.3.3"
  }
}
```

#### 2.`cli.js` 文件添加 `shebang`

添加`shebang`的目的在于告诉命令行程序怎么解析代码

```
//添加到cli.js文件的首行

#!/usr/bin/env node
```

#### 3. 发布到 npm

- yarn login // 输入账号密码邮箱进行登录。没账号去 npm 官网注册即可
- yarn publish

需要注意事项就如果你使用的是淘宝源，要先切换到官方源才可进行登录发布

## 单元测试

待补充

## 总结

从这个项目中，加深了对以下内容的理解

#### 1. 项目中如何使用 promise 和 async/await

#### 2. node.js 文件模块读写 API 的使用

#### 3. commander.js inquire.js 库的使用

#### 4. 表驱动编程的优化技巧

#### 5. 单元测试以及 mock 数据
