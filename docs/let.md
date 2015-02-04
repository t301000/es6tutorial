# let 和 const 語法

## let 語法

ES6 新增了 let 指令，用來宣告變數。它的用法類似於 var ，但是所宣告的變數只在 let 指令所在的程式碼區塊內有效。

```javascript

{
    let a = 10;
    var b = 1;
}

a // ReferenceError: a is not defined.
b //1

```

上面程式碼在程式碼區塊之中，分別用 let 和 var 宣告了兩個變數。然後在程式碼區塊之外使用這兩個變數，結果 let 宣告的變數出現錯誤， var 宣告的變數回傳了正確的值。這表示， let 宣告的變數只在它所在的程式碼區塊有效。

for 迴圈的計數器，就很適合使用 let 指令。

```javascript

for(let i = 0; i < arr.length; i++){}
 
console.log(i)
//ReferenceError: i is not defined


```

上面變數的計數器 i ，只在 for 迴圈體內有效。

下面的程式碼如果使用 var ，最後輸出的是 9 。

```javascript

var a = [];
for (var i = 0; i < 10; i++) {
  var c = i;
  a[i] = function () {
    console.log(c);
  };
}
a[6](); // 9

```

如果使用 let ，宣告的變數僅在區塊的作用域內有效，最後輸出的是 6 。

```javascript

var a = [];
for (var i = 0; i < 10; i++) {
  let c = i;
  a[i] = function () {
    console.log(c);
  };
}
a[6](); // 6

```

let 不像 var 那樣，會發生“變數提升”現像。

```javascript

function do_something() {
  console.log(foo); // ReferenceError
  let foo = 2;
}

```

上面程式碼在宣告 foo 之前，就使用這個變數，結果會拋出一個錯誤。

注意，let 不允許在相同作用域內，重復宣告同一個變數。

```javascript

// 出現錯誤
{
    let a = 10;
    var a = 1;
}

// 出現錯誤
{
    let a = 10;
    let a = 1;
}

```

## 區塊作用域

let 實際上為 JavaScript 新增了區塊作用域。

```javascript

function f1() {
  let n = 5;
  if (true) {
      let n = 10;
  }
  console.log(n); // 5
}

```

上面的函式有兩個程式碼區塊，都宣告了變數 n ，執行後輸出 5 。這表示外層程式碼區塊不受內層程式碼區塊的影響。如果使用 var 定義變數 n ，最後輸出的值就是 10 。

區塊作用域的出現，實際上使得獲得廣泛應用的立即函式（ IIFE ）不再必要了。

```javascript

// IIFE 寫法
(function () {
    var tmp = ...;
    ...
}());

// 區塊作用域寫法
{
    let tmp = ...;
    ...
}

```

另外， ES6 也規定，函式本身的作用域，在其所在的區塊作用域之內。

```javascript

function f() { console.log('I am outside!'); }
(function () {
  if(false) {
    // 重復宣告一次函數f
    function f() { console.log('I am inside!'); }
  }

  f();
}());

```

上面程式碼在 ES5 中運行，會得到“ I am inside! ”，但是在 ES6中 執行，會得到“ I am outside! ”。這是因為 ES5 存在函式提升，不管會不會進入 if 程式碼區塊，函式宣告都會提升到當下作用域的頂部，得到執行；而 ES6 支持區塊作用域，不管會不會進入 if 程式碼區塊，其內部宣告的函式皆不會影響到作用域的外部。

需要注意的是，如果在嚴格模式下，函式只能在頂層作用域和函式內宣告，其他情況（比如 if 程式碼塊、迴圈程式碼塊）的宣告都會出現錯誤。

## const 指令

const 也用來宣告變數，但是宣告的是常數。一旦宣告，常數的值就不能改變。

```javascript

const PI = 3.1415;
PI // 3.1415

PI = 3;
PI // 3.1415

const PI = 3.1;
PI // 3.1415

```

上面程式碼表示改變常數的值是沒有作用的。需要注意的是，對常數重新賦值不會報錯，只會默默地失敗。

const 的作用域與 let 指令相同：只在宣告所在的區塊作用域內有效。

```javascript

if (condition) {
    const MAX = 5;
}

// 常數 MAX 在此處不可得

```

const 宣告的常數，也與 let 一樣不可重復宣告。

```javascript

var message = "Hello!";
let age = 25;

// 以下兩行都會出現錯誤
const message = "Goodbye!";
const age = 30;

```
