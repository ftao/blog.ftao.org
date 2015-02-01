---
title: 单人项目git常用操作
author: Filia.Tao
layout: post
permalink: /2011/04/13/useful-git-command-for-single-person-project/
categories:
  - 编程开发
tags:
  - git
  - github
---
注意:下面的内容只适用于 1. 单人项目 2.代码托管在github 3.开发环境为Linux 

创建仓库 
:   访问github, 点击 创建仓库。 

在本地创建一个仓库 
:   `$ git init `

第一次push 到github
:   `$ git remote add origin git@github.com:username/your-repo.git<br />
            $ git push origin master<br />
           ` 

本地创建branch,并切换到该branch
:   `$ git branch branch-name<br />
             $ git checkout branch-name<br />
`

push branch 到 github
:   `$ git push origin branch-name`

创建tag
:   `$ git tag v0.1`

push (所有) tag 到 github（push tag 后github 就会出现该tag 的下载包) 
:   `$ git push --tags`</dl>