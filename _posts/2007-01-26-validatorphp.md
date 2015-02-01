---
title: validator.php
author: Filia.Tao
layout: post
permalink: /2007/01/26/validatorphp/
categories:
  - Web开发
tags:
  - php
---
Test for inline code highlighter.

<mycode::inline::php>  
class Validation  
{  
var $ValidateRules = Array();</mycode::inline::php>

function add($name,$advice,$func)  
{  
$this->ValidateRules[$name] = new ValidateRule($name,$advice,$func);  
}  
function addValidateRuleObject($ValidateRule)  
{  
$this->ValidateRules[$ValidateRule->name] = $ValidateRule;  
}  
function get($name)  
{  
return $this->ValidateRules[$name];  
}  
};

class ValidateRule  
{  
var $name;  
var $advice;  
var $func;  
function ValidateRule($name,$advice,$func)  
{  
$this->name = $name;  
$this->advice = $advice;  
$this->func = $func;  
}

function getAdvice()  
{  
return $this->advice;  
}

function test()  
{  
$args = func\_get\_args();  
return call\_user\_func_array($this->func,$args);  
}  
};