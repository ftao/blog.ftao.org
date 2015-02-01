---
title: 正确的给HTML元素添加事件处理
author: Filia.Tao
layout: post
permalink: /2006/05/06/right-way-to-add-event-handler-for-html-element/
categories:
  - javascript
---
在JavaScript怎样给HTML元素添加事件处理？这个问题虽然很简单，但有时还是会混淆起来。  
关键字：JavaScript HTML EventHandle  
1.直接书写在HTML代码里。  
<input id=”button1&#8243; type=”button” onclick=”do something here”>  
“do something here”,可以包含很多语句，比如说”alert (&#8216;botton is clicked‘);“,  
或者”check(this.form);return false;”。check 是已经定义的一个函数，this这里指的是这个button,this.form 就是包含这个按钮的表单。(关于this这个东西，比较复杂，得好好看看犀牛书）

2.在JavaScipt中给元素的onclick ,onselect,onfocus等属性赋值。  
the_button = document.getElementById(&#8216;button1&#8243;);  
the_button.onclick = fucntionname;  
这里我们不能用在HTML代码直接写的形式，我们给它赋的值是一个函数，不加引号，也不能加上括号。我们也可以使用匿名函数来执行多个语句。  
the\_button.onclick = function( ) { somefunciotn( ); do\_somthing\_else; another\_fucntion(); };  
3.使用事件监听。  
**obj.addEventListener(*****whichEvent, handler, direction)***(MOZ/N6+, DOM2)  
**obj.attachEvent(*****whichHandler*****, *****theFunction*****)** (IE5+)  
由于IE不支持DOM2标准，所以为了浏览器兼容，我们一般采用下面的形式。( DOM2的函数中direction 是一个bool值，表示冒泡（false)还是捕获(true)）  
try{ the\_botton.addEventListener(&#8216;click&#8217;,handler\_function,fasle;}  
catch(e) { the\_botton.attachEvent(&#8216;onclick&#8217;,hanler\_fucntion);}  
还有一点要注意DOM2中的函数第1参数是whichEvent,如&#8217;click&#8217;，而IE中是whichHandler,如”onclick”。有带不带”on”之分。
