# Truthy 的使用

Truthy 值在用到 Boolean 类型的上下文中表示 true 的值。

在 JavaScript 中，所有值都是 Truthy，除了：

- 定义了这个值是 false
- null
- undefined
- NaN
- ""
- 0

Truthy 值主要用在 if 判断中。

```javascript
if (true)
if ({})
if ([])
if (42)
if ("0")
if (new Date())
if (-42)
if (12n)
if (3.14)
if (-3.14)
if (Infinity)
if (-Infinity)
```
