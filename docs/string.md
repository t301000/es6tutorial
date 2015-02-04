# 字串的擴增

ES6 加強了對 Unicode 的支援性，並且擴增了字串物件。

## codePointAt()

JavaScript 內部，字元以 UTF-16 的格式儲存，每個字元固定為 2 個位元組。對於那些需要 4 個位元組儲存的字元（ Unicode 編碼位置大於 0xFFFF 的字元）， JavaScript 會認為它們是兩個字元。

```javascript

var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1)	// ''
s.charCodeAt(0) // 55362		
s.charCodeAt(1) // 57271
		
```

上面程式碼中，中文字“𠮷”的編碼位置是 0x20BB7 ， UTF-16 編碼為 0xD842 0xDFB7 （十進制為 55362 57271 ），需要 4 個位元組儲存。對於這種 4 個位元組的字元， JavaScript 不能正確處理，字串長度會誤判為 2 ，而且 charAt 方法無法讀取字元， charCodeAt 方法只能分別回傳前兩個位元組和後兩個位元組的值。

ES6 提供了 codePointAt 方法，能夠正確處理 4 個位元組儲存的字元，回傳一個字元的編碼位置。

```javascript

var s = "𠮷a";

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271
		
s.charCodeAt(2) // 97

```

codePointAt 方法的參數，是字元在字串中的位置（從 0 開始）。上面程式碼中， JavaScript 將“𠮷 a ”視為三個字元， codePointAt 方法在第一個字元上，正確地辨識了“𠮷”，回傳了它的十進制編碼位置 134071 （即十六進制的 20BB7 ）。在第二個字元（即“𠮷”的後兩個位元組）和第三個字元“ a ”上， codePointAt 方法的結果與 charCodeAt 方法相同。

總之， codePointAt 方法會正確回傳四位元組的 UTF-16 字元的編碼位置。對於那些兩個位元組儲存的常規字元，它的回傳結果與 charCodeAt 方法相同。

codePointAt 方法是測試一個字元由兩個位元組還是由四個位元組組成的最簡單方法。

```javascript

function is32Bit(c) {
    return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false

```

## String.fromCodePoint()

ES5 提供 String.fromCharCode 方法，用於從編碼位置回傳對應字元，但是這個方法不能辨識輔助平面的字元（編號大於 0xFFFF ）。

```javascript

String.fromCharCode(0x20BB7)
// "ஷ"

```

上面程式碼中，最後回傳編碼位置 U+0BB7 對應的字元，而不是編碼位置 U+20BB7 對應的字元。

ES6 提供了 String.fromCodePoint 方法，可以辨識 0xFFFF 的字元，彌補了 String.fromCharCode 方法的不足。在作用上，正好與 codePointAt 方法相反。

```javascript

String.fromCodePoint(0x20BB7) 
// "𠮷"

```

注意， fromCodePoint 方法定義在 String 物件上，而 codePointAt 方法定義在字串的實例物件上。

## at()

ES5 提供 String.prototype.charAt 方法，回傳字串給指定位置的字元。該方法不能識別編碼位置大於 0xFFFF 的字元。

```javascript

'𠮷'.charAt(0)
// '\uD842'

```

上面程式碼中， charAt 方法回傳的是 UTF-16 編碼的第一個位元組，實際上是無法顯示的。

ES7 提供了 at 方法，可以識別 Unicode 編號大於 0xFFFF 的字元，回傳正確的字元。

```javascript

'𠮷'.at(0)
// '𠮷'

```

## 字元的 Unicode 表示法

JavaScript 允許採用“ \uxxxx ”形式表示一個字元，其中“ xxxx ”表示字元的編碼位置。

```javascript

"\u0061"
// "a"

```

但是，這種表示法只限於 \u0000——\uFFFF 之間的字元。超出這個範圍的字元，必須用兩個雙位元組的形式表達。

```javascript

"\uD842\uDFB7"
// "𠮷"

"\u20BB7"
// " 7"

```

上面程式碼表示，如果直接在“ \u ”後面跟上超過 0xFFFF 的數值（比如 \u20BB7 ）， JavaScript 會理解成“ \u20BB+7 ”。由於 \u20BB 是一個不可顯示的字元，所以只會顯示一個空格，後面跟著一個 7 。

ES6 對這一點做出了改進，只要將編碼位置放入大括號，就能正確解讀該字元。

```javascript

"\u{20BB7}"
// "𠮷"

"\u{41}\u{42}\u{43}"
// "ABC"

```

## 正規表示式的修飾符號 u

ES6 對正規表示式增加了修飾符號 u，用來正確處理大於 \uFFFF 的 Unicode 字元。

**（1）點字元**

點（ . ）字元在正規表示式中，解釋為除了換行以外的任意單個字元。對於編碼位置大於 0xFFFF 的 Unicode 字元，點字元不能識別，必須加上修飾符號 u 。

```javascript

var s = "𠮷";

/^.$/.test(s) // false
/^.$/u.test(s) // true

```

上面程式碼表示，如果不增加修飾符號 u ，正規表示式就會認為字串為兩個字元，從而匹配失敗。

**（2）Unicode字元表示法**

ES6 新增了使用大括號表示 Unicode 字元，這種表示法在正規表示式中必須加上修飾符號 u ，才能識別。

```javascript

/\u{61}/.test('a') // false
/\u{61}/u.test('a') // true
/\u{20BB7}/u.test('𠮷') // true

```

上面程式碼表示，如果不加修飾符號 u ，正規表示式無法識別 `\u{61}` 這種表示法，只會認為這匹配 61 個連續的 u 。

**（3）量詞**

使用修飾符號 u 後，所有量詞都會正確識別大於編碼位置大於 0xFFFF 的 Unicode 字元。

```javascript

/a{2}/.test('aa') // true
/a{2}/u.test('aa') // true
/𠮷{2}/.test('𠮷𠮷') // false
/𠮷{2}/u.test('𠮷𠮷') // true

```

**（4）預設模式**

修飾符號 u 也影響到預設模式，能否正確辨識編碼位置大於 0xFFFF 的 Unicode 字元。

```javascript

/^\S$/.test('𠮷') // false
/^\S$/u.test('𠮷')

```

上面程式碼的 `\S` 是預設模式，匹配所有不是空格的字元。只有加了修飾符號 u ，它才能正確匹配編碼位置大於 0xFFFF 的 Unicode 字元。

利用這一點，可以寫出一個正確回傳字串長度的函式。

```javascript

function codePointLength(text) {
    var result = text.match(/[\s\S]/gu);
    return result ? result.length : 0;
}

var s = "𠮷𠮷";

s.length // 4
codePointLength(s) // 2

```

**（5）修飾符號 i**

有些 Unicode 字元的編碼不同，但是字型很相近，比如， \u004B 與 \u212A 都是大寫的K。

```javascript

/[a-z]/i.test('\u212A') // false
/[a-z]/iu.test('\u212A') // true

```

上面程式碼中，不加修飾符號 u ，就無法辨識非規範的 K 字元。

## normalize()

為了表示語調和重音符號， Unicode 提供了兩種方法。一種是直接提供帶重音符號的字元，比如 Ǒ （ \u01D1 ）。另一種是提供合成符號（ combining character ），即原字元與重音符號的合成，兩個字元合成一個字元，比如 O （ \u004F ）和 ˇ （ \u030C ）合成 Ǒ （ \u004F\u030C ）。

這兩種表示方法，在視覺和語義上都等價，但是 JavaScript 不能識別。

```javascript

'\u01D1'==='\u004F\u030C' //false

'\u01D1'.length // 1
'\u004F\u030C'.length // 2

```

上面程式碼表示， JavaScript 將合成字元視為兩個字元，導致兩種表示方法不相等。

ES6 提供 String.prototype.normalize() 方法，用來將字元的不同表示方法統一為同樣的形式，這稱為 Unicode 正規化。

```javascript

'\u01D1'.normalize() === '\u004F\u030C'.normalize() 
// true

```

normalize 方法可以接受四個參數：

- NFC ，預設參數，表示“標準等價合成”（ Normalization Form Canonical Composition ），回傳多個簡單字元的合成字元。所謂“標準等價”指的是視覺和語義上的等價。
- NFD ，表示“標準等價分解”（ Normalization Form Canonical Decomposition ），即在標準等價的前提下，回傳合成字元分解的多個簡單字元。
- NFKC ，表示“相容等價合成”（ Normalization Form Compatibility Composition ），回傳合成字元。所謂“相容等價”指的是語義上存在等價，但視覺上不等價，比如“囍”和“喜喜”。
- NFKD ，表示“相容等價分解”（ Normalization Form Compatibility Decomposition ），即在相容等價的前提下，回傳合成字元分解的多個簡單字元。

```javascript

'\u004F\u030C'.normalize(NFC).length // 1
'\u004F\u030C'.normalize(NFD).length // 2

```

上面程式碼表示， NFC 參數回傳字元的合成形式， NFD 參數回傳字元的分解形式。

不過， normalize 方法目前不能識別三個或三個以上字元的合成。這種情況下，還是只能使用正規表示式，通過 Unicode 編號區間判斷。

## includes(), startsWith(), endsWith()

傳統上， JavaScript 只有 indexOf 方法，可以用來確定一個字串是否包含在另一個字串中。 ES6 又提供了三種新方法。

- **includes()**：回傳布林值，表示是否找到了參數字串。
- **startsWith()**：回傳布林值，表示參數字串是否在來源字串的頭部。
- **endsWith()**：回傳布林值，表示參數字串是否在來源字串的尾部。

```javascript

var s = "Hello world!";

s.startsWith("Hello") // true
s.endsWith("!") // true
s.includes("o") // true

```

這三個方法都支援第二個參數，表示開始搜索的位置。

```javascript

var s = "Hello world!";

s.startsWith("world", 6) // true
s.endsWith("Hello", 5) // true
s.includes("Hello", 6) // false

```

上面程式碼表示，使用第二個參數n時，endsWith的行為與其他兩個方法有所不同。它針對前n個字元，而其他兩個方法針對從第n個位置直到字串結束。

## repeat()

repeat()回傳一個新字串，表示將原字串重復n次。

```javascript

"x".repeat(3) // "xxx"
"hello".repeat(2) // "hellohello"

```

## 正規表示式的y修飾符

除了修飾符號u，ES6還為正規表示式添加了y修飾符，叫做“粘連”（sticky）修飾符。它的作用與g修飾符類似，也是全局匹配，後一次匹配都從上一次匹配成功的下一個位置開始，不同之處在於，g修飾符只確保剩余位置中存在匹配，而y修飾符確保匹配必須從剩余的第一個位置開始，這也就是“粘連”的涵義。

```javascript

var s = "aaa_aa_a";
var r1 = /a+/g;
var r2 = /a+/y;

r1.exec(s) // ["aaa"]
r2.exec(s) // ["aaa"]

r1.exec(s) // ["aa"]
r2.exec(s) // null

```

上面程式碼有兩個正規表示式，一個使用g修飾符，另一個使用y修飾符。這兩個正規表示式各執行了兩次，第一次執行的時候，兩者行為相同，剩余字串都是“_aa_a”。由於g修飾沒有位置要求，所以第二次執行會回傳結果，而y修飾符要求匹配必須從頭部開始，所以回傳null。

如果改一下正規表示式，保證每次都能頭部匹配，y修飾符就會回傳結果了。

```javascript

var s = "aaa_aa_a";
var r = /a+_/y;

r.exec(s) // ["aaa_"]
r.exec(s) // ["aa_"]

```

上面程式碼每次匹配，都是從剩余字串的頭部開始。

進一步說，y修飾符號隱含了頭部匹配的標志&#710;。

```javascript

/b/y.exec("aba")
// null

```

上面程式碼由於不能保證頭部匹配，所以回傳null。y修飾符的設計本意，就是讓頭部匹配的標志&#710;在全局匹配中都有效。

與y修飾符相匹配，ES6的正則物件多了sticky屬性，表示是否設置了y修飾符。

```javascript

var r = /hello\d/y;
r.sticky // true 

```

## 模板字串

模板字串（template string）是增強版的字串，用反引號（`）標識。它可以當作普通字串使用，也可以用來定義多行字串，或者在字串中嵌入變量。

```javascript

// 普通字串
`In JavaScript '\n' is a line-feed.`

// 多行字串
`In JavaScript this is
 not legal.`

// 字串中嵌入變量
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

```

上面程式碼中的字串，都是用反引號表示。如果在模板字串中嵌入變量，需要將變量名寫在`${}`之中。

大括號內部可以進行運算，以及引用物件屬性。

```javascript

var x = 1;
var y = 2;

console.log(`${x} + ${y} = ${x+y}`) 
// "1 + 2 = 3"

console.log(`${x} + ${y*2} = ${x+y*2}`) 
// "1 + 4 = 5"

var obj = {x: 1, y: 2};
console.log(`${obj.x + obj.y}`)
// 3

```

如果模板字串中的變量沒有聲明，將報錯。

```javascript

var msg = `Hello, ${place}`;    
// throws error

```

模板字串使得字串與變量的結合，變得容易。下面是一個例子。

```javascript

if (x > MAX) {
	throw new Error(`Most ${MAX} allowed: ${x}!`);
	// 傳統寫法為'Most '+MAX+' allowed: '+x+'!'
}

```

模板字串可以緊跟在一個函數名後面，該函數將被調用來處理這個模板字串。

```javascript

var a = 5;
var b = 10;

tag`Hello ${ a + b } world ${ a * b}`;

```

上面程式碼中，模板字串前面有一個函數tag，整個表達式將回傳tag處理模板字串後的回傳值。

函數tag依次接受三個參數。第一個參數是一個數組，該數組的成員是模板字串中那些沒有變量替換的部分，也就是說，變量替換只發生在數組的第一個成員與第二個成員之間、第二個成員與第三個成員之間，以此類推。第一個參數之後的參數，都是模板字串各個變量被替換後的值。 

- 第一個參數：['Hello ', ' world ']
- 第二個參數: 15
- 第三個參數：50

```javascript

var a = 5;
var b = 10;

function tag(s, v1, v2) {
  console.log(s[0]); // "Hello "
  console.log(s[1]); // " world "
  console.log(v1);  // 15
  console.log(v2);  // 50

  return "OK";
}

tag`Hello ${ a + b } world ${ a * b}`;
// "OK"

```

下面是一個更復雜的例子。

```javascript

var total = 30;
var msg = passthru`The total is ${total} (${total*1.05} with tax)`;

function passthru(literals) {
  var result = "";
  var i = 0;
        
  while (i < literals.length) {
    result += literals[i++];
    if (i < arguments.length) {
      result += arguments[i];
    }
  }

  return result;

}

msg
// "The total is 30 (31.5 with tax)"

```

上面這個例子展示了，如何將各個參數按照原來的位置拼合回去。

處理函數的第一個參數，還有一個raw屬性。它也是一個數組，成員與處理函數的第一個參數完全一致，唯一的區別是字串被轉義前的原始格式，這是為了模板函數處理的方便而提供的。

```javascript

tag`First line\nSecond line`

```

上面程式碼中，tag函數的第一個參數是一個數組`["First line\nSecond line"]`，這個數組有一個raw屬性，等於`["First line\\nSecond line"]`，兩者唯一的區別就是斜杠被轉義了。

function tag(strings) {
  console.log(strings.raw[0]); 
  // "First line\\nSecond line"
}

## String.raw()

String.raw方法，往往用來充當模板字串的處理函數，回傳字串被轉義前的原始格式。

```javascript

String.raw`Hi\n${2+3}!`;
// "Hi\\n5!"

String.raw`Hi\u000A!`;
// 'Hi\\u000A!'

```

String.raw方法也可以正常的函數形式使用。這時，它的第一個參數，應該是一個具有raw屬性的物件，且raw屬性的值應該是一個數組。

```javascript

String.raw({ raw: 'test' }, 0, 1, 2);
// 't0e1s2t'


// 等同於
String.raw({ raw: ['t','e','s','t'] }, 0, 1, 2);

```
