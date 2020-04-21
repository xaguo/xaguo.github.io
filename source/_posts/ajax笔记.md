---
title: ajax笔记
date: 2017-8-13 18:40:45
tags: 随笔
---

异步JavaScript和XML，使用 JavaScript 发起异步请求。
<!-- more -->

- 浏览器中`XMLHttpRequest` 示例

	    ```js
		function ajax(url, fnSucc, fnFaild)
		{
			//1.创建ajax对象
			var oAjax=null;
			// 兼容 IE
			if(window.XMLHttpRequest)
			{
				oAjax=new XMLHttpRequest();
			}else{
				oAjax=new ActiveXObject("Microsoft.XMLHTTP");
			}
			
			//2.连接服务器
			//open(方法, url, 是否异步)
			oAjax.open('GET', url, true);
			
			//3.发送请求
			oAjax.send();
			
			//4.接收返回
			//OnReadyStateChange
			// http 请求状态变化 回调
			oAjax.onreadystatechange=function ()
			{
				// 成功完成
				if(oAjax.readyState==4)
				{
					if(oAjax.status==200)
					{
						//alert('成功：'+oAjax.responseText);
						fnSucc(oAjax.responseText);
					}
					else
					{
						if(fnFaild)
						{
							fnFaild();
						}
					}
				}
			};
	
			// ajax.open('GET','http://www.baidu.com')
			// ajax.send();
		}
		```

- JQuery 中的 ajax

		```js
		// url setting
		// async 表示是否异步执行 ajax 请求，默认为 true，一般不指定为 false
		// method 表示请求的方法，默认为 GET 可以指定为 POST PUT 等
		// contentType 发送 POST 请求的格式 application/x-www-form-urlencode;application/json
		// data 发送的数据
		// headers http 的 headers
		// dataType 接受的数据格式
		
		$.ajax('http://www.baidu.com',{
			dataType: 'json'
		}).done(function(data){
			console.log('success',data);
		}).fail(function(xhr,status){
			console.log(xhr.status);
		});
		
		$.get('http://www.baidu.com',{id:1})
		.done(function(data){
			console.log('get',data);
		})
		.fail(function(xhr,status){
			console.log(xhr.status);
		});
		
		$.post('http://www.baidu.com',{id:1})
		.done(function(data){
			console.log('post',data);
		})
		.fail(function(xhr,status){
			console.log(xhr.status);
		});
		
		$.getJSON('http://www.baidu.com',{id:1})
		.done(function(data){
			console.log('getJSON',data);
		})
		.fail(function(xhr,status){
			console.log(xhr.status);
		});
		
		```