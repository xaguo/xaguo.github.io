---
title: Websocket使用
date: 2017-09-16 13:21:17
tags: [随笔]
---

[WebSocket](http://www.websocket.org/) 协议是基于 TCP 的一种新的网络协议。它实现了浏览器与服务器全双工(full-duplex)通信——允许服务器主动发送信息给客户端。
<!-- more -->

WebSocket 伴随着 HTML5 出现的一种新技术。它和传统的 HTTP 不同。

传统的 HTTP 是由浏览器发起，服务端接收到请求之后返回一个数据。这样一次来回之后请求就断了。

Websocket 同样由浏览器发起一个 Websocket 请求，当服务器接收到 Websocket 请求的时候，会在服务器和浏览器之间建立一个 Web 端的 socket 连接，这个 socket 连接允许浏览器和服务器相互发送消息。如果浏览器和服务器都不断开的话，这个 socket 连接是不会断开的。本质就是 TCP 连接。

## 发送信息到【ws://echo.websocket.org/】，返回相同的信息。

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Send Msaagae</title>
	</head>
	<body>
	<h1>Send Message</h1>
	<input id="sendTxt" type="text">
	<button id="sendBtn">发送</button>
	<div id="recv"></div>
	<script type="text/javascript">
	    var websocket = new WebSocket("ws://echo.websocket.org/");
	    websocket.onopen = function () {
	        console.log("websocket open");
	        document.getElementById("recv").innerHTML = "connected";
	    };
	    websocket.onclose = function () {
	        console.log("websocket close");
	    };
	    websocket.onmessage=function (e) {
	        console.log(e.data);
	        document.getElementById("recv").innerHTML = e.data;
	    }
	    document.getElementById("sendBtn").onclick = function () {
	    var txt =document.getElementById("sendTxt").value;
	    websocket.send(txt)
	    };
	</script>
	</body>
	</html>
	```
## 用 Websocket 搭建自己的 websocket server， 实现 echo 功能。

服务端

安装nodejs-websocket `npm install nodejs-websocket`

	```js
	var ws = require("nodejs-websocket")
	//端口号
	var PORT=3000
	var server = ws.createServer(function (conn) {
	    console.log("New connection")
	    conn.on("text", function (str) {
	        //打印出接收到的内容
	        console.log("Received "+str)
	        // 把接收到的字符串转成大写，再在后面加 3 个感叹号，然后返回
	        conn.sendText(str.toUpperCase()+"!!!")
	    })
	    conn.on("close", function (code, reason) {
	        console.log("Connection closed")
	    })
	    //处理断开错误
	    conn.on("error",function (err) {
	        console.log("handle err")
	        console.log(err)
	    })
	}).listen(PORT)
	console.log("websocket server listening on port " + PORT);
	```

前端

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Send Msaagae</title>
	</head>
	<body>
	<h1>Send Message</h1>
	<input id="sendTxt" type="text">
	<button id="sendBtn">发送</button>
	<div id="recv"></div>
	<script type="text/javascript">
	    var websocket = new WebSocket("ws://localhost:3000/");
	    websocket.onopen = function () {
	        console.log("websocket open");
	        document.getElementById("recv").innerHTML = "connected";
	    };
	    websocket.onclose = function () {
	        console.log("websocket close");
	    };
	    websocket.onmessage=function (e) {
	        console.log(e.data);
	        document.getElementById("recv").innerHTML = e.data;
	    }
	    document.getElementById("sendBtn").onclick = function () {
	    var txt =document.getElementById("sendTxt").value;
	    websocket.send(txt)
	    };
	</script>
	</body>
	</html>
	```
## Websocket 实现聊天功能

服务端

	```js
	var ws = require("nodejs-websocket")
	//端口号
	var PORT=3000
	//客户端计数器
	var clientCount = 0
	var server = ws.createServer(function (conn) {
	    console.log("New connection")
	    clientCount++
	    conn.nickname='user'+clientCount
	    var mes={}
	    mes.type='enter'
	    mes.data= conn.nickname+" comes in"
	    broadcast(JSON.stringify(mes))
	    conn.on("text", function (str) {
	        //打印出接收到的内容
	        console.log("Received "+str)
	        // 把接收到的字符串返回
	        console.log("Connection closed")
	        var mes={}
	        mes.type='message'
	        mes.data= conn.nickname+':'+str
	        broadcast(JSON.stringify(mes))
	    })
	    conn.on("close", function (code, reason) {
	        console.log("Connection closed")
	        var mes={}
	        mes.type='leave'
	        mes.data= conn.nickname+" left"
	        broadcast(JSON.stringify(mes))
	    })
	    //处理断开错误
	    conn.on("error",function (err) {
	        console.log("handle err")
	        console.log(err)
	    })
	}).listen(PORT)
	console.log("websocket server listening on port " + PORT);
	function broadcast(str) {
	    server.connections.forEach(function (connection) {
	        connection.sendText(str)
	    })
	}
	```

客户端

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Chat Room</title>
	</head>
	<body>
	<h1>Chat Room</h1>
	<input id="sendTxt" type="text">
	<button id="sendBtn">发送</button>
	<div id="recv"></div>
	<script type="text/javascript">
	    function showMessage(str,type) {
	        var div=document.createElement('div');
	        div.innerHTML=str;
	        if(type=="enter"){
	            div.style.color="blue"
	        }else if(type=='leave'){
	            div.style.color="red"
	        }
	        document.body.appendChild(div);
	    }
	    var websocket = new WebSocket("ws://localhost:3000/");
	    websocket.onopen = function () {
	        console.log("websocket open");
	        document.getElementById("recv").innerHTML = "connected";
	        document.getElementById("sendBtn").onclick = function () {
	            var txt =document.getElementById("sendTxt").value;
	            document.getElementById("sendTxt").value="";
	           if (txt){
	               websocket.send(txt)
	           }
	    };
	    websocket.onclose = function () {
	        console.log("websocket close");
	    };
	    websocket.onmessage=function (e) {
	        console.log(e.data);
	        var mes=JSON.parse(e.data);
	        showMessage(mes.data,mes.type);
	    }
	    };
	</script>
	</body>
	</html>
	```

## socket.io 实现聊天功能 ##

socket.io 可以直接发生对象、可以发生自定义事件。

服务端

安装socket.io: `npm install socket.io`

	```js
	var app = require('http').createServer()
	var io = require('socket.io')(app);
	//端口号
	var PORT=3000
	//客户端计数
	var clientCount = 0
	app.listen(PORT);
	//连接时
	io.on('connection', function (socket) {
	    clientCount++
	    socket.nickname='user'+clientCount
	    io.emit('enter',  socket.nickname + " comes in");
	    //接收到消息时
	    socket.on('message',function (str) {
	        io.emit('message',socket.nickname+':'+str)
	    })
	    //断开连接时
	    socket.on("disconnect",function () {
	        io.emit('leave',  socket.nickname + " left")
	    })
	});
	console.log("websocket server listening on port " + PORT);
	```

客户端

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>socketio</title>
	    <script type="text/javascript" src="socket.io.2.0.3.js"></script>
	</head>
	<body>
	<h1>Chat Room</h1>
	<input id="sendTxt" type="text">
	<button id="sendBtn">发送</button>
	<div id="recv"></div>
	<script type="text/javascript">
	    function showMessage(str, type) {
	        var div = document.createElement('div');
	        div.innerHTML = str;
	        if (type == "enter") {
	            div.style.color = "blue"
	        } else if (type == 'leave') {
	            div.style.color = "red"
	        }
	        document.body.appendChild(div);
	    }
	    var socket = io("ws://localhost:3000/");
	    document.getElementById("recv").innerHTML = "connected";
	    document.getElementById("sendBtn").onclick = function () {
	        var txt = document.getElementById("sendTxt").value;
	        document.getElementById("sendTxt").value = "";
	        if (txt) {
	            socket.emit('message', txt)
	        }
	    };
	    socket.on('enter', function (data) {
	        showMessage(data,'enter')
	    })
	    socket.on('message',function (data) {
	        showMessage(data,'message')
	    })
	    socket.on('leave', function (data) {
	        showMessage(data,'leave')
	    })
	</script>
	</body>
	</html>
	```
