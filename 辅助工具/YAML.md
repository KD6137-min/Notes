# YAML

数据序列化语言，旨在让人类直接可写可读，是 JSON 的严格超集，增加了在语法上有意义的换行符和缩进

用**缩进**来表示结构，键值对为核心，不必用 `{}`、`;`、`=`

根对象（贯穿整个YAML文档的始终）是一个映射（map），等价于其他语言中的字典、哈希表、对象

```yaml
name: Alice
age: 23
is_student: true
```

> [!caution]
>
> - 冒号后面**必须有空格**
> - **不能**用Tab缩进
> - **缩进 = 层级**，同一层级必须对齐，一般用 **2 空格或 4 空格**（项目统一即可）

```yaml
person:
  name: Alice
  age: 23
```

等价于：

```python
{
  "person": {
    "name": "Alice",
    "age": 23
  }
}
```

## 复合键

键不必为字符串，可以为其他类型，可以为复合的如多行对象

使用`?` + 空格，表示复合键的开始

```yaml
? |
  This is a key
  that has multiple lines
: and this is its value

# 可用于表示序列间映射关系
? - Manchester United
  - Real Madrid
: [ 2001-01-01, 2002-02-02 ]
```

## JSON风格

YAML 是 JSON 的超集，可以写 JSON 风格的映射和序列：

```yaml
json_map: {"key": "value"}
json_seq: [3, 2, 1, "takeoff"]
and quotes are optional: {key: [3, 2, 1, takeoff]}
```

## 列表 / 数组

### 简单列表

`-` 算缩进，不必为相同类型

```yaml
fruits:
  - apple
  - banana
  - orange
```

等价于：

```python
{
  "fruits": ["apple", "banana", "orange"]
}
```

### 列表里是对象

```yaml
students:
  - name: Alice
    age: 23
  - name: Bob
    age: 24
```

等价于：

```python
{
  "students": [
    {"name": "Alice", "age": 23},
    {"name": "Bob", "age": 24}
  ]
}
```

## 集合

```yaml
set: 
  ? item1
  ? item2
  ? item3
or: {item1, item2, item3}
```

本质是value均为null的映射，即以上set等价于：

```yaml
set2: 
  item1: null
  item2: null
  item3: null
```







## 字符串、数字、布尔值

类型自动推断，1不会被解释为布尔值

```yaml
title: Deep Learning
year: 2025
pi: 3.14
flag: true
nothing: null
```

字符串加引号的情况：

- 有 `:`、`#`、特殊符号
- 容易被误判为布尔值（yes/no/on/off）

```yaml
path: "/home/user/data"
time: "12:30"
text: "yes"
```

## 注释

```yaml
# 这是注释
learning_rate: 0.001  # 行尾注释
```

## 多行字符串

保留换行（`|`）：原样保留，写的是几行，读出来就是几行

```yaml
description: |
  This is line one.
  This is line two.
```

折叠成一行（`>`）：把换行折叠成空格

```yaml
description: >
  This is line one.
  This is line two.
```

## 引用和锚点

`&`：锚点，`*`：引用，`<<`：语言无关的合并键类型，表示一个/多个指定映射中的所有键值会插入当前映射中

```yaml
defaults: &base
  lr: 0.001
  batch_size: 16

train:
  <<: *base
  epochs: 100
```

等价于：

```yaml
train:
  lr: 0.001
  batch_size: 16
  epochs: 100
```







