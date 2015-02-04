# 變數的解構賦值

## 陣列的解構賦值

ES6 允許按照一定模式，從陣列和物件中提取值，對變數進行賦值，這被稱為解構（ Destructuring ）。

以前，為變數賦值，只能直接指定值。

```javascript

var a = 1;
var b = 2;
var c = 3;

```

ES6 允許寫成下面這樣。

```javascript

var [a, b, c] = [1, 2, 3];

```

上面程式碼表示，可以從陣列中提取值，按照對應位置，對變數賦值。

本質上，這種寫法屬於“模式匹配”，只要等號兩邊的模式相同，左邊的變數就會被賦予對應的值。下面是一些使用巢狀陣列進行解構的例子：

```javascript

var [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

var [,,third] = ["foo", "bar", "baz"];
third // "baz"

var [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

```

如果解構不成功，變數的值就等於 undefined 。

```javascript

var [foo] = [];
var [foo] = 1;
var [foo] = 'Hello';
var [foo] = false;
var [foo] = NaN;
var [bar, foo] = [1];

```

以上幾種情況都屬於解構不成功， foo 的值都會等於 undefined 。另一種情況是不完全解構。

```javascript

var [x, y] = [1, 2, 3];

```

上面程式碼中， x 和 y 可以順利取到值。

如果對 undefined 或 null 進行解構，會出現錯誤。

```javascript

// 出現錯誤
var [foo] = undefined;
var [foo] = null;

```

這是因為解構只能用於陣列或物件。其他原始類型的值都可以轉為相應的物件，但是， undefined 和 null 不能轉為物件，因此出現錯誤。

解構賦值允許指定預設值。

```javascript

var [foo = true] = [];
foo // true

[x, y='b'] = ['a'] // x='a', y='b'
[x, y='b'] = ['a', undefined] // x='a', y='b'

```

解構賦值不僅適用於 var 指令，也適用於 let 和 const 命令。

```javascript

var [v1, v2, ..., vN ] = array;
let [v1, v2, ..., vN ] = array;
const [v1, v2, ..., vN ] = array;

```

對於 Set 結構，也可以使用陣列的解構賦值。

```javascript

[a, b, c] = new Set(["a", "b", "c"])
a // "a"

```

事實上，只要某種資料結構具有迭代器模式接口，都可以採用陣列形式的解構賦值。

## 物件的解構賦值

解構不僅可以用於陣列，還可以用於物件。

```javascript

var { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

```

物件的解構與陣列有一個重要的不同。陣列的元素是按順序排列的，變數的取值由它的位置決定；而物件的屬性沒有順序，變數必須與屬性同名，才能取到正確的值。

```javascript

var { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined

```

上面程式碼的第一個例子，等號左邊的兩個變數的順序，與等號右邊兩個同名屬性的順序不一致，但是對取值完全沒有影響。第二個例子的變數沒有對應的同名屬性，導致取不到值，最後等於 `undefined` 。

如果變數名稱與屬性名稱不一致，必須寫成下面這樣。

```javascript

var { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"

```

和陣列一樣，解構也可以用於巢狀結構的物件。

```javascript

var o = {
  p: [
    "Hello",
    { y: "World" }
  ]
};

var { p: [x, { y }] } = o;
x // "Hello"
y // "World"

```

物件的解構也可以指定預設值。

```javascript

var { x = 3 } = {};
x // 3

var {x, y = 5} = {x: 1};
console.log(x, y) // 1, 5

```

如果要將一個已經宣告的變數用於解構賦值，必須非常小心。

```javascript

// 錯誤的寫法

var x;
{x} = {x:1};
// SyntaxError: syntax error

```

上面程式碼的寫法會出現錯誤，因為 JavaScript 引擎會將 `{x}` 解析成一個程式碼區塊，從而發生語法錯誤。只有不將大括號寫在行首，避免 JavaScript 將其解析為程式碼區塊，才能解決這個問題。

```javascript

// 正確的寫法

({x}) = {x:1};
// 或者
({x} = {x:1});

```

物件的解構賦值，可以很方便地將現有物件的方法，賦值到某個變數。

```javascript

let { log, sin, cos } = Math;

```

上面程式碼將 Math 物件的對數、正弦、餘弦三個方法，賦值到對應的變數上，使用起來就會方便很多。

## 用途

變數的解構賦值用途很多。

**（1）交換變數的值**

```javascript

[x, y] = [y, x]; 

```

**（2）從函式回傳多個值**

函式只能回傳一個值，如果要回傳多個值，只能將它們放在陣列或物件裡回傳。有了解構賦值，取出這些值就非常方便。

```javascript

// 返回一個陣列

function example() {
    return [1, 2, 3];
}
var [a, b, c] = example();

// 返回一個物件

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
var { foo, bar } = example();

```

**（3）函式參數的定義**

```javascript

function f([x]) { ... }

f(['a'])

function f({x, y, z}) { ... }

f({x:1, y:2, z:3})

```

這種寫法對取用 JSON 物件中的資料來說相當有用。

**（4）函式參數的預設值**

```javascript

jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};

```

指定參數的預設值，就避免了在函式內部再寫 `var foo = config.foo || 'default foo';` 這樣的語句。

**（5）迭代 Map 結構**

任何部署了迭代器模式的物件，都可以用 for...of 迴圈迭代。 Map 結構原生支援迭代器模式，配合變數的結構賦值，取得鍵名和鍵值就會非常方便。

```javascript

var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world

```

如果只想取得鍵名，或者只想取得鍵值，可以寫成下面這樣。

```javascript

// 取得鍵名
for (let [key] of map) {
  // ...
}

// 取得鍵值
for (let [,value] of map) {
  // ...
}

```

**（6）輸入模組的指定方法**

載入模組時，往往需要指定輸入哪些方法。使用解構賦值讓輸入的語句非常簡單明瞭。

```javascript

const { SourceMapConsumer, SourceNode } = require("source-map");

```
