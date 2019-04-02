---
layout: post
title: Java 并发之同步辅助类 CyclicBarrier
category: [Java]
tags: [Java]
---

CyclicBarrier 允许一组线程全部到达屏障点时才会继续执行。

简单说就是线程调用 `await` 方法是到达屏障点，此时线程被阻塞，当所有线程到达屏障点时，这些线程被唤醒继续执行。

