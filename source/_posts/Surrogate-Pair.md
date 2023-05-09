---
title: Surrogate Pair
date: 2022-06-28 14:40:01
update: 2022-6-28 15:47:34
tags:
  - Typescript
  - JavaScript
  - Unicode
categories:
  - JavaScript
description: 'JavaScript中的Surrogate Pair'
keywords: 'Typescript,JavaScript,Surrogate Pair'
top_img: /assert/index-image.png
cover: false
---

## <center>Surrogate Pair</center>
  什么是`Surrogate Pair`呢？在我了解到它的时候，我只是在Google搜索"`如何在JavaScript中获取字符串的最后一位`"，于是我得到了下面几种方式，先让我们看看这几种方式
#### 获取字符串的最后一位的几种方式
  - substring、substr
    ```JavaScript
      str.substr(str.length - 1)
      str.substring(str.length - 1)
    ```

  - charAt
    ```JavaScript
      str.charAt(str.length - 1)
    ```

  - split
    ```JavaScript
      str.split('')[str.length - 1]
    ```

  - 正则表达式
    ```JavaScript
      str.match(/.$/).pop()
    ```

  - Array.from()
    ```JavaScript
      Array.from(str).pop()
    ```

  - Spread
    ```JavaScript
      [...str].pop()
    ```

#### 几种方式处理字符串
  以上几种方式处理字符串正常情况下（如英文字符串）都没有什么问题，让我们来看看执行结果
  ```JavaScript
    let str = 'example'
    str.substr(str.length - 1) // 'e'
    str.substring(str.length - 1) // 'e'
    str.charAt(str.length - 1) // 'e'
    str.split('')[str.length - 1] // 'e'
    str.match(/.$/).pop() // 'e'
    Array.from(str).pop() // 'e'
    [...str].pop() // 'e'
  ```
  所有方式都能正确获取字符串的最后一位，但当我们我们字符串中出现`Surrogate Pair`时（比如emoji），让我们来看看会发生什么
  ```JavaScript
    let str = 'example💩'
    str.substr(str.length - 1) // '\uDCA9'
    str.substring(str.length - 1) // '\uDCA9'
    str.charAt(str.length - 1) // '\uDCA9'
    str.split('')[str.length - 1] // '\uDCA9'
    str.match(/.$/).pop() // '\uDCA9'
    Array.from(str).pop() // '💩'
    [...str].pop() // '💩'
  ```
  这个结果让是否让你感到费解？接下来让我们了解一下原因吧

#### Surrogate Pair
  - 什么是Surrogate Pair
  
    在Unicode字符集中，有一些字符的码点需要超过16位才能表示。因此，Unicode定义了一种机制，用于将这些字符分成两个16位编码单元。这些编码单元被称为代理项（Surrogate）。一个代理项由两个编码单元组成：高代理项和低代理项。高代理项的编码单元在范围0xD800到0xDBFF之间，而低代理项的编码单元在范围0xDC00到0xDFFF之间

    例如，表情符号😀（U+1F600）是由两个编码单元组成的代理对表示的。高代理项为0xD83D，低代理项为0xDE00。这两个编码单元组成的代理对表示的是一个单个的Unicode字符

  - JavaScript中的Surrogate Pair

    JavaScript使用UTF-16编码，当JavaScript遇到代理对时，它将其解释为两个单独的字符，而不是一个字符，这意味着，如果您正在处理包含代理对的字符串，您需要考虑到这些特殊字符，以确保正确处理字符串中的每个字符

    在处理包含代理对的字符串时，需要注意字符串的长度和索引。由于代理对是由两个16位编码单元组成的，所以当代理对出现在字符串中时，该字符串的长度将增加2而不是1
    ```JavaScript
      '💩'.length // 2
    ```
    此外，在字符串中使用索引访问代理对时需要注意。因为代理对的第一个编码单元在字符串中的索引位置必须是偶数，而第二个编码单元在字符串中的索引位置必须是奇数

    因此，在使用以上几种方式处理`Surrogate Pair`时，一些方法不能正确进行处理

#### 如何正确处理`Surrogate Pair`？
  在上面的几种方式中，我们也看到了如何正确处理`Surrogate Pair`
  
  - Array.from()
  
    使用Array.from()能正确的将所有字符串按照你想要的方式处理
    ```JavaScript
      let str = '😀hello💩'
      Array.from(str) // ['😀', 'h', 'e', 'l', 'l', 'o', '💩']
    ```

  - Spread
    同样使用Spread(扩展运算符)也能进行处理
     ```JavaScript
      let str = '😀hello💩'
      [...str] // ['😀', 'h', 'e', 'l', 'l', 'o', '💩']
    ```

#### 总结
  `Surrogate Pair`是Unicode字符集中的一些字符，由于其码点超过了16位，因此被拆分为两个16位编码单元。在JavaScript中，由于字符串是UTF-16编码，因此代理对被解释为两个单独的字符。在处理包含代理对的字符串时，需要注意字符串的长度和索引