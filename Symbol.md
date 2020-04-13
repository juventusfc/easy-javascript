# Symbol

## ES6 引入 Symbol 是为了表示唯一的值

**ES5 的对象属性名都是字符串**，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin 模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是 ES6 引入 Symbol 的主要原因。

另外，有些时候我们不关心具体的值，只要确保它唯一。比如，我们做一个游戏程序，用户需要选择角色的种族。

```javascript
var race = {
  protoss: "protoss", // 神族
  terran: "terran", // 人族
  zerg: "zerg", // 虫族
};

function createRole(type) {
  if (type === race.protoss) {
    创建神族角色;
  } else if (type === race.terran) {
    创建人族角色;
  } else if (type === race.zerg) {
    创建虫族角色;
  }
}
```

那么用户选择种族后，就需要调用 createRole 来创建角色：

```javascript
// 传入字符串
createRole("zerg");
// 或者传入变量
createRole(race.zerg);
```

一般传入字符串被认为是不好的做法，所以使用 `createRole(race.zerg)` 的更多。

如果使用 `createRole(race.zerg)`，我们发现 `race.protoss`、`race.terran`、`race.zerg` 的值为多少并不重要。

改为如下写法，对 `createRole(race.zerg)` 毫无影响：

```javascript
var race = {
  protoss: "askdjaslkfjas;lfkjas;flkj", // 神族
  terran: ";lkfalksjfl;askjfsfal;skfj", // 人族
  zerg: "qwieqwoirqwoiruoiwqoisrqwroiu", // 虫族
};
```

也就是说,**race.zerg 的值是多少无所谓，只要它的值跟 race.protoss 和 race.terran 的值不一样就行。**

Symbol 的用途就是如此：**Symbol 可以创建一个独一无二的值（但并不是字符串）**。

用 Symbol 来改写上面的 race：

```javascript
var race = {
  protoss: Symbol(),
  terran: Symbol(),
  zerg: Symbol(),
};

race.protoss !== race.terran; // true
race.protoss !== race.zerg; // true
```

你也可以给每个 Symbol 起一个名字：

```javascript
var race = {
  protoss: Symbol("protoss"),
  terran: Symbol("terran"),
  zerg: Symbol("zerg"),
};
```

不过这个名字跟 Symbol 的值并没有关系，你可以认为这个名字就是个注释。如下代码可以证明 Symbol 的名字与值无关：

```javascript
var a1 = Symbol("a");
var a2 = Symbol("a");
a1 !== a2; // true

var s1 = Symbol("s");
o[s1] = "sym"; // 属性名为 Symbol(s)

var s2 = Symbol("s");
o[s2] = "sym"; // 属性名为 Symbol(s)。注意，s2 也是唯一的，与 s1 的值并不相等，虽然描述都是"s"

o; // {name: "hello", gender: "male", age: 30, Symbol(s): "sym", Symbol(s): "sym"}
```

## Object 的属性 key 创建方式

Object 的属性 key，使用 String 类型创建方式有四种，使用 Symbol 类型创建方式有三种。

- String 类型作为属性 key

  ```javascript
  // 第一种方式
  var o = {
    name: "hello", // 属性名为 name
  };

  // 第二种方式
  o.gender = "male"; // 属性名为 gender

  // 第三种方式
  var t = "age";
  o[t] = 30; // 属性名为 age

  // 第四种方式
  Object.defineProperty(o, "age", {
    value: 30,
    writable: true,
    enumerable: true,
    configurable: true,
  });
  ```

- Symbol 类型作为属性 key

  ```javascript
  let mySymbol = Symbol();

  // 第一种写法
  let a = {};
  a[mySymbol] = "Hello!";

  // 第二种写法
  let a = {
    [mySymbol]: "Hello!",
  };

  // 第三种写法
  let a = {};
  Object.defineProperty(a, mySymbol, { value: "Hello!" });

  // 以上写法都得到同样结果
  a[mySymbol]; // "Hello!"
  ```

  注意，**Symbol 值作为对象属性名时，不能用点运算符**。

  ```javascript
  const mySymbol = Symbol("mysymbol");
  const a = {};

  a.mySymbol = "Hello!"; // 属性是 String 类型才能使用点运算符
  a[mySymbol] = "World";
  a; // {mySymbol: "Hello!", Symbol(mysymbol): "World"}
  ```

## Symbol 作为属性 key 时，Object 中属性的遍历

Symbol 作为属性名，该属性不会出现在 `for...in`、`for...of` 循环中，也不会被 `Object.keys()` 、 `Object.getOwnPropertyNames()` 、 `JSON.stringify()` 返回。但是，它也不是私有属性，有一个 `Object.getOwnPropertySymbols(object)` 方法，可以获取指定对象的所有 Symbol 属性名。

```javascript
const obj = {};
let a = Symbol("a");
let b = Symbol("b");

obj[a] = "Hello";
obj[b] = "World";

const objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols;
// [Symbol(a), Symbol(b)]
```

## Symbol 类型的创建方式

Symbol 类型主要有两种创建方式：

```javascript
// 第一种
var mySymbol = Symbol("my symbol");

// 第二种。具体参考下文。
var myAnotherSymbol = Symbol.for("my symbol");
```

## Symbol API

- `Symbol.for`  
  接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。

  ```javascript
  let s1 = Symbol.for("foo");
  let s2 = Symbol.for("foo");

  s1 === s2; // true
  ```

  `Symbol.for()`与 `Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的 key 是否已经存在，如果不存在才会新建一个值。比如，如果你调用 `Symbol.for("cat")` 30 次，每次都会返回同一个 Symbol 值，但是调用 `Symbol("cat")` 30 次，会返回 30 个不同的 Symbol 值。

  ```javascript
  Symbol.for("bar") === Symbol.for("bar");
  // true

  Symbol("bar") === Symbol("bar");
  // false
  ```

  上面代码中，由于 Symbol()写法没有登记机制，所以每次调用都会返回一个不同的值。

- `Symbol.keyFor`  
  方法返回一个已登记的 Symbol 类型值的 key。

  ```javascript
  let s1 = Symbol.for("foo");
  Symbol.keyFor(s1); // "foo"

  let s2 = Symbol("foo");
  Symbol.keyFor(s2); // undefined
  ```

  上面代码中，变量 s2 属于未登记的 Symbol 值，所以返回 undefined。
