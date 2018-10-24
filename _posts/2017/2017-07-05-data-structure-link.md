---
layout: post
title: 数据结构与算法 - 链表
categories: [数据结构与算法]
tags: [数据结构与算法]
---

和数组相比，链表是一种稍微复杂点的数据结构。

## 数组和链表的区别

数组需要一块连续的内存空间来存储，对内存的要求比较高。如果我们申请一个大小为 100M 的数组，当内存中没有足够大的连续内存空间时，即使内存剩余空间大于 100M，仍然会申请失败。

而链表恰恰相反，它不需要一块连续的内存空间，通过指针或引用将一块块内存连起来使用，如果我们申请 100M 大小的链表，只要剩余内存大于 100M，就可以申请成功。

数组的随机查找速度快，链表的插入、删除操作速度快。

## 链表的分类

链表主要有三种：单链表、双向链表和循环链表。

## 单链表的 Java 实现

```java
package com.renguangli.datastructure.link;

/*
 * 单链表的Java实现
 */
public class OneLink<T> {

    /**
     * 头节点，不存储任何数据，哨兵模式，解决边界问题
     * 头节点的下一个节点才是真正的头节点
     */
    private Node<T> head = newNode(null);

    /**
     * 链表长度
     */
    private int size;

    /**
     * 从头节点开始打印数据
     */
    public void print() {
        Node<T> node = head.next;
        while (node != null) {
            System.out.println(node.value);
            node = node.next;
        }
    }

    /**
     * 获取头节点数据
     * @return firstValve
     */
    public T getFirst() {
        return head.next == null ? null : head.next.value;
    }

    /**
     * 获取尾节点数据
     * @return lastV
     */
    public T getLast() {
        return lastNode().value;
    }

    public T get(int index) {
        Node<T> node = getNode(index);
        return node == null ? null : node.value;
    }

    /**
     * 添加节点
     * @param value newValue
     */
    public void add(T value) {
        addLast(value);
    }

    /**
     * 添加节点
     * @param value newValue
     */
    public void add(int index, T value) {
        if (index < 0 || index > size - 1) {
            throw new IndexOutOfBoundsException();
        }
        Node<T> newNode = newNode(value);
        Node<T> node = getNode(index);
        newNode.next = node.next;
        node.next = newNode;
    }

    /**
     * 在链表头节点插入数据
     * @param value value
     */
    public void addFirst(T value) {
        Node<T> newNode = newNode(value);
        newNode.next = this.head.next; // 1、将头节点的next节点插入到新节点的next节点
        this.head.next = newNode; // 2、将新节点插入到头节点的next节点
        // 注意 1、2 两个步骤不能交换，否则产生环，即自己指向自己
        ++size;
    }

    /**
     * 在链表尾部插入数据
     * @param value value
     */
    public void addLast(T value) {
        // 将新节点赋值给链表最后一个节点
        lastNode().next = newNode(value);
        ++size;
    }

    private Node<T> getNode(int index) {
        // 如果链表无数据返回null
        if (head.next == null) {return null;}

        int length = 0;
        Node<T> node = this.head;
        while (node.next != null) {
            node = node.next;
            if (index == length++) {// 找到指定索引处的节点并返回
                return node;
            }
        }
        // 如果index超过size - 1返回null
        return null;
    }

    /**
     * 找到链表的最后一个节点
     * @return lastNode
     */
    private Node<T> lastNode() {
        Node<T> lastNode = this.head;
        while (lastNode.next != null) {
            lastNode = lastNode.next;
        }
        return lastNode;
    }

    public int size() {
        return size;
    }

    /**
     * 新建节点
     * @param value newValue
     * @return newNode
     */
    private Node<T> newNode(T value) {
        return new Node<T>(value, null);
    }

    /**
     * 链表结构
     * @param <T>
     */
    private static class Node<T> {
        T value;
        Node<T> next;

        Node(T value, Node<T> next) {
            this.value = value;
            this.next = next;
        }
    }
}
```

## 链表的应用场景

LRU 缓存淘汰算法

Java 集合 LinkedList
