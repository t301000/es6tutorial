# 陣列的擴展

## Array.from()

Array.from() 用於將兩類物件轉為真正的陣列：類似陣列的物件（array-like object）和可迭代（iterable）的物件，其中包括 ES6 新增的 Set 和 Map 結構。

```javascript

let ps = document.querySelectorAll('p');

Array.from(ps).forEach(function (p) {
  console.log(p);
});

```

上面程式碼中，querySelectorAll 方法回傳的是一個類似陣列的物件，只有將這個物件轉為真正的陣列，才能使用 forEach 方法。

Array.from 方法可以將函式的 arguments 物件，轉為陣列。

```javascript

function foo() {
  var args = Array.from( arguments );
}

foo( "a", "b", "c" );

```

任何有 length 屬性的物件，都可以通過 Array.from 方法轉為陣列。

```javascript

Array.from({ 0: "a", 1: "b", 2: "c", length: 3 });
// [ "a", "b" , "c" ]

```

Array.from() 還可以接受第二個參數，作用類似於陣列的 map 方法，用來對每個元素進行處理。

```JavaScript

Array.from(arrayLike, x => x * x);
// 等同於
Array.from(arrayLike).map(x => x * x);

```

Array.from() 的一個應用是，將字串轉為陣列，然後回傳字串的長度。這樣可以避免 JavaScript 將大於 \uFFFF 的 Unicode 字元算成兩個字元的 bug。

```javascript

function countSymbols(string) {
  return Array.from(string).length;
}

```

## Array.of()

Array.of() 方法用於將一組值轉換為陣列。

```javaScript

Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1

```

這個方法的主要目的是彌補陣列構造函式 Array() 的不足：因為參數個數的不同，會導致 Array() 的行為有差異。

```javascript

Array() // []
Array(3) // [undefined, undefined, undefined]
Array(3,11,8) // [3, 11, 8]
		
```

上面程式碼說明，只有當參數個數不少於 2 個，Array() 才會回傳由參陣列成的新陣列。

## 陣列實例的 find() 和 findIndex()

陣列實例的 find() 用於找出第一個符合條件的陣列元素。它的參數是一個回呼函式，所有陣列元素依次迭代該回呼函式，直到找出第一個回傳值為 true 的元素，然後回傳該元素，否則回傳 undefined。

```javascript

[1, 5, 10, 15].find(function(value, index, arr) {
	return value > 9;
}) // 10 

```

從上面程式碼可以看到，回呼函式接受三個參數，依次為當前的值、當前的位置和原陣列。

陣列實例的 findIndex() 的用法與 find() 非常類似，回傳第一個符合條件的陣列元素的位置，如果所有元素都不符合條件，則回傳 -1。

```javascript

[1, 5, 10, 15].findIndex(function(value, index, arr) {
	return value > 9;
}) // 2 

```

這兩個方法都可以接受第二個參數，用來綁定回呼函式的 this 物件。

另外，這兩個方法都可以發現 NaN，彌補了 IndexOf() 的不足。

```javascript

[NaN].indexOf(NaN) 
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0

```

## 陣列實例的 fill()

fill() 使用固定值來填充一個陣列。

```javascript

['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]

```

上面程式碼說明了 fill 方法用於空陣列的初始化非常方便。陣列中已有的元素，會被全部抹去。

fill() 還可以接受第二個和第三個參數，用於指定填充的起始位置和結束位置。

```javascript

['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']

```

## 陣列實例的 entries()，keys() 和 values()

ES6提供三個新的方法：entries()，keys() 和 values()，用於迭代陣列。它們都回傳一個迭代器，可以用 for...of 迴圈進行迭代，唯一的區別是 keys() 是對鍵名的迭代、values() 是對鍵值的迭代，entries() 是對鍵值與鍵名的迭代。

```javascript

for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// 'a'
// 'b'

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"

```

## 陣列推導

ES6 提供簡潔寫法，允許直接通過現有陣列產生新陣列，這被稱為陣列推導（array comprehension）。

```javascript

var a1 = [1, 2, 3, 4];
var a2 = [for (i of a1) i * 2];

a2 // [2, 4, 6, 8]

```

上面代碼表示，通過 for...of 結構，陣列 a2 直接在 a1 的基礎上產生。

注意，陣列推導中，for...of 結構總是寫在最前面，回傳的表達式寫在最後面。

for...of 後面還可以附加 if 判斷式，用來設定迴圈的限制條件。

```javascript

var years = [ 1954, 1974, 1990, 2006, 2010, 2014 ];

[for (year of years) if (year > 2000) year];
// [ 2006, 2010, 2014 ]

[for (year of years) if (year > 2000) if(year < 2010) year];
// [ 2006]

[for (year of years) if (year > 2000 && year < 2010) year];
// [ 2006]

```
上面程式碼說明了 if 判斷式寫在 for...of 與回傳的表達式之間，可以使用多個 if 判斷式。

陣列推導可以替代 map 和 filter 方法。

```javascript

[for (i of [1, 2, 3]) i * i];
// 等價於
[1, 2, 3].map(function (i) { return i * i });

[for (i of [1,4,2,3,-8]) if (i < 3) i];
// 等價於
[1,4,2,3,-8].filter(function(i) { return i < 3 });

```

上面程式碼說明了模擬 map 功能只要單純的 for...of 迴圈就行了，模擬 filter 功能除了 for...of 迴圈，還必須加上 if 判斷式。

在一個陣列推導中，還可以使用多個 for...of 結構，構成多重迴圈。

```javascript

var a1 = ["x1", "y1"];
var a2 = ["x2", "y2"];
var a3 = ["x3", "y3"];

[for (s of a1) for (w of a2) for (r of a3) console.log(s + w + r)];
// x1x2x3
// x1x2y3
// x1y2x3
// x1y2y3
// y1x2x3
// y1x2y3
// y1y2x3
// y1y2y3

```

上面程式碼在一個陣列推導之中，使用了三個 for...of 結構。

需要注意的是，陣列推導的方括號構成了一個單獨的作用域，在這個方括號中宣告的變數類似於使用 let 指令聲明的變量。

由於字串可以視為陣列，因此字串也可以直接用於陣列推導。

```javascript

[for (c of 'abcde') if (/[aeiou]/.test(c)) c].join('') // 'ae'

[for (c of 'abcde') c+'0'].join('') // 'a0b0c0d0e0'

```

上面程式碼使用了陣列推導，對字串進行處理。

陣列推導需要注意的地方是，新陣列會立即在記憶體中產生。這時，如果原陣列是一個很大的陣列，將會非常耗費記憶體。

## Array.observe()，Array.unobserve()

這兩個方法用於監聽（取消監聽）陣列的變化，指定回呼函式。

它們的用法與 Object.observe 和 Object.unobserve 方法完全一致，也屬於 ES7 的一部分，請參閱《物件的擴增》一章。唯一的區別是，物件可監聽的變化一共有六種，而陣列只有四種：add、update、delete、splice（陣列的 length 屬性發生變化）。
