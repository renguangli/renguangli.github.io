---
layout: post
title: Java 中判断字母的大小写并互相转化的方法
category: [Java]
tags: [Java]
---

判断字母的大小写并互相转化

## 大小写判断

### 方法一

根据 Character 类提供的大小写判断方法

```java
Character.isUpperCase(c); // 是否是大写
Character.isUpperCase(c); // 是否是小写
```

### 方法二 

通过 ASCII 码判断字母大小写，ASCII在 65-90 之间是大写，97-122 是小写

```java
/*
 * 是否是大写
 */
public boolean isUpperCase(char c) {
    return c >=65 && c <= 90;
}

/*
 * 是否是小写
 */
public boolean isLowerCase(char c) {
    return c >=97 && c <= 122;
}
```

## 大小写互相转换

### 方法一

根据 Character 类提供的大小写转换方法

```java
/*
 * 小写转大写
 */
public char toUpper(char c) {
    return Character.isLowerCase(c) ? c : Character.toUpperCase(c);
}

/*
 * 大写转小写
 */
public char toLower(char c) {
    return Character.isUpperCase(c) ? c : Character.toLowerCase(c);
}
```

### 方法二

通过 ASCII 加 32 转换为小写，减 32 转换为大写

```java
/*
 * 是否是大写
 */
public boolean isUpperCase(char c) {
    return c >=65 && c <= 90;
}

/*
 * 是否是小写
 */
public boolean isLowerCase(char c) {
    return c >=97 && c <= 122;
}
```

## 字符串大写转小写，小写转大写

```java
public class WordUpperLow {

    public static void main(String[] args) {
        new WordUpperLow().upToLowToUp("HeLLoWoRlD");
    }

    public void upToLowToUp(String str) {

        /*
         * 方法一 根据 char 的工具类 Character
         */
        char[] chars = str.toCharArray();
        for (int i = 0, length = chars.length; i < length; i++) {
            char c = chars[i];
            //判断字母是不是大写，如果是大写变为小写
            if (Character.isUpperCase(c)){
                chars[i] = Character.toLowerCase(c);
                continue;
            }
            //如果为小写，变为大写
            chars[i] = Character.toUpperCase(c);
        }
        String str1 = new String(chars);
        System.err.println(str1);

        /*
         * 方法二
         * 通过ASCII码判断字母大小写 ASCII在65-90之间是大写，97-122是小
         * 大转小加32 小转大减去32
         */
        byte[] bytes = str.getBytes();
        for (int i = 0, length = bytes.length; i < length; i++) {
            //如果ASCII在65-90之间为大写，加上32变为小写
            if (bytes[i] >= 65 && bytes[i] <= 90){
                bytes[i] += 32;
                //如果ASCII在97-122之间为小写，减去32变为大写
            } else if (bytes[i] >= 97 && bytes[i] <= 122){
                bytes[i] -= 32;
            }
        }
        String str2 = new String(bytes);
        System.err.println(str2);
    }

    /*
     * 小写转大写
     */
    public char toUpper(char c) {
        return c >=65 && c <= 90 ? c : (char)(c - 32);
    }

    /*
     * 大写转小写
     */
    public char toLower(char c) {
        return c >=97 && c <= 122 ? c : (char)(c + 32);
    }


    /*
     * 是否是大写
     */
    public boolean isUpperCase(char c) {
        // Character.isUpperCase(c);
        return c >=65 && c <= 90;
    }

    /*
     * 是否是小写
     */
    public boolean isLowerCase(char c) {
//        Character.isLowerCase(c);
        return c >=97 && c <= 122;
    }
}

```