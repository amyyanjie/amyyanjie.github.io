---
title: JavaScript学习笔记
english_title: JavaScript-study-notes
date: 2018-01-10 00:10:04
tags: JavaScript 
categories: 前端
---

HTML(Hyper Text Markup Language)，即超文本标记语言，定义了网页的内容；   

CSS(Cascading Style Sheet)，即层叠样式表，描述了网页的布局；  

JavaScript网页的行为；

DOM(Document Object Model)文档对象模型，是用于访问HTML元素的正式W3C标准。

----
如果把值赋给尚未声明的变量，该变量将被自动作为全局变量声明，即使是在函数内执行。

在HTML中，全局变量是window对象，所有数据变量都属于window对象。

----
===为绝对相等，即数据类型与值都必须相等。

!==不绝对等于（值和类型有一个不相等，或两个都不相等）
x+=y等同于x=x+y,类推，x%=y等同于x=x%y。

字符串和数字相加，数字转成字符串。

数字和布尔值相加，布尔值false转成0，true转成1

字符串和布尔值相加，布尔值转化成字符串

数字与null（空值）相加，null转成数字0

字符串与null（空值）相加，null转为字符串


----
条件运算符：

voteable=(age<18)?"年龄太小"：“年龄已达到”；

----
switch语句

swirch语句会使用恒等计算符(===)进行比较
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>菜鸟教程</title>
</head>
<body>
<p>点击下面的按钮来显示今天是周几：</p>
<button onclick="myfunction()">点击这里</button>
<p id="demo"></p>

<script>
    function myfunction() {
        var x;
        var d = new Date().getDate();
        switch (d) {
            case 6:
                x = "今天是周六";
            case 0:
                x = "今天是周日";
            default:
                x = "期待周末";
        }
        document.getElementById("demo").innerHTML = x;

    }
</script>
</body>
</html>
```

---------
do/while循环，该循环至少会执行一次，即使条件为false，因为代码块会在条件被测试前执行。

continue语句中断循环中的迭代，如果出现了指定的条件，继续循环中的下一个迭代。即跳过循环中的一个迭代。

通过标签引用，break语句可以跳出任何JavaScript代码块。不带标签引用break语句只能用在循环或switch中。continue语句只能用在循环中。

-------

---
#### 
- 5种不同的数据类型：string、number、boolean、object、function

- 3种对象类型：Object、Date、Array

- 2个不包含任何值的数据类型：null、undefined

- typeof一般只能返回如下几个结果：number，boolean,string,function,object(NULL，数组，对象),undefined.
 
   可以使用if(typeof a!="undefined"){}获取一个变量是否存在

- 可通过instance来判断对象的具体类型。（因为typeof遇到null,数组，对象时都返回object类型）。如if(arr instanceof Array).

数组是一种特殊的对象类型，用typeof返回object.

null是一个只有一个值的特殊类型，表示一个空对象引用。用typeof检测null返回是object。

undefined是一个没有设置值的变量，typeof返回undefined。

null与undefined的值相等，类型不等。

typeof NaN  //返回number   
typeof Array //返回object   
typeof Date   //返回object

JavaScript只有一种数字类型。对象和函数也是变量。

----
#### 类型转换

- 将数字、布尔值、日期转换为字符串：全局方法String();或者toString()方法。

- 全局方法Number()可将字符串、布尔值、日期转换为数字。

   空字符串转换为0；其他的字符串（不含数字）会转换为NaN(不是个数字)。
   
   字符串parseInt()、日期getTime()都可以转换为数字。
   
- 一元运算符(+)可用于将变量转换为数字。  如果变量不能转换，它仍然会是一个数字，但值为NaN（不是一个数字）。 

- "5"+1  //返回"51"   
  "5"-1  //返回4

----    
#### 正则表达式（Regular Exception),regex、regexp、RE。

- 在JavaScript中，正则表达式通常用于两个字符串方法：search()和replace()。

- test()，如果字符串含有匹配的文本，返回true,否则false。

- exec()。该函数返回一个数组，存放匹配的结果。如果未找到匹配，返回值为null。

-----
```
var x = 0.1;
var y = 0.2;
var z = x + y; //z的结果为0.30000000000000004
if(z == 0.3)  //返回false
var z = (x * 10 + y * 10) /10;//z的结果为0.3
```
因为JavaScript中的所有数据都是以浮点型数据(float)来存储,对浮点型的精确度难确定。

----
#### javascript:void(0)含义
javascript:void(0)仅仅表示一个死链接。void(0)计算为0，但javascript上没有任何效果。void仅仅是表示不返回任何值，但是括号内的表达式还是要运行。

----
#### 对象定义的规则
 冒号与属性值间有个空格；字符串使用双引号（数字不需要）；最后一个属性-值对后面不要加分号；右花括号独立一行，分号结束。短的对象代码直接写一行，每行代码字符小于80个。


----
## JSON
### 简介
JSON(Java Script Object Notation)，即JavaScript对象表示法，是用于存储和传输数据的格式，通常用于服务端向网页传递数据。

JSON是一种轻量级的数据交换格式，但JSON格式仅仅是一个文本，文本可以被任何编程语言读取及作为数据格式传递。


通常我们从服务器中读取JSON数据，并在网页中显示数据。
- JSON.parse(),用于将一个JSON字符串转换为为JavaScript对象。

- JSON.stringify(),用于将JavaScript值转换为JSON字符串。

- JSON文本格式在语法上与创建JavaScript对象的代码相同。所以，无需解析器，JavaScript程序能够使用內建的eval()函数，用JSON数据类生成原生的JavaScript对象。
```
//创建包含JSON语法的JavaScript字符串
var txt = '{"sites" : ['+
'{"name':"谷歌","url":"www.google.com"},'+
'{"name":"微博","url":"www.weibo.com"}]}'

var obj = eval("(" + txt + ")");

```

   eval(String)函数可计算某个字符串，并执行其中的JavaScript代码。但存在安全问题
   
 
对于AJAX应用程序来说，JSON比XML更快更易使用：

- 使用XML:读取XML文档；使用XML DOM来循环遍历文档；读取值并存储在变量中。

- 使用JSON：读取JSON字符串；用eval()处理JSON字符串。

### JSON语法
JSON使用JavaScript语法。
- JSON数据格式为键/值对，就像JavaScript对象属性，键/值对包括字段名称（双引号中）、冒号、值。数据由逗号分隔。
  
   JavaScript语句键值对中用的=，JSON中(name: "教程")与JavaScript中(name= "教程")是等价的。
- JSON对象保存在大括号内，对象可以保存多个键值对。
- JSON数组保存在中括号内，数组可以包含对象。

### JSON对象
JSON对象和JSON字符串的区别：
```
//JSON对象
var obj = {"name": "amy","sex": "female"};
//JSON字符串
var str='{"name": "amy","sex": "female"}';
```
对象属性的值可以是一个数组，数组中可以包含另一个数组，或者是另一个JSON对象。

- 访问、修改对象值：可用点号(.)；也可用中括号[]来访问或修改。
 
   可用delete关键字来删除对象的属性。

- 循环对象：可用for-in循环对象的属性，用[]来访问对象的值。

- 嵌套JSON对象：可以使用(.)或([])来访问嵌套的JSON对象。

### JSON.parse()
在接收服务器数据时一般是字符串，用JSON.parse()方法吧数据转换为JavaScript对象。

JSON.parse(text[,reviver])。text:有效的字符串。reviver:可选，一个转换结果的函数，将为对象的每个成员调用此函数，如JSON.parse(text,function(key,value){})。

- JSON不能存储Date对象，可将其转换为字符串。解析后之后再将字符串转换为Date对象。

- JSON不允许包含函数。可以将函数作为字符串存储，解析后再将字符串转换为函数。

### JSON.stringify()
在向服务器发送数据时一般是字符串，用JSON.stringify()方法将JavaScript对象（含数组）转换为字符串。

JOSN.stringify(value[,repalcer[, space]])。value:有效的JSON字符串。replacer：可选，用于转换结果的函数或数组。space：可选，文本添加缩进、空格和换行符。

- JSON不能存储Date对象，JSON.stringify()会将所有日期转换为字符串。

- JSON不允许包含函数，JSON.stringify()会删除JavaScript对象的函数，包括key和value。可以在执行JSON.stringify()之前转换为字符串tostring()来避免。

不建议在JSON中使用函数。

