---
layout: post
title: Mybatis 插入/批量插入返回主键 ID
category: [Mybatis]
tags: [Mybatis]
---

有时候我们在插入一条数据后需要返回主键ID，Mytais 提供了相关方法。

User 实体类
```
public class User {
    private Integer userId;
    private String nickName;
    private String realName;
    private String avatar；
}
```
Mapper 接口

```
Long save(User user); // 单条插入
Long batchSave(List<User> users);// 批量插入
```

## 单条插入返回主键
Mapper 映射文件
```
<!-- 插入时将主键值设置到参数user的userId字段上 -->
<insert id="save" useGeneratedKeys="true" keyProperty="userId">
    insert into user (nick_name, real_name, avatar)
    values (#{nickName}, #{realName}, #{avatar})
</insert>
```
或者

```
<insert id="save" useGeneratedKeys="true" keyProperty="userId">
    <selectKey keyProperty="userId" resultType="int" order="AFTER">
        select last_insert_id();
    </selectKey>
    insert into user (nick_name, real_name, avatar)
    values (#{nickName}, #{realName}, #{avatar})
</insert>
```

插入成功后 Mybatis 会自动将主键 ID 赋值给 user 对象的 userId 字段。

## 批量插入返回主键
Mapper 映射文件

```
<!-- 批量插入时将主键值设置到参数user的userId字段上 -->
<insert id="batchSave" useGeneratedKeys="true" keyProperty="userId">
    insert into user
      (nick_name, real_name, avatar)
    values 
    <foreach collection="list" item="item" separator=",">
        (#{item.nickName}, #{item.realName}, #{item.avatar})
    </foreach>
</insert>
```
UserService

```
List<User> batchSave(List<User> users){
	userMapper.batchSave(users);
	return users;
}
```
插入成功后，参数 users 中的 User 对象已包含主键 ID 了。

## 参考

<https://www.cnblogs.com/xiao-lei/p/6809884.html>