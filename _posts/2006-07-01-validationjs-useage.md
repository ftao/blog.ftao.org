---
title: validation.js 使用
author: Filia.Tao
layout: post
permalink: /2006/07/01/validationjs-useage/
categories:
  - javascript
---
最近在使用validation.js 这个表单验证库[(Really easy field validation with Prototype)][1]  
遇到一个问题，对于<input type=”file”>　表单元素会失败.原因在于prototype.js 中的$F()方法不能获得<input type=”file”>　表单元素的值.

所以有两种方法来修复这个问题，其一 ,change  
`if(Validation.isVisible(elm) &#038;&#038; !v.test($F(elm), elm)) {`  
(at line 86 of validation.js (1.5.2)  
to  
`if(Validation.isVisible(elm) &#038;&#038; !v.test($F(elm)||elm.value, elm)) {`  
第二种方法,as new version of validation.js  
pass two argument “v” and “elm” to validation rules.  
We can use elm.value instead of v to validate.  
eg.

` Validation.add('validate-paper', 'Only pdf or doc file is acceptable', function (v,elm) {<br />
v = v||elm.value;                var ext = v.substr(v.lastIndexOf('.'));<br />
return (ext.toUpperCase()==".PDF" || ext.toUpperCase()==".DOC");         });  `

 [1]: http://tetlaw.id.au/view/blog/really-easy-field-validation-with-prototype/
