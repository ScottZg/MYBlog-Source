---
title: Git分支管理规范
date: 2019-01-18 10:01:32
tags: ["Git"]
---
为了规范Git分支，可以参考使用下列规范进行命名分支，方便开发与分支管理。    
<table><tr><td>分支</td><td>用途</td><td>Demo</td><td>说明</td></tr><tr><td>master</td><td>主分支，不能直接向该分支提交代码</td><td>master</td><td>锁定分支权限</td></tr><tr><td>release/xxx</td><td>上线分支，不要直接向该分支提交代码</td><td>release/10.0.0</td><td>锁定分支权限</td></tr><tr><td>feature/xxx</td><td>个人开发分支，用于新需求开发</td><td>feature/jira-2323</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr><tr><td>bugfix/xxx</td><td>bug修复开发</td><td>bugfix/jira-2322</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr><tr><td>refactor/xxx</td><td>重构，不涉及bug修复，不新增需求，只是代码重构</td><td>refactor/jira-2322</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr><tr><td>pref/xxx</td><td>改进性能的代码更改</td><td>pref/jira-2322</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr><tr><td>hotfix/xxx</td><td>线上问题修复</td><td>hotfix/jira-2322</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr><tr><td>docs/xxx</td><td>不涉及代码层面的更改</td><td>docs/jira-2322</td><td>xxx可以按照自己的规则去写，也可以写taskid</td></tr></table>