---
layout: post
title: "git提交出现fatal: Unable to create ‘project_path/.git/index.lock’: File exists."
category: [git,error]
tags: [error]
---

今天使用git提交代码出现了如下错误
``` bash
fatal: Unable to create ‘java-examples/.git/index.lock’: File exists.
```

index.lock文件已存在，删除即可。

``` bash
rengu@LAPTOP-VKU3G8S8 MINGW64 /c/Java/IdeaProjects/java-examples (master)
$ ll .git |grep index.lock
-rw-r--r-- 1 rengu 197609     0 10月  5 21:03 index.lock

rengu@LAPTOP-VKU3G8S8 MINGW64 /c/Java/IdeaProjects/java-examples (master)
$ rm -r .git/index.lock
```



