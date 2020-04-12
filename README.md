# easy-javascript

JavaScript 的主要功能是响应用户对网页的行为，从而更改网页内容和展示形式。对任何编程语言来说，必定是**用规定的文法，表达特定语义，最终操作运行时**。

一般来说，**程序=算法+数据结构**，对运行时来说，执行过程就是算法，类型就是数据结构，。

JavaScript 文件从编写到执行的过程为：

![javascript](./images/javascript.png)

图中，编写 JavaScript 文件并以二进制流的形式存储。当需要执行这个文件时，将文件以二进制流的形式读取出来，交给 JavaScript 引擎去解码、词法分析、语法分析、执行。

## JavaScript 的知识架构

- 文法
  - [词法](./词法.md)
  - [语法](./语法.md)
    - CommonJS 和 ES6Modules
- 语义
- 运行时
  - 数据结构
    - [类型](./类型.md)
      - Symbol
      - Object
      - Handle Big Int in JavaScript
    - 实例
      - 应用和机制
  - 算法
    - 事件循环和宏观任务和微观任务
    - 函数的执行
    - 语句级的执行
      - Truthy 的使用

## 使用 JavaScript 的注意事项

JavaScript 有很多令人奇怪的语言特性，有些都能算是语言缺陷了。整理如下:

[注意事项](./注意事项.md)
