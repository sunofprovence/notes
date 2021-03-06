# YAML学习笔记

## 基础概念

**XML**(eXtensible Markup Language)，可扩展标记语言。

**YAML**(Yet Another Markup Language)，字面意思是“仍然是一种标记语言”，但实际上 YAML 并不是一种标记语言。

> 有点绕口，YAML 实际上是非常接近 标记语言，但又不是严格意义上的标记语言。
> 有点像 “MongoDB是非关系型数据库中最像关系型数据库的一种数据库”的说法。



## 结构形式

YAML 使用空格缩进的形式来展示和表明整体数据结构、层级关系。

> 注意：只能用空格，不可以用 Tab 缩进

YAML 文件格式后缀为 .yml。

结构格式遵循以下几点：

1. 只能使用空格缩进，不可以使用 Tab 缩进
2. 空格多少不重要，只要是同层级别空格相同(左侧对齐)即可
3. 大小写敏感
4. 使用 # 开头即表示这行为注释



## 数据类型

YAML 支持以下几种数据类型：

1. 对象：值键对的集合，又称为 映射、哈希、字典
2. 数组：按次序排列的值，就是普通概念中的数组，又称为 序列、列表
3. 纯量：单个不可再分的值，也就是 对象属性值 或 数组元素 最深级别的值



### 数据类型：对象

对象的值键对结构形式为：key: value，切记 冒号后面一定要跟一个空格。

若对象的值也是一个对象，例如 person:{name:puxiao,age:34} ，则有以下 3种 书写形式：

第1种书写形式：使用中括号分割

```
person:{name: 'puxiao',age: 34}
```

第2种书写形式：使用空格与换行 (推荐使用)

```
person:
    name: 'puxiao'
    age: 34
```

第3种书写形式：针对复杂的对象模式，使用问号与冒号

```
?
    - complexkey1
    - complexkey2
:
    - complexvalue1
    - complexvalue2
```

上述代码表明所有属性名是一个数组[complexkey1,complexkey2]，对应的属性值也是一个数组[complexvalue1,complexvalue2]

> 第3种形式是针对复杂对象的，暂时没有完全理解用法，也没遇到具体实际应用场景。

> 一般情况下，推荐使用第2种书写形式



### 数据类型：数组

使用 - 开头的行即表示这是一个构成数组的元素，后面缩进的行即该元素的值。

#### 一维数组

例如：

```
persons:
  -
    name:'puxiao'
    age:34
  -
    name:'yang'
    age:18
```

> 上述代码中，数组每个元素的值是一个对象，拥有 name 和 age 属性。

当然也可以采用 流式(flow) 的形式来属性，以下代码可以表达出相同的数据结果：

```
person:[{name: 'puxiao',age: 34},{name: 'yang',age: 18}]
```



#### 多维数组

如果数组的元素也是数组(二维或多维数组)，则可在 数组元素 行 继续添加 -，以表明依然是一个数组，例如：

```
persons:
  -
    - A
    - B
    - C
```

以上代码中，A、B、C 均是另外一个数组



### 数据类型：纯量

更加准确的说法，应该是：数据值(对象属性或数组元素)的最终深层级别的值类型。

纯量类型有：

| 类型   | 示例                                             |
| ------ | ------------------------------------------------ |
| 字符串 | 'puxiao'                                         |
| 布尔值 | true、false、True、False、TRUE、FALSE            |
| 整数   | 34                                               |
| 浮点数 | 3.14、                                           |
| 时间   | Wed Oct 07 2020 12:46:49 GMT+0800 (中国标准时间) |
| 日期   | 2020-10-07                                       |
| Null   | ~ (使用 ~ 表示 null)                             |



## 变量引用

在 YAML 中，使用 &、*、<< 这 3 个符号来实现变量对象数据的引用。

> 官方教程中并不称呼为 变量，而是称呼为 锚点，本人只是习惯于称呼为 变量。

| 符号 | 对应含义                 |
| ---- | ------------------------ |
| &    | 对外声明(创建)引用变量名 |
| *    | 表明此处为引用某变量名   |
| <<   | 将变量合并到当前数据中   |

### 引用对象示例

以下为一个变量引用的示例：

```
me: &me
  name: 'puxiao'
  age:34
  
coder:
  backend: 'koa'
  frontend: 'react'
  <<: *me
```

以上代码等同于以下代码：

```
me:
  name: 'puxiao'
  age: 34
  
coder:
  backend: 'koa'
  frontend: 'react'
  name: 'puxiao'
  age: 34
```



### 引用纯量示例

以一个数组为例：

```
code:
  -
    &koa 'koa'
    'mongodb'
    'react'
    *koa
```

以上代码等同于：

```
code:
  -
    'koa'
    'mongodb'
    'react'
    'koa'
```

即：code:['koa','mongodb','react','koa']



### 复杂的对象结构

所谓复杂，说简单点，就是一个对象的属性，综合了普通对象、数组、多维数组等于一体。