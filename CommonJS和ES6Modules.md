# CommonJS 和 ES6 Modules

## CommmonJS

- 模块的引用: `require`
- 模块的导出: `module.exports`

引入 CommonJS 模块，其实是引入了整个模块。

```javascript
let { stat, exists, readFile } = require("fs");

// 等同于
let _fs = require("fs");
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

上面代码的实质是**整体加载** fs 模块（即加载 fs 的所有方法），生成一个对象（`fs`），然后再从这个对象上面读取 3 个方法。

一般的使用方法：

```javascript
// index.js
function isNumber(n) {
  return typeof n === "number";
}
// 导出一整个对象
module.exports = {
  sum: function (a, b) {
    if (isNumber(a) && isNumber(b)) {
      return a + b;
    } else {
      return NaN;
    }
  },
};
```

```javascript
// another.js
var mod = require("./index");
console.log(mod.sum(2, "2")); // NaN
console.log(mod.sum(2, 2)); // 4
mod.isNumber(); // 抛出错误
```

## ES6 Modules

ES6 Modules 是 ES6 引入的新语法，JavaScript 从此有了官方的模块机制。当 a 模块 import b 模块时：

- b 模块会被执行
- b 模块 export 出去的东西,会赋值给引用 a 模块中的变量

```javascript
// a.js
import bb from "./b";
console.log("index");
console.log("hello", bb);
```

```javascript
console.log("aa");
let bb = "forza juven";
console.log("bb");
export default bb;
console.log("cc");
```

输出:

```text
aa
bb
cc
index
hello forza juven
```

### import 引用

import 语句，都会执行被 import 进来的模块。import 的写法分为：

- 不带 from 的写法: 直接 import 一个模块，只是保证了这个模块代码被执行，引用它的模块是无法获得它的任何信息的。

  ```javascript
  // 引入一个模块
  import "mod";
  ```

- 带 from 的 3 种写法: 带 from 的 import 意思是，在保证这个模块代码被执行的基础上，引入模块中的一部分信息，可以把它们变成本地的变量。

  ```javascript
  // 1. 把模块默认的导出值放入变量x
  import x from "./a.js";

  // 2. 把模块中的导出的变量 a 和 modify，赋值给 x 和 modify
  import { a as x, modify } from "./a.js";

  // 3. 把模块中导出的所有的变量以类似对象属性的方式引入
  import * as x from "./a.js";
  ```

### exprot 导出

exprot 规定的是模块对外的接口，必须与模块内部的变量建立一一对应关系。export 的写法：

- 导出变量

  - 独立使用 export 声明

    ```javascript
    // circle.js
    const PI = 3.14;
    let radius = 5;
    let getArea = (r) => PI * r * r;
    export { PI, radius, getArea };
    ```

    对应的 import 写法：

    ```javascript
    // another1.js
    import { PI, radius, getArea } from "./circle";
    console.log(PI); // 3.14
    ```

    ```javascript
    // another2.js
    import * as circle from "./circle";
    console.log(circle); // { PI: 3.14, radius: 5, getArea: [Function: getArea] }
    ```

  - 直接在声明型语句(var/function/class/let/const)前添加 export

    ```javascript
    // circle.js
    export const PI = 3.14;
    export let radius = 5;
    export let getArea = (r) => PI * r * r;
    ```

    对应的 import 写法：

    ```javascript
    // another1.js
    import { PI, radius, getArea } from "./circle";
    console.log(PI); // 3.14
    ```

    ```javascript
    // another2.js
    import * as circle from "./circle";
    console.log(circle); // { PI: 3.14, radius: 5, getArea: [Function: getArea] }
    ```

- 配合 default 使用

  ```javascript
  // circle.js
  const PI = 3;
  let getArea = (r) => PI * r * r;
  export default getArea;
  ```

  对应的 import 写法：

  ```javascript
  // another.js
  // 1. default 引用(推荐)
  import getArea from "./circle";
  console.log(getArea); // [Function: getArea]
  console.log(getArea(3)); // 27

  // 2. 整体引用
  import * as circle from "./circle";
  console.log(circle); // { default: [Function: getArea] }
  console.log(circle.default(3)); // 27
  ```

### 注意事项

default 的行为跟导出变量的行为是不一致的。

- 导出变量：当 t1 模块中的 a 被更改后，t2 中引用它的 a 也会被更改
- default：当 t1 模块中的 b 被更改后，t2 中引用它的 b 不会被更改。

```javascript
// t1.js
export var a = 1;
let b = 10086;
export function modify() {
  a = 2;
  b = 10088;
}
export default b;
```

```javascript
// t2.js
import b, { a, modify } from "./t1.js";
console.log(a);
console.log(b);
modify();
console.log(a);
console.log(b);
```

执行 `node t2.js` 后，输出为：

```text
1
10086
2
10086
```

## 两者比较

CommonJS 是在**运行时**才确定引入, 然后执行这个模块, 相当于是调用一个函数, 返回一个对象, 就这么简单。当 a 文件 require 了 b 文件时，相当于**复制**了一份 b 文件，b 文件的改变不会影响 a 文件引入的值。  
ES6 Module 是语言层面的, 导入导出是声明式的代码集合,**编译时**就能确定模块的依赖关系。当 a 文件 import 了 b 文件时，相当于**引用**了一份 b 文件，b 文件的改变会影响 a 文件引入的值。
