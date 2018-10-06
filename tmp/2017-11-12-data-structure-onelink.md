---
layout: post
title: 单链表的Java实现
category: [数据结构与算法]
tags: [数据结构与算法]
---

链表和数组一样是一种线程结构，不同的是链表的插入和删除效率比数组高。

数组随机访问和末尾插入删除效率高。

## 代码实现

```
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
    public boolean add(int index, T value) {
        if (index > size - 1) {
            throw new IndexOutOfBoundsException();
        }

        Node<T> newNode = newNode(value);
        Node<T> node = getNode(index);
        newNode.next = node.next;
        node.next = newNode;

        addLast(value);
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

    /**
     * 链表长度
     */
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


