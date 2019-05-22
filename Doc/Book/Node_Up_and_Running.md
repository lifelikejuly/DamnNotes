# 《Node即学即用》

## Node

## 核心API
### Events
Node.js 所有的异步 I/O 操作在完成时都会发送一个事件到事件队列
```
// 引入 events 模块
var events = require('events');
// 创建 eventEmitter 对象
var eventEmitter = new events.EventEmitter();
```
### Http
```
var http = require('http');
 
// 用于请求的选项
var options = {
   host: 'localhost',
   port: '8080',
   path: '/index.html'  
};
 
// 处理响应的回调函数
var callback = function(response){
   // 不断更新数据
   var body = '';
   response.on('data', function(data) {
      body += data;
   });
   
   response.on('end', function() {
      // 数据接收完成
      console.log(body);
   });
}
// 向服务端发送请求
var req = http.request(options, callback);
req.end();
```
### I/O
Node.js 提供一组类似 UNIX（POSIX）标准的文件操作API
```
var fs = require("fs")
```
## 工具API 

### DNS

### 加密


### 进程

## 重要外部模块
### Express
Node的MVC框架，Ruby on Rails的风格提供RESTful架构。
- 模板引擎
Haml、Jade、Embedded
- 中间件
Connect是链接Express和网络的中间层，扩展了Node的基础http模块。
- http://www.expressjs.com.cn/

### 扩展Node


