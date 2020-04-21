---
title: JavaScript基础知识
date: 2017-010-14 11:33:03
tags: [随笔,JavaScript]
---

JavaScript基础知识学习记录。
<!-- more -->

参考书 : 《JavaScript 权威指南》。[参考资料](https://developer.mozilla.org/zh-CN/docs/learn/JavaScript)

## 数据类型

- 6 种数据类型

	```js
	//弱类型
	var num = 32;
	num = "this is a number";
	
	32 + 32  //->64
	"32" + 32  //->"3232"
	"32" - 32  //->0
	/*
	
	                    |- Function
	                    |- Array
	          |- object-|- Date
	          |         |- ...
	          |- number
	          |- string
	原始类型 --|- boolean
	          |- null
	          |- undefined
	*/
	```
- 隐式转换

	```js
	var x= 'The answer is' + 42;
	var x= 42 + 'is the answer ' ;
	
	//num - 0
	
	"37" - 0//->37
	
	//num + “”
	
	37 + ""//->"37"
	
	//等于 ==
	
	"1.23" == 1.23;//当一边是数字时，强制转成//数字
	0 == false;
	null == undefined;
	new Object() == new Object();
	[1,2]==[1,2];
	//类型相同,同 ===
	//类型不同，尝试类型转换后比较：
	null == undefind; //相等
	//number == string 转 number
	1 == "1.0";//->true
	//boolean == ？转 number
	1 == true;//->true
	//object == number|string尝试对象转为基本类型
	new String('hi')=='hi';//true
	new String('5')==5;//true
	//其他类型不同 : false
	
	//严格等于 ===
	
	//类型不同，直接返回 false
	//类型相同：
	null === null;
	undefined === undefined;
	NaN != NaN;
	new Object != new Object;//比较的是引用
	```

- 包装对象

	```js
	var str = "string"// 基本数据类型
	var strObj = new String("string")//包装对象类型
	var a = "string";
	alert(a.lenth);//6 // - >自动把基本类型转成对象
	a.t=3;
	alert(a.t)//undefined//-> 完成访问后临时对象会销毁
	//str - > String Object
	//123 - > Number Object
	//true - > Boolean Object
	```
- 类型监测

typeof

	```js
	//typeof 100 "num"
	//typeof true "boolean"
	//typeof function "function"
	//typeof undefined "undefined"
	//typeof new Object() "object"
	//typeof [1,2] "object"
	//typeof null "object" ===> typeof === "object"兼容问题
	//typeof NaN "number"
	```
instanceof

左操作数是一个对象，否则返回 false

右操作数是一个函数对象或者函数构造器，否则抛出 typeerror 异常

	```js
	//obj instanceof Object 判断对象原型链上是否有右边函数的 prototype 属性
	[1,2] instanceof Array === true;
	new Object() instanceof Array === false;
	function Person(){}//undefined
	function Student(){}//undefined
	Student.prototype=new Person()//Person
	Student.prototype.constructor=Student//function Student(){}
	var bosn = new Student()
	bosn instanceof Student//true
	var one = new Person()
	one instanceof Person//true
	one instanceof Student//false
	bosn instanceof Person//true
	```
**注意：不同 window 或 iframe 间的对象检测不能使用 instanceof！！！看上去相同的对象会返回 false**

Object.prototype.toString

	```js
	Object.prototype.toString.apply([]);//===”[object Array]”
	
	Object.prototype.toString.apply(function(){});//===”[object Function]”
	
	Object.prototype.toString.apply(null);//===”[object Null]”
	
	Object.prototype.toString.apply(undefined);//===”[object Undefined]”
	
	//IE 6/7/8 Object.prototype.toString.apply(null)//返回”[object Objecr]”
	```
**constructor**

constructor 指向构造这个对象的构造器，注意 constructor 可以被改写。

**duck type**

通过对象的特征判断对象的类型。

- 小结
- 
typeof 适合基本类型及 function 检测，遇到 null 失败(用严格 === null)。

[[Class]] 通过 {}.toString 拿到，适合内置对象和基元类型，遇到 null 和 undefined 失效 (IE 6/7/8 Object.prototype.toString.apply(null)返回”[object Objecr]”)

instanceof 适合自定义对象，也可以用来检测原生对象，在不同 iframe 和 window 间检测时失效。

## 表达式

wiki 表达式是指能算出值得任何可用程序单元。

JS 权威指南 表达式是一种 JS 短语，可以使 JS 解释器用来产生一个值。

	```js
	// 原始表达式
	// 常量，直接量 3.14 "test"
	// 关键字 null , this , true
	// 变量 i ，j , k
	// 符号表达式
	10 * 20
	// 数组对象的初始化表达式
	[1,2] //-> new Array(1,2);
	[1,,,2] //-> [1,undefined,undefined,4]
	{x:1,y:2} //-> var o = new Object(); o.x=1;o.y=2;
	// 函数表达式
	var fe = function(){};
	(function)(){console.log('Hello World';)}();
	// 属性访问表达式
	var o = {x:1}
	o.x
	o['x']
	// 调用表达式
	func();
	// 对象创建表达式
	new Func(1,2);
	new Object;
	```

## 运算符

	```js
	/*
	一元 + num
	二元 a + b
	三元 c ？a :b
	赋值 x += 1
	比较 a == b
	算术 a - b
	位 a | b
	逻辑 exp1 && exp2
	字符串 "a" + "b"
	特殊 delete obj.x
	*/
	
	//运算符 ? : (c ? a : b) 
	var val true ? 1 : 2;//val = 1
	//运算符 ,  (a,b)
	var val = (1,2,3)//val=3
	//运算符 delete (delete obj.x;)
	var obj = {x: 1};
	obj.x;//1
	delete obj.x;
	obj.x; //undefined
	//运算符 delete
	var obj = {};
	//给 object 设置 x 属性
	Object.defineProperty(obj,'x'{
	configurable:false,//false 时不能 delete
	value:1
	});
	delete obj.x;//false
	obj.x;//1
	//运算符 in
	window.x = 1;
	'x' in window;//true
	//运算符 instanceof , typeof
	{}instanceof Object //true  判断对象的类型，基于原型链判断
	typeof 100 === 'number' //true 判断原始类型或者函数对象，返回一个字符串。
	//运算符 new
	function Foo(){}
	foo.prototype.x = 1;
	var obj = new Foo();
	obj.x;//1
	obj.hasOwnProperty('x');//false
	obj.__proto__.hasOwnProperty('x');//true
	//运算符 this
	this;//window(浏览器)
	var obj = { func : function(){return this;}
	};
	obj.func();//obj
	//运算符 void
	void 0//undefined
	void(0)//undefined
	```

## 语句 Statement

JavaScript 程序由语句组成，语句遵守特定的语法规则。

例如：if 语句，while 语句，with 语句等等。

**块 block**:块语句常用于组合 0 ~ 多个语句。块语句用一对花括号定义。

	```js
	/*
	语法：
	{
	语句 1;
	语句 2;
	...
	语句 n;
	}*/
	
	{
	var str = "hi";
	console.log(str);
	}
	
	if(true){
	console.log('hi');
	}
	
	{a:1,b:2}//SyntaxError : Unexpected token: [当成块，所以报语法错误]
	var o = {a:1,b:2};//ok
	```

**注意：没有块级的作用域**

	```js
	/*
	                                var i = 0;
	for(var i=0;i<10;i++){          for(;i<10;i++){
	var str = 'hi';          =      var str = 'hi'
	console.log(str);               console.log(str);
	}                               }
	
	{                               var x=1;
	var x = 1;        =             {
	//...                            //...
	}                               }
	*/
	function foo(){
	var a = 1;
	console.log(a);//1
	}
	foo();
	confole.log(typeof a);//undefined
	```

**声明语句 var**

	```js
	var a =1;
	var a=b=1;//b 是隐式的创建了一个全局变量
	function foo(){
	var a = b = 1;
	}
	foo();
	console.log(typeof a)//undefined
	console.log(typeof b)//number
	var a=1,b=1;
	```
**try catch 语句**

```js
	//try - catch;try - finally;try - catch - finally
	try{
	throw "test";//抛出异常
	}catch(ex){
	console.log(ex);//test 有异常则捕获异常
	}finally{
	console.log("finally");
	}
	// ====><=========
	try{
	    try{
	        throw new Error("oops");//抛出异常到最近的 catch，跳出前先执行 finally
	    }finally{
	        console.log("finally");
	    }
	}carch(ex){
	    console.error("outer",ex.message);
	}
	// ==>结果
	//"finally"
	//"outer""oops"
	// ====><=========
	 try{
	         try{
	             throw new Error("oops");//抛出异常到最近的 catch，跳出前先执行 finally
	         }catch(ex){
	             console.error("inner",ex.message);
	         }finally{
	              console.log("finally");
	         }
	 }carch(ex){
	     console.error("outer",ex.message);
	 }
	 / ==>结果
	 //"inner""oops"
	 //"finally"
	 // ====><=========
	  try{
	          try{
	              throw new Error("oops");//抛出异常到最近的 catch，跳出前先执行 finally
	          }catch(ex){
	              console.error("inner",ex.message);
	              throw ex;
	          }finally{
	               console.log("finally");
	          }
	  }carch(ex){
	      console.error("outer",ex.message);
	  }
	  / ==>结果
	  //"inner""oops"
	  //"finally"
	  //"outer""oops"
	```
**函数、switch、循环**

function

	```js
	fd();//true //函数声明会被前置处理，可以在前面调用
	//函数声明
	function fd(){
	    //do something
	    retirn true;
	}
	//函数表达式//函数表达式不能前置调用
	fe();//TypeError
	var fe = function(){
	    //do something
	};
	```
for…in

	```js
	var p;
	var obj = {x:1,y:2}
	for (p in obj){
	}
	//for in 
	//1. 顺序不确定
	//2. enumerable 为 false 时不会出现，为 true 时会出现
	//3. for in 对象属性时受原型链影响
	```
switch

	```js
	val=2;
	//====><=====================
	switch(val){
	    case 1:
	        console.log(1);
	        break;
	    case 2:
	        console.log(2);
	        break;
	    default:
	        console.log(0);
	        break;
	}//--->结果 2
	
	//====><=====================
	switch(val){
	    case 1:
	        console.log(1)
	    case 2:
	        console.log(2);
	    dafault:
	        console(0);
	}//--->结果 2 0
	
	//====><=====================
	switch(val){
	case 1:
	case 2:
	case 3:
	    console.log(123);
	    break;
	case 4:
	case 5:
	    console.log(45)
	    break;
	dafault:
	    console.log(0);
	}---> 输出123
	```

循环

	```js
	while(isTrue){
	    //do something
	}
	do{
	    //do something
	}while(isTrue)
	var i;
	for(i=0;i<n;i++){
	    //do something
	}
	```

with

	```js
	//with 可以修改作用域，可以用 x 直接输出1，不需要用 obj.x
	with({x:1}){
	    console.log(x);
	}
	with(document.forms[0]){
	    console.log(name.value);//== 隐式调用 document.forms[0].name.value
	}
	// ||
	var form = document.forms[0];
	console.log(form.name.value);        
	//让 JS 引擎优化更难
	//可读性差
	//可被变量代替
	//**严格模式下禁用**
	```
## 严格模式

严格模式是一种特殊的执行模式，它修复了部分语言上的不足，提供更强的错误检查，并增强安全性。

用法

	```js
	function func(){
	    'use struct';//这样可以兼容，不支持时被忽略
	}
	//
	'use strict';
	function func(){
	}
	```
严格模式不允许使用 with

	```js
	!function(){
	    with({x:1}){
	        console.log(x);
	    }
	}();//一般情况，输出1
	!function(){
	‘use strict'
	    with({x:1}){
	        console.log(x);
	    }
	}();//严格模式 SyntaxError
	```
严格模式不允许未声明的变量被赋值

	```js
	!function(){// ！表示函数表达式而不是函数声明
	    x=1;
	    console.log(window.x)
	}();//一般情况下，输出 1
	!function(){
	    'use strict'
	    x=1;
	    console.log(window.x)
	}();//严格模式下，报 ReferenceError
	```
严格模式 arguments 变为参数的静态副本

	```js
	!function(a){
	    arguments[0]=100;
	    console.log(a);
	}(1);//a 传入 1 ,输出 100// a不传 输出 undefined
	
	!function(a){
	    'use strict';
	    arguments[0]=100;
	    console.log(a)
	}(1);//仍然输出 1
	
	!function(a){
	    'use strict';
	    arguments[0].x=100;
	    console.log(a)
	}({x:1});//仍然输出 100[如果传入对象，修改对象属性，会相互影响]
	```

严格模式 删除参数、函数名报错

	```js
	!function(a){
	    console.log(delete a);
	}(1);//删除失败，返回 false ,输出 false
	!function(a){
	    'use strict';
	    console.log(delete a);
	}(1);//严格模式下，报 SyntaxError
	```

严格模式 delete 不可配置的属性报错

	```js
	!function(a){
	    var obj = {};
	    Object.defineProperty(obj,'a',{configurable:false});
	    console.log(delete obj.a);
	}(1);//删除失败，返回 false，输出 false
	!function(a){
	    'use strict';
	    var obj = {};
	    Object.defineProperty(obj,'a',{configurable:false});
	    delete obj.a;
	}(1);//报错 TypeError
	```
严格模式 对象字面量重复属性名报错

	```js
	!function(){
	    var obj = {x:1,x:2};//同名以最后一个为准
	    console.log(obj.x);
	}();//一般情况下 输出1
	!function(){
	    'use strict';
	    var obj = {x:1,x:2};
	    console.log(obj.x);
	}();//严格模式 报错 SyntaxError
	```

严格模式 禁止八进制字面量

	```js
	!function(){
	    console.log(0123);
	}();//一般情况 83
	!function(){
	    'use strict';
	    console.log(0123);
	}();//严格模式，报错 SyntaxError
	```

严格模式 eval,arguments 变为关键字，不能作为变量、函数名

	```js
	!function(){
	    function eval(){}
	    console.log(eval);
	}();//一般情况，输出 function eval(){}
	!function(){
	    'use strict';
	    function eval(){}
	    console.log(eval);
	}();//严格模式，报错 SyntaxError
	```

严格模式 eval 变为独立作用域

	```js
	!function(){
	    eval('var evalVal = 2');
	    console.log(typeof evalVal);
	}();//一般情况下 eval 所在的函数可以拿到evalVal ，输出 number
	!function(){
	    'use strict'
	    eval('var evalVal = 2');
	    console.log(typeof evalVal);
	}();//严格模式  eval 所在的函数拿不到 evalVal，输出 undefined
	```

严格模式下

一般函数调用时(不是对象的方法调用，也不使用 apply/call/bind 等修改 this) this 指向 null,而不是全局变量

若使用 apply/call，传入 null 或 undefined 时，this 将指向 null 或 undefined,而不是全局变量
试图修改不可写属性(weitable = false),在不可扩展的对象上添加属性时报错 TypeError,而不是忽略。
arguments.caller,argument.callee 被禁用

## 对象

对象中包含一系列属性，这些属性是无序的。每个属性都有一个字符串 key 和对应的 value。

	```js
	var obj = {x:1,y:2};
	obj.x;//1
	obj.y;//2
	```
**对象的key**

	```js
	var obj = {};
	obj[1]=1;
	obj['1']=2;
	obj;//Object{1:2}
	
	obj[{}]=true;
	obj[{x:1}]=true;
	obj;//Object{1:2,[object Object]:true}
	
	//对象的属性可以动态的创建或删除
	var obj = {};
	obj.y = 2;
	obj.x = 1;
	//属性的标签：writable\enumerable\configurable\value\get/set
	//对象的原型 [[proto]]
	function foo(){}
	foo.prototype.z = 3;
	var obj = new foo();
	//对象标签[[class]] 表示对象属于哪一个种类的
	//对象标签[[extensible]] 表示对象是否允许增加新的属性
	```

**对象创建、原型链**

字面量
	```js
	var obj1 = {x:1,y:2};
	var obj2 = {
	    x:1,
	    s:2,
	    o:{
	        z:3,
	        n:4
	    }
	}
	```

new / 原型链

	```js
	function foo(){}
	foo.protopyte.z = 3;
	var obj = new foo();
	obj.x = 1;
	obj.y = 1;
	obj.x;//1
	obj.y;//2
	obj.z;//3 沿着原型链向上查找
	//[[poto]] -> foo.prototype [[poto]] -> Object.prototype -> null
	typeof obj.toString;//'function'
	'z' in obj;//true
	obj.hasOwnPrototy('z');//false
	obj.z = 5;//不会沿着原型链向上查找改变 z 值，仅在对象上添加属性 z = 5
	obj.hasOwnPrototy('z');//true
	foo.prototype.z;//still 3
	obj.z;//5
	obj.z=undefined;//赋值为 undefined
	obj.z;//undefined
	delete obj.z;//true
	obj.z;//still 3!!!
	```

Object.create

	```js
	var obj = Object.create({x:1});
	obj.x;//1
	typeof obj.toString//"function"
	obj.hasOwnProtoperty('x');//false
	// 原型链上创建对象，指向  Object.prototype 
	//obj -> {x:1} -> Object.prototype -> null
	
	var obj = Object.create(null);
	obj.toString;//undefined
	//obj -> null
	```

**属性操作**

读写对象属性

	```js
	var obj = {x:1,y:2};
	obj.x;//1
	obj['y'];//2
	obj['x']=3;
	obj.y=4;
	var obj = {x1:1,x2:2};
	var i = 1,n = 2;
	for(;i<=n;i++){
	    console.log(obj['x'+i);
	}
	//输出：1,2
	var p;
	for(p in obj){//顺序不确定
	    console.log(obj[p]);
	}
	```

属性异常

	```js
	var obj ={x:1};
	obj.y;//undefined
	var yz = obj.y.z;//TypeError: Cannot read property 'z' of undefined
	obj.y.z = 2;     //TypeError: Cannot read property 'z' of undefined
	
	var yz;
	if(obj.y){              var yz = obj && obj.y && obj.y.z;
	    yz = obj.y.z;
	}
	```

删除属性

	```js
	var person = {age : 28,title : 'fe'};
	delete person.age;//true
	delete person['title'];//true
	person.age;//undefined
	delete person.age;//true 表示已经不存在该属性了
	
	delete Object.prototype;//false 不允许删除
	
	var descriptor = Object.getOwnPropertyDescriptor(Object,'prototype');
	descriptor.configurable;//false  所以 Object.prototype 不能被删除
	//全局变量、函数不能被删除
	val globalVal = 1;
	delete globalVal;//false
	function fd(){}
	delete fd;//false
	//局部变量、函数不能被删除
	(function(){
	    var localVal=1;
	    return delete localVal;
	}());//false
	(function(){
	    function fd(){};
	    return delete fd;
	}());//false
	//隐式创建全局变量，可以被删除
	ohNo=1；
	window.ohNo;//1
	delete ohNo;//true
	```

检测属性

	```js
	var cat = new Object();
	cat.legs = 4;
	cat.name = 'Kitty';
	'legs' in cat;//true
	'abc' in cat;//false
	//原型链上的属性
	'toString' in cat;//true,inherited property!!!
	//检测对象上是否具有属性
	cat.hasOwnProperty('legs');//true
	cat.hasOwnProperty('toString');//false
	//检测属性是否可以枚举，原型链上的属性 Enumerable 一般为 false
	cat.propertyIsEnumerable('legs');//true
	cat.propertyIsEnumerable('toString');//false
	//自定义属性的 Enumerable 
	Object.defineProperty(cat,'price',{enumerable:false,value:1000});
	cat.propertyIsEnumerable('price');//false
	cat.hasOwnProperty('price');//true
	if(cat&&cat.legs){
	cat.legs*=2;
	}
	if(cat.legs!=undefined){
	//!==undefined,or,!==null
	}
	if(cat.legs!==undefined){
	//only if cat.legs is not undefined
	}
	```

属性枚举

	```js
	var o = {x:1,y:2,z:3}
	'toString' in o;//true
	o.propertyIsEnumerable('toString');//false
	var key;
	for(key in o){
	    console.log(key);//x,y,z|toString 不会被枚举出来
	}
	var obj = Object.create(o);
	obj.a = 4;
	var key;
	for(key in obj){
	    console.log(key);//a,x,y,z
	}
	var obj = Object.create(o);
	    obj.a = 4;
	    var key;
	    for(key in obj){
	        //过滤掉原型链上的属性
	        if(obj.hasOwnProperty(key)){
	            console.log(key);//a
	        }
	}
	```

get/set 方法

	```js
	var man = {
	    name:'Bosn',
	    weibo:'@Bosn',
	    get age(){
	        return new Date().getFullYear()-1988;
	    }
	    set age(val){
	        console.log('Age can\'t be set to' + val);
	    }
	}
	console.log(man.age);//27
	man.age = 100;//Age can't be set to 100
	console.log(man.age);//still 27
	
	var man = {
	    weibo:'@Bosn',
	    $age : null,
	    get age(){
	        if(this.$age == undefined){
	            return new Date().getFullYear - 1988;
	        }else{
	            return this.$age;
	        }
	    }
	    set age(val){
	        val = +val;//转成 number
	        if(!isNaN(val)&&val>0&&val<150){
	            this.$age = +val;
	        }else{
	            throw new Error('Incorrect val = '+ val);
	        }
	    }
	}
	console.log(man.age);//27
	man.age = 100;
	console.log(man.age);//100
	man.age = 'abc';//error:Incorrect val = NaN
	```

get/set 与原型链

	```js
	function foo(){
	    Object.defineProperty(foo.propertype,'z',{get :function(){return1;}});
	    var obj = new foo();
	    obj.z;//1
	    obj.z=10;//走原型上的 get/set 方法，不会给对象创建一个属性
	    obj.z;//1
	
	    Object.defineProperty(obj,'z',{value:100,configurable:true});
	    obj.z;//100;
	    delete obj.z;
	    obj.z;//back to 1
	}
	```

属性标签

属性级的权限设置
value -> 值，
writable -> 是否可修改，
enumerable -> 是否可枚举，影响 for in 中是否出现，
configurable -> 是否可以再被修改，是否可以被 delete

	```js
	Object.getOwnPropertyDescriptor({pro:true},'pro');
	//Object{value: true, writable: true, enumerable: true, configurable: true}
	Object.getOwnPropertyDescriptor({pro:true},'a');//undefined
	
	var person = {};
	Object.defineProperty(person,'name',{
	    configurable: false//!!!
	    writable: false,//!!!
	    enumerable: true,
	    value: "Bosn Ma"
	});
	person.name;//Bosn Ma
	person.1;//
	person.name;//still Bosn Ma
	delete person.name;//false
	//再创建一个属性
	Object.defineProperty(person,'type',{
	    configurable:true,
	    writable:true,
	    enumerable:false,//!!!
	    value:"Object"
	});
	Object.keys();//["name"]
	//for in 也不存在
	//定义多个标签,不写的默认 false
	Object.defineProperties(person,{
	title:{value:'fe',enumerable:true},
	crop:{value:'XAXA',enumerable:true},
	salary:{value:5000,enumerable:true,writable:true},
	});
	Object.getOwnPropertyDescriptor(person,'salary');
	//Object {value: 5000, writable: true, enumerable: true, configurable: false}
	Object.getOwnPropertyDescriptor(person,'crop');
	//Object {value: "XAXA", writable: false, enumerable: true, configurable: false}
	
	 Object.defineProperties(person,{
	title:{value:'fe',enumerable:true},
	crop:{value:'XAXA',enumerable:true},
	salary:{value:5000,enumerable:true,writable:true},
	luck:{
	    get:function(){return Math.random()>0.5?'good':'bad';}
	},
	promote:{
	    set:function(level){ this.salary*=1+level*0.1;}
	}
	});
	Object.getOwnPropertyDescriptor(person,'salary');
	//Object {value: 5000, writable: true, enumerable: true, configurable: false}
	Object.getOwnPropertyDescriptor(person,'crop');
	//Object {value: "XAXA", writable: false, enumerable: true, configurable: false}
	person.salary;//5000
	person.promote = 2;
	person.salary;//6000
	/*
	            configurable:true  configurable:true  configurable:false  configurable:false
	            writable:true      writable:false     writable:false      writable:false
	
	修改属性的值      √                  √                 √                 ×
	                              重设 value 标签修改
	
	通过属性赋值      √                  ×                 √                 ×
	修改属性的值
	
	delete 该属性     √                  √                 ×                 ×
	返回 true
	
	修改 getter/      √                  √                 ×                 ×
	setter fangf
	
	修改属性标签*
	(除了 writable    √                  √                 ×                 ×
	从 true 改为 
	false 总是允
	许的)
	*/
	```

**对象、标签序列化**

	```js
	//原型标签 [[ptoto]] 指向对象的 Object.prototype 属性
	//[[class]] 表示对象的类型
	var toString = Object.prototype.toString;
	function getType(o){return toString.call(o).slice(8,-1);};
	toString.call(null);//"[object Null]"
	getType(null);//"Null"
	getType(undefined);//"Undefined"
	getType(1);//"Number"
	getType(new Number(1));//"Number"
	typeof new Number(1);//object
	getType(true);//"Boolean"
	getType(new Boolean(true));//"Boolean"
	//[[extensible]]
	var obj = {x:1,y:2};
	Object.isExtensible(obj);//true
	Object.preventExtensions(obj);
	Object.isExtensible(obj);//false
	obj.z=;
	obj.z;//undefined; add new property failed
	Object.getOwnPropertyDescriptor(obj,'x');
	//Object {value: 1, writable: true, enumerable: true, configurable: true}
	
	Object.seal(obj);
	Object.getOwnPropertyDescriptor(obj,'x');
	//{value: 1, writable: true, enumerable: true, configurable: false}
	
	Object.freeze(obj);
	Object.getOwnPropertyDescriptor(obj,'x');
	//{value: 1, writable: false, enumerable: true, configurable: false}
	//[causion] not affects prototype chain!!!不影响原型链
	```

序列化其他对象方法

	```js
	var obj = {x:1,y:true,z:[1,2,3],nullVal:null}; 
	JSON.stringify(obj)//"{"x":1,"y":true,"z":[1,2,3],"nullVal":null}"
	//值为 undefined 时不会出现在 序列化结果中。NaN -> null 。 Infiniti -> null。时间 -> UTC
	obj = {val:undefined,a:NaN,b:Infinity,c:new Date()};
	JSON.stringify(obj);//"{"a":null,"b":null,"c":"2017-10-19T05:21:24.228Z"}"
	obj = JSON.parse('{"x":1}');
	obj.x;//1
	```

自定义序列化

	```js
	var obj = {
	    x:1,
	    y:2,
	    o:{
	        o1:1,
	        o2:2,
	        toJSON:function(){return this.o1+this.o2;}
	    }
	};
	JSON.stringify(obj);
	//"{"x":1,"y":2,"o":3}"
	```

**其它对象方法**

	```js
	var obj = {x:1,y:2};
	obj.toString();//"[object Object]"
	obj.toString = function(){return this.x+this.y};
	"Result" + obj;//"Result 3",by toString
	
	+obj;//3,from toString
	//强制转化为基本类型时调用 valueOf
	obj.valueOf = function(){return this.x+this.y + 100;};
	+ obj;//103,from valueOf
	
	"Resault"+obj still "Resault 103"
	```

## 数组

数组是值得有序集合，每个值叫做元素，每个元素在数组中都有数组位置编号，也就是索引。

JS 数组是弱类型的，数组中可以含有不同类型的元素。数组元素甚至可以是对象或其它数组

	```js
	var arr = [1,true,null,undefined,{x:1},[1,2,3]];
	//      arr[0]    arr[2]       arr[4].x    arr[5][2]
	```

**数组创建**

	```js
	//字面量
	var BAT = ['Alibaba','Tencent','Baidu'];
	var students = [{name:'XA',age:22},name:'XB',age:20];
	var arr = ['Nunnly','is','big','keng','B',123,true,null];
	var arrInArr = [[1,2],[3,4,5];
	var commasArr1 = [1,,2];//1,undefined,2
	var commasArr2 = [,,];//undefined,undefined 数组允许最后一个 ,
	//数组大小
	//size from 0 to 4294967295(2^23-1) 超过报错 RangeError
	//new Array()
	var arr = new Array();//new 可以省略
	var arrWithLength = new Array(100);//undefined * 100
	var arrlikeLiteral = new Array(true,false,null,1,2,'hi');
	//等价于 [true, false, null, 1, 2, 'hi'];
	```

**数组操作**

	```js
	//数组元素读写
	var arr = [1,2,3,4,5];
	arr[1];//2
	arr.length;//5
	arr[5]=6;
	arr.length;//6
	delete arr[0];
	arr[0];//undefined
	//数组元素增删， 动态的， 无需指定大小
	arr arr = [];
	arr[0] = 1;
	arr[1] = 2;
	arr.push(3);
	arr;//[1,2,3];
	arr[arr.length] = 4;//equal to arr.push(4);
	arr;//[1,2,3,4]
	arr.unshift(0);
	arr;//[0,1,2,3,4];
	delete arr[2];
	arr;//[0,1,undefined,3,4]
	arr.length -= 1;
	arr;//[0,1,undefined,3], 4 is removed
	arr;//[0,1,undefined],3 is removed
	arr.shift();//0 returned by shift
	arr;//[1,undefined]
	//数组迭代
	var i = 0,n = 10;
	var arr = [1,2,3,4,5];
	for(;i<n;i++){
	    console.log(arr[i]);//1,2,3,4,5
	}
	for(i in arr){
	    console.log(arr[i]);//1,2,3,4,5
	}
	Array.prototype.x = 'inherited';
	for(i in arr){
	    console.log(arr[i]);//1,2,3,4,5,inherited
	}
	for(i in arr){
	    if(arr.hasOwnProperty[i]){
	        console.log(arr[i]);//1,2,3,4,5        
	    }
	}//!!! for in 是不保证顺序的！！！
	```

**二维数组**

	```js
	var arr = [[0,1],[2,3],[4,5]];  //result:
	var i = 0,j = 0;                //row 0
	var row;                        //0
	for(;i<arr.length;i++){         //1
	    row = arr[i];               //row 1
	    console.log('row '+i);      //2
	    for(j=0;j<row.length;j++){  //3
	        console.log(row[j]);    //row 2
	    }                           //4
	}                               //5
	```

**稀疏数组**

稀疏数组并不含有从 0 开始的连续索引。一般 length 属性值比实际元素个数大。

	```js
	var arr1 = [undefined];
	var arr2 = new Array(1);
	0 in arr1;//true
	0 in arr2;//false
	arr1.length = 100;
	arr1[99] = 123;
	99 in arr1;// true
	98 in arr1;// false
	var arr =[,,];
	0 in arr;// false
	```

**数组方法**

```
{} => Object.prototype
[] => Array.prototype
Array.prototype.join
Array.prototype.reverse
Array.prototype.sort
Array.prototype.concat
Array.prototype.slice//切片
Array.prototype.splice//胶接
Array.prototype.forEach (ES5）
Array.prototype.map     (ES5）
Array.prototype.filter  (ES5）
Array.prototype.every   (ES5）
Array.prototype.some    (ES5）
Array.prototype.reduce/reduceRight (ES5）
Array.prototype.indexOf/lastindexOf (ES5）
Array.isArray (ES5）
```

Array.prototype.join 将数组转为字符串

	```js
	var arr = [1,2,3];
	arr.join();//"1,2,3"
	arr.join("_");//"1_2_3"
	function repeatString(str,n){
	    return new Array(n+1).join(str);
	}
	repeatString("a",3);//"aaa"
	repeatString("Hi",5);//"HiHiHiHiHi"
	```

Array.prototype.reverse 将数组逆序

	```js
	var arr = [1,2,3];
	arr.reserve();//[3,2,1]
	arr;//[3,2,1] 原数组被修改
	```

Array.prototype.sort 排序

	```js
	var arr = ["a","d","c","b"];
	arr.sort();//["a","b","c","d"]
	arr = [13,24,51,3];
	arr.sort();//[13,24,3,51]
	arr;//[13,24,3,51] 默认按字母顺序排序，原数组被修改
	arr.sort(function(a,b){
	    return a - b;
	});
	arr;//3,13,24,51
	arr = [{age:25},{age:39},{age:49}];
	arr.sort(function(a,b){
	    return a.age - b.age;
	});
	arr.forEach(function(item){
	    console.log("age",item.age);
	});
	//age 25
	//age 39
	//age 49
	```

Array.prototype.concat 数组合并

	```js
	var arr = [1,2,3];
	arr.concat(4,5);//[1,2,3,4,5]
	arr;//[1,2,3]//原数组未被修改
	arr.concat([10,11],13);//[1,2,3,10,11,13]
	arr.concat(1,[2,3]);//[1,2,3,1,[2,3]]
	```

Array.prototype.slice 返回部分数组

	```js
	var arr = [1,2,3,4,5];
	arr.slice(1,3);//[2,3]
	arr.slice(1);//[2,3,4,5]
	arr.slice(1,-1);//[2,3,4]//-1 表示倒数第一个元素
	arr.slice(-4,-3);//[2]
	arr;[1,2,3,4,5];//原数组中元素不被修改
	```

Array.prototype.splice 数组拼接

	```js
	var arr = [1,2,3,4,5];
	arr.splice(2);//returns [3,4,5],把从 3 开始的元素作为返回值并删除掉
	arr;//[1,2];//原数组被修改
	var arr = [1,2,3,4,5];
	arr.splice(2,2);//returns [3,4],把从 2 开始的 2 个元素作为返回值并删除掉
	arr;//[1,2,5];//原数组被修改
	var arr = [1,2,3,4,5];
	arr.splice(1,1,'a','b');//returns [2],把从 1 开始的 1 个元素作为返回值并删除掉，在删除的位置插入 'a','b'
	arr;//[1,2];//原数组被修改
	```

Array.prototype.forEach 数组遍历 [ES5 ie9 +]

	```js
	var arr = [1,2,3,4,5];
	arr.forEach(function(x,index,a){
	    console.log(x+'|'+index+'|'+(a===arr));
	});
	//1|0|true
	//2|1|true
	//3|2|true
	//4|3|true
	//5|4|true
	```

Array.prototype.map 数组映射 [ES5 ie9 +]

	```js
	var arr = [1,2,3];
	arr.map(function(x){
	    return x+10;
	});//[11,12,13]
	arr;//[1,2,3] 原数组未被修改
	```

Array.prototype.filter 数组过滤 [ES5 ie9 +]

	```js
	var arr = [1,2,3,4,5,6,7,8,9,10];
	arr.filter(function(x,index){
	    return index % 3 === 0 || x >= 8;
	});//return [1,4,7,8,9,10]
	arr;//[1,2,3,4,5,6,7,8,9,10] 原数组未被修改
	```

Array.prototype.every & some 数组判断 [ES5 ie9 +]

	```js
	var arr = [1,2,3,4,5];
	//every 每一个元素都符合条件
	arr.every(function(x){
	    return x<10;
	});//true
	arr.every(function(x){
	    return x<3;
	});//false
	var arr = [1,2,3,4,5];
	//every 只要一个元素都符合条件
	arr.some(function(x){
	    return x === 3;
	});//true
	arr.some(function(x){
	    return x === 100;
	});//false
	```

Array.prototype.reduce & reduceRight 数组元素两两操作 [ES5 ie9 +]

	```js
	//reduce
	var arr = [1,2,3];
	var sum = arr.reduce(function(x,y){
	    return x+y
	},0);// 0 表示操作的第一个元素，不传就指向数组的第一个元素，第一次的返回值作为第二次的 x
	sum;//6 
	arr;//[1,2,3] 原数组不被修改
	arr;//[3,9,6]
	var max = arr.reduce(function(x,y){
	    console.log(x + '|' + y);
	    return x > y ? x : y;
	});//6
	arr;//[1,2,3]
	//3:9
	//9:6
	max;//9
	//reduceRight
	arr;//[3,9,6]
	var max = arr.reduce(function(x,y){
	    console.log(x + '|' + y);
	    return x > y ? x : y;
	});//6
	arr;//[1,2,3]
	//9:6
	//9:3
	max;//9
	```

Array.prototype.indexOf & lastIndexOf 数组检索 [ES5 ie9 +]

	```js
	var arr = [1,2,3,2,1];
	arr.indexOf(2);//1
	arr.indexOf(99);//-1 //找不到就返回 -1
	arr.indexOf(2,1);//3 从第 1 个元素开始找 2
	arr.indexOf(1,-3);//4 从倒数第 3 个元素开始找 1
	arr.indexOf(2,-1);//-1
	arr.LastIndexOf(2);//3
	arr.LastIndexOf(2,-2);//3
	arr.LastIndexOf(2,-3);//1
	```

Array.isArray 判断是否为数组 [ES5 ie9 +]


	```js
	Array.isArray([]);//true
	
	[]instanceof Array;//true
	({}).toString.apply([])=== [object Array];//true
	[].constructor === Array;//true 
	```

**数组和一般对象的比较**


|  相同        | 不同           |
| :------------- |:-------------|
|都可以继承|数组自动更新 length|
|数组是对象，对象不一定是数组|按索引访问数组常常比访问一般对象属性明显迅速|
|都可以当作对象添加或删除属性|数组对象继承 Array.prototype 上的大量数组操作方法|

**数组和字符串**

	```js
	var str = "hello world";
	str.charAt(0);//"h"
	str[1];//e
	
	Array.prototype.join.call(str,"_");
	//"h_e_l_l_o_ _w_o_r_l_d"
	```

