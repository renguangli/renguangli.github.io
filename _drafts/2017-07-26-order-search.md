---
layout: post
title: 数据结构与算法 - 查找算法之顺序查找
category: 数据结构与算法
tags: [数据结构与算法]
---

所谓顺序查找就是按照顺序一个一个找直到找到为止。

顺序查找的元素有序无序都可以使用。

```java
public void bubbleSort(int[] a, int x) {
    for (int i = 0; i < a.length; i++) {
        if (a[i] = x) {
            return i
        }
    }
    return -1;
}
```

