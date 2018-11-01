---
layout: post
title: 数据结构与算法 - 二分查找
categories: [数据结构与算法]
tags: [数据结构与算法]
---

二分查找算法也叫折半查找算法，他是一种简单易懂的快速查找算法，主要针对于有序数据集合。

## 二分查找的思想

二分查找算法在生活中随处可可见。比如说，我们现在做一个猜数字游戏，我随机写下一个 0 到 100 的数字，然后你猜我写的是哪个数字。在猜的过程中，你每猜一次我会告诉你大了或者小了，直到猜中为止。这就是二分查找的思想。
数组（Array）是一种线性数据结构，它用一组连续的内存空间来存储相同数据类型的元素。

## 二分查找实现

二分查找可以使用循环或者递归的方式实现，下面是用 Java 实现的二分查找算法。

### 循环

```java
public int binarySearch(int[] a, int x) {
    int low = 0, mid, high = a.length - 1;
    while (low <= high) {
        mid = (low + high) / 2; // 数组中间元素下标
        if (a[mid] == x) { // 如果 a[mid] 等于 x ，返回数组下标
            return mid;
        } else if (a[mid] > x) { // 如果 a[mid] 大于 x ，说明 x 在数组右半部分，low = mid + 1
            low = mid + 1;
        } else { // 如果 a[mid] 小于 x ，说明 x 在数组左半部分，high = mid - 1
            high = mid - 1;
        }
    }
    return -1;
}
```

代码中的 low，mid，high 都是数组的下标，mid 是 [low,high] 区间的中间位置。通过比较 a[min] 和 x 的大小，来缩短接下来要查找的区间，直到找到或者区间缩小为 0，就退出。




