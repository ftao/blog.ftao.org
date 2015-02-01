---
title: 写的第一个Greasemonkey脚本
author: Filia.Tao
layout: post
permalink: /2006/04/12/first-greasemokey-script/
categories:
  - Firefox
  - javascript
---
<font size="-1"><font color="#000000"><a href="http://cn.iblog.com/greasemonkey.mozdev.org/">Greasemonkey</a></font>一个Firefox的扩展，可以执行javascript脚本，为页面添加很多实用功能。</font>

最近在学JavaScript，顺手写了一个脚本，解决了一个问题。

大家都知道我们学校的教务处的网站是做的不咋样。起码肯定不复合WEB标准，用firefox是无法登录的。

其实呢，网页的设计者还是考虑了浏览器兼容性的，不过犯了一个小错误。大家看[这个页面][1]的源码,它里面的check()函数中犯了一个小错误。

<div>
  <pre>function check() {  	if ( document.main.xh.value.length == 0 ) 	{ 	   window.alert( "请输入用户！" ) 	  document.main.xh.focus() 	 }  	else if (document.main .user_pwd .value.length ==0 ) 	 {          window.alert("请输入密码")           document.main .user_pwd .focus()      　}          　　else 	{   	  <font color="#ff0000"> self.main.submit();</font>     　}    }</pre>
</div>

self.main.submit()是不正确的，应该self指的是window而非document。我就不明白了上面两处都用了document.main.XXXX，为什么这里就要用错误的self.main.submit()呢？

所以我就写了个小脚本([下载][2]）修正了这个错误。

<div>
  str=&#8217;if ( document.main.xh.value.length == 0 )&#8217;;<br /> str+=&#8217;{window.alert( “Please Input UserName!” );document.main.xh.focus();}&#8217;;<br /> str+=&#8217;else if (document.main .user_pwd .value.length ==0 )&#8217;<br /> str+=&#8217;{window.alert(“Please Input Password!”);document.main .user_pwd .focus();}&#8217;;<br /> str+=&#8217;else{document.main.submit();}&#8217;;<br /> aHref=document.getElementsByTagName(“a”);<br /> for (i=aHref.length-1;i>=0;i++)<br /> {<br /> if (aHref[i].href=”javascript:check()” mce_href=”javascript:check()”)<br /> break;<br /> }if (i>=0)<br /> { aHref[i].href=”javascript:” mce_href=”javascript:”+str;<br /> }
</div>

我的脚本其实不好，应该还有更好的方法，不过还没学会<img border="0" alt="Laughing" src="http://cn.iblog.com/js/tinymce/jscripts/tiny_mce/plugins/emotions/images/smiley-laughing.gif" />。

 [1]: http://jwc.seu.edu.cn/admin/students/default.asp?topage=1&#038;mode=2
 [2]: http://whygudu.iblog.cn/resserver.php?blogId=6692&#038;resource=jwcSeu.user.js
