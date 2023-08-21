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

​				