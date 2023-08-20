## 1.介绍一下CSS的盒子模型

CSS的盒子模型有哪些：标准盒子模型、IE盒子模型

CSS的盒子模型区别：

- 标准盒子模型：margin、border、padding、content
- IE盒子模型 ：margin、content（ border +  padding  + content ）

```css
* {
        margin: 0;
        padding: 0;
      }
ul {
  overflow: hidden;
  list-style: none;
}
.uls1 li {
  box-sizing: content-box; /*标准盒模型*/
  float: left;
  margin: 10px;
  padding: 50px;
  width: 100px;
  height: 100px;
  border: 5px solid #ccc;
}
.uls2 li {
  box-sizing: border-box; /*IE盒模型*/
  float: left;
  margin: 10px;
  padding: 50px;
  width: 100px;
  height: 100px;
  border: 5px solid #ccc;
}
```

盒子模型的宽度如何计算？

- offsetWidth = （内容宽度 + 内边距 + 边框），无外边距

  ```html
  <style>
    #div1 {
      width: 100px;
      padding: 10px;
      border: 1px solid #ccc;
      margin: 10px;
    }
  </style>
  <div id="div1"></div>
  
  答案是 122px：100 + 10 + 10 + 1 + 1
  如果让offsetWidth等于100px，该如何做？
  box-sizing: border-box; /* 让offsetWidth等于100px */
  ```

  

## 2.line-height与height的区别

line-height是每一行文字的高，如果文字换行则整个盒子高度会增大（行数*行高）。
height是一个死值，就是这个盒子的高度。

### line-height的继承问题

- 如果line-height为具体数值：30px，则直接继承该值
- 如果line-height为比例值：2/1.5，则继承该比例值
- 如果line-height为百分比：200%，则继承计算出来的值（考点）

## 3.CSS选择符有哪些？哪些属性可以继承？

CSS选择符

- 通配（*）
- id选择器 （#）
- 类选择器 （.）
- 标签选择器（div、p、h1...）
- 相邻选择器（+）
- 后代选择器（ul  li）
- 子元素选择器（ul > li）
- 属性选择器（a[href]）

CSS属性哪些可以继承：

- 文字系列：
  - font-size、color、line-height、text-align...
  - 不可继承属性：border、padding、margin...

## 4.CSS优先级算法如何计算？

优先级比较：!important > 内联样式 > id > class > 标签 > 通配

CSS权重计算：

1. 内联样式（style）  权重值:1000
2. id选择器  				 权重值:100
3. 类选择器 				  权重值:10
4. 标签&伪元素选择器   权重值:1
5. 通配、>、+         权重值:0

## 5.用CSS画一个三角形

使用border

```css
.div {
		width: 0;
		height: 0;

		border-left:100px solid transparent;
		border-right:100px solid transparent;
		border-top:100px solid transparent;
		border-bottom:100px solid #ccc;

}
```



## 6.一个盒子不给宽度和高度如何水平垂直居中？

##### 方式一：

```
<div class='container'>
	<div class='main'>main</div>
</div>

.container{
		display: flex;
		justify-content: center;
		align-items: center;
		width: 300px;
		height: 300px;
		border:5px solid #ccc;
}
.main{
		background: red;
}
```

##### 方式二：

```
<div class='container'>
	<div class='main'>main</div>
</div>

.container{
		position: relative;
		width: 300px;
		height: 300px;
		border:5px solid #ccc;
}
.main{
		position: absolute;
		left:50%;
		top:50%;
		background: red;
		transform: translate(-50%,-50%);
}
```

## 7.display有哪些值，有什么作用？

- none     			隐藏元素
- block    			把某某元素转换成块元素
- inline   			把某某元素转换成内联元素
- inline-block 	把某某元素转换成行内块元素

## 8.对BFC规范(块级格式化上下文：block formatting context)的理解？

BFC就是页面上一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。

如何触发BFC：

- float的值非none
- overflow的值非visible
- display的值为：inline-block、table-cell...
- position的值为:absoute、fixed

## 9.清除浮动有哪些方式

1. 触发BFC

2. 多创建一个盒子，添加样式：clear: both;

3. after方式

  ```css
  ul:after{
  		content: '';
  		display: block;
  		clear: both;
  }
  ```

  

## 10.在网页中的应该使用奇数还是偶数的字体？为什么呢？

偶数 : 让文字在浏览器上表现更好看。

另外说明：ui给前端一般设计图都是偶数的，这样不管是布局也好，转换px也好，方便一点。

## 11.position有哪些值？分别是根据什么定位的？

- static [默认]  没有定位
- fixed  固定定位，相对于浏览器窗口进行定位。
- relative  相对于自身定位，不脱离文档流。
- absolute	相对于第一个有relative的父元素，脱离文档流。

relative和absolute区别
1. relative不脱离文档流 、absolute脱离文档流
2. relative相对于自身 、 absolute相对于第一个有relative的父元素
3. relative如果有left、right、top、bottom ==》left、top
	 absolute如果有left、right、top、bottom ==》left、right、top、bottom

## 12.写一个左中右布局占满屏幕，其中左、右俩块固定宽200，中间自适应宽，要求先加载中间块，请写出结构及样式。

```html
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      ul {
        overflow: hidden;
        list-style: none;
      }
      .uls1 li {
        box-sizing: content-box; /*标准盒模型*/
        float: left;
        margin: 10px;
        padding: 50px;
        width: 100px;
        height: 100px;
        border: 5px solid #ccc;
      }
      .uls2 li {
        box-sizing: border-box; /*IE盒模型*/
        float: left;
        margin: 10px;
        padding: 50px;
        width: 100px;
        height: 100px;
        border: 5px solid #ccc;
      }
      .center {
        float: left;
        width: 100%;
        height: 100vh;
        background: pink;
        .main {
          padding: 0 200px;
        }
      }
      .left {
        float: left;
        width: 200px;
        height: 100vh;
        background: red;
        margin-left: -100%;
      }
      .right {
        float: left;
        width: 200px;
        height: 100vh;
        background: blue;
        margin-left: -200px;
      }
    </style>
  </head>
  <body>
    <div class="center">
      <div class="main">center</div>
    </div>
    <div class="left">left</div>
    <div class="right">right</div>
  </body>
</html>
```

居中对齐有哪些实现方式？

- 水平居中
  - inline元素：text-align:center
  - block元素：margin:auto
  - absolute元素：left:50% + margin-left 负值
- 垂直居中
  - inline元素：line-height的值等于height值
  - absolute元素：top:50% + margin-top负值
  - absolute元素：transform(-50%,-50%)
  - absoulte元素：top, left,bottom,right = 0 + margin:auto

## 13.什么是css reset

reset.css：是一个css文件，用来重置css样式的。
normalize.css ：为了增强跨浏览器渲染的一致性，一个CSS 重置样式库。

## 14.css sprite是什么,有什么优缺点

1. css sprite是什么，雪碧图
  - 把多个小图标合并成一张大图片。
2. 优缺点
  - 优点：减少了http请求的次数，提升了性能。
  - 缺点：维护比较差（例如图片位置进行修改或者内容宽高修改）

## 15.display: none;与visibility: hidden;的区别

1.占用位置的区别

- display: none; 				是不占用位置的
- visibility: hidden;   虽然隐藏了，但是占用位置

2.重绘和回流的问题

- visibility: hidden; 、 display: none;  产生重绘
- display: none;     还会产生一次回流

注意：产生回流一定会造成重绘，但是重绘不一定会造成回流。

- 产生回流的情况：改变元素的位置(left、top...)、显示隐藏元素....
- 产生重绘的情况：样式改变、换皮肤

## 16.opacity 和 rgba区别

共同性：实现透明效果

1. opacity 取值范围0到1之间，0表示完全透明，1表示不透明
2. rgba   R表示红色，G表示绿色，B表示蓝色，取值可以在正整数或者百分数。A表示透明度取值0到1之间

区别：继承的区别
opacity会继承父元素的opacity属性，而RGBA设置的元素的后代元素不会继承不透明属性。

## 17.margin纵向重叠的问题以及负值的问题

margin纵向重叠

- 相邻元素的margin-top和margin-bottom会发生重叠
- 空白内容的 p 标签也会重叠

```html
<style type="text/css">
  p {
    font-size: 16px;
    line-height: 1;
    margin-top: 10px;
    margin-bottom: 15px;
  }
</style>
<p>AAA</p>
    <p></p>
    <p></p>
    <p></p>
<p>BBB</p>
AAA与BBB之间的距离，由于margin纵向重叠的问题，只有AAA的margin-bottom的15px
```

margin负值

- margin-top和margin-left负值，元素向上，向左移动
- margin-right负值，右侧元素左移，自身不受影响
- margin-bottom负值，下方元素上移，自身不受影响

## 18.flex实现三点骰子

常用语法

- flex-direction 主轴方向
- justify-content 主轴对齐方式
- align-items 交叉轴对齐方式
- flex-wrap 是否换行
- align-self 子元素在交叉轴的对齐方式

```html
flex实现三点骰子
<style type="text/css">
  .box {
    width: 200px;
    height: 200px;
    border: 2px solid #ccc;
    border-radius: 10px;
    padding: 20px;

    display: flex;
    justify-content: space-between;
  }
  .item {
    display: block;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background-color: #666;
  }
  .item:nth-child(2) {
    align-self: center;
  }
  .item:nth-child(3) {
    align-self: flex-end;
  }

</style>

<div class="box">
  <span class="item"></span>
  <span class="item"></span>
  <span class="item"></span>
</div>
```

## 19.响应式

### 1.rem是什么

rem是一个长度单位

- px，绝对长度单位，最常用
- em，相对长度单位，相对于父元素，不常用
- rem，相对长度单位，相对于根元素，常用于响应式布局

### 2.如何实现响应式

响应式布局的常用方案

- media-query，根据不同的屏幕宽度设置根元素font-size

```css
<style type="text/css">
        @media only screen and (max-width: 374px) {
            /* iphone5 或者更小的尺寸，以 iphone5 的宽度（320px）比例设置 font-size */
            html {
                font-size: 86px;
            }
        }
        @media only screen and (min-width: 375px) and (max-width: 413px) {
            /* iphone6/7/8 和 iphone x */
            html {
                font-size: 100px;
            }
        }
        @media only screen and (min-width: 414px) {
            /* iphone6p 或者更大的尺寸，以 iphone6p 的宽度（414px）比例设置 font-size */
            html {
                font-size: 110px;
            }
        }

        body {
            font-size: 0.16rem;
        }
        #div1 {
            width: 1rem;
            background-color: #ccc;
        }

</style>
```

### 3.vw/vh

vh 网页视口高度的 1/100

vw 网页视口宽度的 1/100

vmax取两者最大值；vmin取两者最小值

rem的弊端

- 阶梯性导致只能是范围

网页视口尺寸

- window.screen.height 屏幕高度

- window.innerHeight 网页视口高度

- document.body.clientHeight body高度