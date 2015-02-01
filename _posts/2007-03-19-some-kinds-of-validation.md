---
title: Some Kinds of Validation
author: Filia.Tao
layout: post
permalink: /2007/03/19/some-kinds-of-validation/
categories:
  - GSoc
  - javascript
tags:
  - GSoc
  - javascript
---
  1. **Simple Validation**  
    required, email, phpone number , digtial-only, alphpbet-only,etc.  
    These validation can be done using a regular expression.  
    We cann&#8217;t do Date validation only using a regular expression, but I still prefer to consider it to be an Simple Validation.
  2. **Complex Validation**  
    length,range.  
    These validation need extra argument to decide the result.
  3. **Mixed Validation**  
    Password-identical Validation, required-if-something validation,etc.  
    The Validation results are based on more than one element.
