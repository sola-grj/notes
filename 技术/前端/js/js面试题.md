## 1.延迟加载JS有哪些方式？

延迟加载：async、defer
```html
<script defer type="text/javascript" src='script.js'></script>
<script async type="text/javascript" src='script.js'></script>
```

​		
defer : 等html全部解析完成，才会执行js代码，顺次执行js脚本。
async : async是和html解析同步的（一起的），不是顺次执行js脚本（谁先加载完谁先执行）。

## 2.js数据类型有哪些？

基本类型：string、number、boolean、undefined、null、symbol、bigInt

引用类型：object

```js
console.log( true + 1 );     			//2
console.log( 'name'+true );  			//nametrue
console.log( undefined + 1 ); 		//NaN
console.log( typeof undefined ); //undefined
console.log( typeof(NaN) );       //number
console.log( typeof(null) );      //object
```

## 3.null和undefined的区别

1. 作者在设计js的都是先设计的null（为什么设计了null：最初设计js的时候借鉴了java的语言）
2. null会被隐式转换成0，很不容易发现错误。
3. 先有null后有undefined，出来undefined是为了填补之前的坑。

具体区别：JavaScript的最初版本是这样区分的：null是一个表示"无"的对象（空对象指针），转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN。

## 4.== 和 === 有什么不同？

==  :  比较的是值

```js
// string == number || boolean || number ....都会隐式转换
// 通过valueOf转换（valueOf() 方法通常由 JavaScript 在后台自动调用，并不显式地出现在代码中。）
console.log(1 == '1') // true
console.log(true == 1) // true
console.log(null == undefined) // true
console.log([1,2] == '1,2') // true
```

=== ： 除了比较值，还比较类型

## 5.js微任务与宏任务

1. js是单线程的语言。
2. js代码执行流程：同步执行完==》事件循环
	同步的任务都执行完了，才会执行事件循环的内容
	进入事件循环：请求、定时器、事件....
3. 事件循环中包含：【微任务、宏任务】
微任务：promise.then
宏任务：setTimeout..

要执行宏任务的前提是清空了所有的微任务

流程：同步==》事件循环【微任务和宏任务】==》微任务==》宏任务=》微任务...

```js
setTimeout(() => {
  console.log('1') // 宏任务
})

new Promise((resolve) => {
  console.log('1 promise 1') // 同步任务
}).then(() => {
  console.log('微1') // 微任务
}).then(() => {
  console.log('微2') // 微任务
})

console.log(2) // 同步任务
```

## 6.js作用域考题

1. 除了函数外，js是没有块级作用域。
2. 作用域链：内部可以访问外部的变量，但是外部不能访问内部的变量。
	 注意：如果内部有，优先查找到内部，如果内部没有就查找外部的。
3. 注意声明变量是用var还是没有写（window.）
4. 注意：js有变量提升的机制【变量悬挂声明】
5. 优先级：声明变量 > 声明普通函数 > 参数 > 变量提升

面试的时候怎么看：

```
1. 本层作用域有没有此变量【注意变量提升】
2. 注意：js除了函数外没有块级作用域
3. 普通声明函数是不看写函数的时候顺序
```

##### 考题一：

```
function c(){
	var b = 1;
	function a(){
		console.log( b );
		var b = 2;
		console.log( b );
	}
	a();
	console.log( b );
}
c();
```

##### 考题二：

```
var name = 'a';
(function(){
	if( typeof name == 'undefined' ){
		var name = 'b';
		console.log('111'+name);
	}else{
		console.log('222'+name);
	}
})()
```

##### 考题三：

```
function fun( a ){
	var a = 10;
	function a(){}
	console.log( a );
}
fun( 100 );
```

## 7.JS对象考题

1. 对象是通过new操作符构建出来的，所以对象之间不想等(除了引用外)；
2. 对象注意：引用类型(共同一个地址)；
3. 对象的key都是字符串类型；
4. 对象如何找属性|方法；
	查找规则：先在对象本身找 ===> 构造函数中找 ===> 对象原型中找 ===> 构造函数原型中找 ===> 对象上一层原型查找

##### 考题一：

```
 [1,2,3] === [1,2,3]   //false
```

##### 考题二：

```
var obj1 = {
	a:'hellow'
}
var obj2 = obj1;
obj2.a = 'world';
console.log(obj1); 	//{a:world}
(function(){
	console.log(a); 	//undefined
	var a = 1;
})();
```

##### 考题三：

```
var a = {}
var b = {
	key:'a'
}
var c = {
	key:'c'
}

a[b] = '123';
a[c] = '456';

console.log( a[b] ); // 456
```

JS对象注意点：

```
1. 对象是通过new操作符构建出来的，所以对象之间不想等(除了引用外)；
2. 对象注意：引用类型(共同一个地址)；
3. 对象的key都是字符串类型；
4. 对象如何找属性|方法；
	查找规则：先在对象本身找 ===> 构造函数中找 ===> 对象原型中找 ===> 构造函数原型中找 ===> 对象上一层原型查找
```

##### 考题一：

```
 [1,2,3] === [1,2,3]   //false
```

##### 考题二：

```
var obj1 = {
	a:'hellow'
}
var obj2 = obj1;
obj2.a = 'world';
console.log(obj1); 	//{a:world}
(function(){
	console.log(a); 	//undefined
	var a = 1;
})();
```

##### 考题三：

```
var a = {}
var b = {
	key:'a'
}
var c = {
	key:'c'
}

a[b] = '123';
a[c] = '456';

console.log( a[b] ); // 456
```

## 8.JS作用域+this指向+原型的考题

##### 考题一：

```
function Foo(){
	getName = function(){console.log(1)} //注意是全局的window.
	return this;
}

Foo.getName = function(){console.log(2)}
Foo.prototype.getName = function(){console.log(3)}
var getName = function(){console.log(4)}
function getName(){
	console.log(5)
}

Foo.getName();    //2
getName(); 		  //4
Foo().getName();  //1
getName();		  //1
new Foo().getName();//3
```

##### 考题二：

```
var o = {
	a:10,
	b:{
		a:2,
		fn:function(){
			console.log( this.a ); // 2
			console.log( this );   //代表b对象
		}
	}
}
o.b.fn();
```

##### 考题三：

```
window.name = 'ByteDance';
function A(){
	this.name = 123;
}
A.prototype.getA = function(){
	console.log( this );
	return this.name + 1;
}
let a = new A();
let funcA = a.getA;
funcA();  //this代表window
```

##### 考题四：

```
var length = 10;
function fn(){
	return this.length + 1;
}
var obj = {
	length:5,
	test1:function(){
		return fn();
	}
}
obj.test2 = fn;
console.log( obj.test1() ); 							//1
console.log( fn()===obj.test2() ); 				//false
console.log( obj.test1() == obj.test2() ); //false
```

## 9.JS判断变量是不是数组，你能写出哪些方法？

##### 方式一：isArray

```
var arr = [1,2,3];
console.log( Array.isArray( arr ) );
```

##### 方式二：instanceof  【可写,可不写】

```
var arr = [1,2,3];
console.log( arr instanceof Array );
```

##### 方式三：原型prototype

```
var arr = [1,2,3];
console.log( Object.prototype.toString.call(arr).indexOf('Array') > -1 );
```

#### 方式四：isPrototypeOf()

```
var arr = [1,2,3];
console.log(  Array.prototype.isPrototypeOf(arr) )
```

#### 方式五：constructor

```
var arr = [1,2,3];
console.log(  arr.constructor.toString().indexOf('Array') > -1 )
```

## 10.slice是干嘛的、splice是否会改变原数组

```
1. slice是来截取的
	参数可以写slice(3)、slice(1,3)、slice(-3)
	返回的是一个新的数组
2. splice 功能有：插入、删除、替换
	返回：删除的元素
	该方法会改变原数组
```

## 11.JS数组去重

##### 方式一：new set

```
var arr1 = [1,2,3,2,4,1];
function unique(arr){
	return [...new Set(arr)]
}
console.log(  unique(arr1) );
```

##### 方式二：indexOf

```
var arr2 = [1,2,3,2,4,1];
function unique( arr ){
	var brr = [];
	for( var i=0;i<arr.length;i++){
		if(  brr.indexOf(arr[i]) == -1 ){
			brr.push( arr[i] );
		}
	}
	return brr;
}
console.log( unique(arr2) );
```

##### 方式三：sort

```
var arr3 = [1,2,3,2,4,1];
function unique( arr ){
	arr = arr.sort();
	var brr = [];
	for(var i=0;i<arr.length;i++){
		if( arr[i] !== arr[i-1]){
			brr.push( arr[i] );
		}
	}
	return brr;
}
console.log( unique(arr3) );
```

## 12.找出多维数组最大值

```
function fnArr(arr){
	var newArr = [];
	arr.forEach((item,index)=>{
		newArr.push( Math.max(...item)  )
	})
	return newArr;
}
console.log(fnArr([
	[4,5,1,3],
	[13,27,18,26],
	[32,35,37,39],
	[1000,1001,857,1]
]));
```

## 13.给字符串新增方法实现功能

给字符串对象定义一个addPrefix函数，当传入一个字符串str时，它会返回新的带有指定前缀的字符串，例如：

console.log( 'world'.addPrefix('hello') )  控制台会输出helloworld

```
解答：
String.prototype.addPrefix = function(str){
	return str  + this;
}
console.log( 'world'.addPrefix('hello') )
```

## 14.找出字符串出现最多次数的字符以及次数

```
var str = 'aaabbbbbccddddddddddx';
var obj = {};
for(var i=0;i<str.length;i++){
	var char = str.charAt(i);
	if( obj[char] ){
		obj[char]++;
	}else{
		obj[char] = 1;
	}
}
console.log( obj );
//统计出来最大值
var max = 0;
for( var key in obj ){
	if( max < obj[key] ){
		max = obj[key];
	}
}
//拿最大值去对比
for( var key in obj ){
	if( obj[key] == max ){
		console.log('最多的字符是'+key);
		console.log('出现的次数是'+max);
	}
}
```

## 15.new操作符具体做了什么

```
1. 创建了一个空的对象
2. 将空对象的原型，指向于构造函数的原型
3. 将空对象作为构造函数的上下文（改变this指向）
4. 对构造函数有返回值的处理判断
```

```
function Fun( age,name ){
	this.age = age;
	this.name = name;
}
function create( fn , ...args ){
	//1. 创建了一个空的对象
	var obj = {}; //var obj = Object.create({})
	//2. 将空对象的原型，指向于构造函数的原型
	Object.setPrototypeOf(obj,fn.prototype);
	//3. 将空对象作为构造函数的上下文（改变this指向）
	var result = fn.apply(obj,args);
	//4. 对构造函数有返回值的处理判断
	return result instanceof Object ? result : obj;
}
console.log( create(Fun,18,'张三')   )
```

## 16.闭包

```
1. 闭包是什么
	闭包是一个函数加上到创建函数的作用域的连接，闭包“关闭”了函数的自由变量。
2. 闭包可以解决什么问题【闭包的优点】
	2.1 内部函数可以访问到外部函数的局部变量
	2.2 闭包可以解决的问题
			var lis = document.getElementsByTagName('li');
      for(var i=0;i<lis.length;i++){
        (function(i){
          lis[i].onclick = function(){
            alert(i);
          }
        })(i)
      }
3. 闭包的缺点
	3.1 变量会驻留在内存中，造成内存损耗问题。
				解决：把闭包的函数设置为null
  3.2 内存泄漏【ie】 ==> 可说可不说，如果说一定要提到ie
```

## 17.原型链

```
1. 原型可以解决什么问题
	对象共享属性和共享方法
2. 谁有原型
函数拥有：prototype
对象拥有：__proto__
3. 对象查找属性或者方法的顺序
	先在对象本身查找 --> 构造函数中查找 --> 对象的原型 --> 构造函数的原型中 --> 当前原型的原型中查找
4. 原型链
	4.1 是什么？：就是把原型串联起来
	4.2 原型链的最顶端是null
```

