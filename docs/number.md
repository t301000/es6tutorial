# 數值的擴增

## 二進制和八進制表示法

ES6 提供了二進制和八進制數值的新的寫法，分別用前綴 0b 和 0o 表示。

```javascript

0b111110111 === 503 // true
0o767 === 503 // true

```

八進制用 0o 前綴的表示方法，將會取代已經在 ES5 中被逐步淘汰的加前綴 0 的寫法。

## Number.isFinite(), Number.isNaN()

ES6 在 Number 物件新提供了 Number.isFinite() 和Number.isNaN() 兩個方法，用來檢查 Infinite 和 NaN 這兩個特殊值。

Number.isFinite() 用來檢查一個數值是否非無窮（infinity）。

```javascript 

Number.isFinite(15);        // true
Number.isFinite(0.8);       // true
Number.isFinite(NaN);       // false
Number.isFinite(Infinity);  // false
Number.isFinite(-Infinity); // false
Number.isFinite("foo");     // false
Number.isFinite("15");      // false
Number.isFinite(true);      // false

```

ES5 可以透過下方的程式碼，部署 Number.isFinite 方法。

```javascript

(function (global) {
  var global_isFinite = global.isFinite;

  Object.defineProperty(Number, 'isFinite', {
    value: function isFinite(value) {
      return typeof value === 'number' && global_isFinite(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);

```

Number.isNaN() 用來檢查一個值是否為 NaN。

```javascript

Number.isNaN(NaN); // true
Number.isNaN(15); // false
Number.isNaN("15"); // false
Number.isNaN(true); // false

```

ES5 可以透過下方的程式碼，部署 Number.isNaN()。

```javascript

(function (global) {
  var global_isNaN = global.isNaN;

  Object.defineProperty(Number, 'isNaN', {
    value: function isNaN(value) {
      return typeof value === 'number' && global_isNaN(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);

```

它們與傳統的全域方法 isFinite() 和 isNaN() 的區別在於：傳統方法先呼叫 Number() 將非數值的值轉為數值，再進行判斷，而這兩個新方法只對數值有效，非數值一律回傳 false。

```javascript

isFinite(25) // true
isFinite("25") // true
Number.isFinite(25) // true
Number.isFinite("25") // false

isNaN(NaN) // true
isNaN("NaN") // true
Number.isNaN(NaN) // true
Number.isNaN("NaN") // false

```

## Number.parseInt(), Number.parseFloat()

ES6 將全域方法 parseInt() 和 parseFloat()，移植到 Number 物件上面，行為上完全保持不變。

```javascript

// ES5 的寫法
parseInt("12.34") // 12
parseFloat('123.45#') // 123.45

// ES6 的寫法
Number.parseInt("12.34") // 12
Number.parseFloat('123.45#') // 123.45

```

這麼做的目的，是逐步減少全域性的方法，使語言逐步模組化。

## Number.isInteger() 和安全整數

Number.isInteger() 用來判斷一個值是否為整數。需要注意的是，在 JavaScript 內部，整數和浮點數的儲存方式是相同的，所以 3 和 3.0 被視為同一個值。

```javascript

Number.isInteger(25)   // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false

```

ES5 可以透過下方的程式碼，部署 Number.isInteger()。

```javascript

(function (global) {
  var floor = Math.floor,
    isFinite = global.isFinite;

  Object.defineProperty(Number, 'isInteger', {
    value: function isInteger(value) {
      return typeof value === 'number' && isFinite(value) &&
        value > -9007199254740992 && value < 9007199254740992 &&
        floor(value) === value;
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);

```

JavaScript 能夠準確表示的整數範圍在 -2&#710;53 及 2&#710;53 之間。ES6 加入了 Number.MAX_SAFE_INTEGER 和Number.MIN_SAFE_INTEGER 這兩個常數，用來表示這個範圍的上下限。Number.isSafeInteger() 則是用來判斷一個整數是否落在這個範圍之內。

```javascript

var inside = Number.MAX_SAFE_INTEGER;
var outside = inside + 1;

Number.isInteger(inside) // true
Number.isSafeInteger(inside) // true

Number.isInteger(outside) // true
Number.isSafeInteger(outside) // false

```

## Math 物件的擴增

**（1）Math.trunc()**

Math.trunc 方法用於去除一個數的小數部分，回傳整數部分。

```javascript

Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4

```

**（2）Math.sign()**

Math.sign 方法用來判斷一個數到底是正數、負數、還是零。如果參數為正數，回傳 +1；參數為負數，回傳 -1；參數為 0，回傳 0；參數為 NaN，回傳 NaN。

```javascript

Math.sign(-5)  // -1
Math.sign(5)   // +1
Math.sign(0)   // +0
Math.sign(-)   // -0
Math.sign(NaN) // NaN

```

ES5 可以透過下方的程式，部署 Math.sign()。

```javascript

(function (global) {
  var isNaN = Number.isNaN;

  Object.defineProperty(Math, 'sign', {
    value: function sign(value) {
      var n = +value;
      if (isNaN(n))
        return n /* NaN */;

      if (n === 0)
        return n; // Keep the sign of the zero.

      return (n < 0) ? -1 : 1;
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);

```

**（3）數學方法**

ES6 在 Math 物件上還提供了許多新的數學方法。

- Math.acosh(x) 回傳 x 的反雙曲餘弦（inverse hyperbolic cosine）
- Math.asinh(x) 回傳 x 的反雙曲正弦（inverse hyperbolic sine）
- Math.atanh(x) 回傳 x 的反雙曲正切（inverse hyperbolic tangent）
- Math.cbrt(x) 回傳 x 的立方根
- Math.clz32(x) 回傳 x 的 32 位元二進制整數形式前導 0 的個數
- Math.cosh(x) 回傳 x 的雙曲餘弦（hyperbolic cosine）
- Math.expm1(x) 回傳 e&#710;x - 1
- Math.fround(x) 回傳 x 的單精度浮點數形式
- Math.hypot(...values) 回傳所有參數的平方和的平方根
- Math.imul(x, y) 回傳兩個參數以 32 位元整數形式相乘的結果
- Math.log1p(x) 回傳 1 + x 的自然對數
- Math.log10(x) 回傳以 10 為底的 x 的對數
- Math.log2(x) 回傳以 2 為底的 x 的對數
- Math.tanh(x) 回傳 x 的雙曲正切（hyperbolic tangent）
