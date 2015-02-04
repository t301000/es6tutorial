# ECMAScript 6 簡介

ECMAScript 6（以下簡稱 ES6 ）是 JavaScript 語言的下一代標準，正在快速開發當中，大部分都已經完成了，預計將在 2015 年 6 月正式發布。 Mozilla 公司將在這個標準的基礎上，推出 JavaScript 2.0 。

ES6 的目標，是使得 JavaScript 語言可以用來編寫大型且復雜的應用程式，成為企業級開發語言。

## ECMAScript 和 JavaScript 的關系

ECMAScript 是 JavaScript 語言的國際標準， JavaScript 是 ECMAScript 的實現。

1996 年 11 月， JavaScript 的創造者 Netscape 公司，決定將 JavaScript 提交給國際標準化組織 ECMA ，希望這種語言能夠成為國際標準。次年， ECMA 發布 262 號標準文件（ ECMA-262 ）的第一版，規定了瀏覽器腳本語言的標準，並將這種語言稱為 ECMAScript。這個版本就是 ECMAScript 1.0 版。

之所以不叫 JavaScript ，有兩個原因。一是商標， Java 是 Sun 公司的商標，根據授權協議，只有 Netscape 公司可以合法地使用 JavaScript 這個名字，且 JavaScript 本身也已經被 Netscape 公司註冊為商標。二是想體現這門語言的制定者是 ECMA ，不是 Netscape ，這樣有利於保證這門語言的開放性和中立性。因此，ECMAScript 和 JavaScript 的關系是，前者是後者的規格，後者是前者的一種實現。在日常場合，這兩個詞是可以互換的。

## ECMAScript 的歷史

1998 年 6 月， ECMAScript 2.0 版發布。

1999 年 12 月， ECMAScript 3.0 版發布，成為 JavaScript 的通用標準，得到了廣泛支持。

2007 年 10 月， ECMAScript 4.0 版草案發布，對 3.0 版做了大幅升級，預計次年 8 月發布正式版本。草案發布後，由於 4.0 版的目標過於激進，各方對於是否通過這個標準，產生了嚴重的分歧。以 Yahoo 、 Microsoft 、 Google 為首的大公司，反對 JavaScript 的大幅升級，主張小幅改動；以 JavaScript 創造者 Brendan Eich 為首的 Mozilla 公司，則堅持當前的草案。

2008 年 7 月，由於對於下一個版本應該包括哪些功能，各方分歧太大，爭論過於激進， ECMA 開會決定，中止 ECMAScript 4.0 的開發，將其中涉及現有功能改善的一小部分，發布為 ECMAScript 3.1 ，而將其他激進的設想擴大範圍，放入以後的版本，由於會議的氣氛，該版本的專案代號命名為 Harmony （和諧）。會後不久， ECMAScript 3.1 就改名為 ECMAScript 5 。

2009 年 12 月， ECMAScript 5.0 版正式發布。 Harmony 專案則一分為二，一些較為可行的想法命名為 JavaScript.next 繼續開發，後來演變成 ECMAScript 6 ；一些不是很成熟的想法，則被視為 JavaScript.next.next ，在更遠的將來再考慮推出。

2011 年 6 月， ECMAscript 5.1 版發布，並且成為 ISO 國際標準（ ISO/IEC 16262:2011 ）。

2013 年 3 月， ECMAScript 6 草案凍結，不再增加新功能。新的功能想法將被放到 ECMAScript 7 。

2013 年 12 月， ECMAScript 6 草案發布。然後是 12 個月的討論期，聽取各方的回饋。

2015 年 6 月， ECMAScript 6 預計將發布正式版本。

ECMA 的第 39 號技術專家委員會（ Technical Committee 39 ，簡稱 TC39 ）負責制定 ECMAScript 標準，成員包括 Microsoft 、 Mozilla 、 Google 等大公司。 TC39 的整體考量是： ES5 與 ES3 基本上保持相容性，而較大的語法修正和新功能的加入，將由 JavaScript.next 完成。目前 JavaScript.next 指的是 ES6 ，當第六版發布以後，則指為 ES7 。 TC39 認為， ES5 會在 2013 年的年中成為 JavaScript 開發的主流標準，並在今後五年中一直保持這個位置。

## 部署進度

由於 ES6 還沒有定案，有些語法規則還會再變動，目前支援 ES6 的軟體和開發環境還不多。各大瀏覽器的最新版本對 ES6 的支援可以查閱 [kangax.github.io/es5-compat-table/es6/](http://kangax.github.io/es5-compat-table/es6/) 。

Google 公司的 V8 引擎已經部署了 ES6 的部分特性。使用 Node.js 0.11 版就可以體驗這些特性。

Node.js 的 0.11 版還不是穩定版本，要使用版本管理工具 [nvm](https://github.com/creationix/nvm) 切換。下載 nvm 以後，進入專案目錄，執行下面的指令。

```bash

$ source nvm.sh
$ nvm use 0.11
$ node --harmony

```

啟動指令中的 `--harmony` 選項可以打開所有已經部署的 ES6 功能。使用下面的指令，可以查詢所有與 ES6 有關的單一選項。

```bash

$ node --v8-options | grep harmony

  --harmony_typeof
  --harmony_scoping
  --harmony_modules
  --harmony_symbols
  --harmony_proxies
  --harmony_collections
  --harmony_observation
  --harmony_generators
  --harmony_iteration
  --harmony_numeric_literals
  --harmony_strings
  --harmony_arrays
  --harmony_maths
  --harmony

```

## Traceur 編譯器

Google公司的 [Traceur](https://github.com/google/traceur-compiler) 編譯器，可以將 ES6 程式碼編譯為 ES5 的程式碼。這代表著，你可以用 ES6 的方式撰寫程式，又不用擔心瀏覽器是否支援。

它有多種使用方式：

**（1）直接插入網頁**

Traceur 允許將 ES6 程式直接插入網頁。

首先，必須在網頁頭部載入 Traceur 庫文件。

```javascript

<!-- 載入 Traceur 編譯器 -->
<script src="http://google.github.io/traceur-compiler/bin/traceur.js"
        type="text/javascript"></script>
<!-- 將 Traceur 編譯器用於網頁 -->
<script src="http://google.github.io/traceur-compiler/src/bootstrap.js"
        type="text/javascript"></script>
<!-- 打開實驗選項，否則有些特性可能編譯失敗 -->
<script>
        traceur.options.experimental = true;
</script>

```

接下來，就可以把 ES6 程式碼放在上面這些程式碼的下方。

```javascript

<script type="module">
	class Calc {
		constructor(){
			console.log('Calc constructor');
		}
		add(a, b){
			return a + b;
		}
	}

	var c = new Calc();
	console.log(c.add(4,5));
</script>

```

正常情況下，上面程式碼會在控制台顯示 9 。

注意， `script` 標籤的 `type` 屬性的值是 `module` ，而不是 `text/javascript` 。這是 Traceur 編譯器識別 ES6 程式碼的標示，編譯器會自動將所有 `type=module` 的程式碼編譯為 ES5 ，然後再交給瀏覽器執行。

如果 ES6 程式碼是一個外部文件，也可以用 `script` 標籤引入網頁。

```javascript

<script type="module" src="calc.js" >
</script>

```

**（2）線上轉換**

Traceur 提供一個[線上編譯器](http://google.github.io/traceur-compiler/demo/repl.html)，可以在線上將 ES6 程式碼轉為 ES5 程式碼。轉換後的程式碼可以直接當作 ES5 程式碼插入網頁執行。

上面的例子轉為 ES5 程式碼執行，結果如下：

```javascript

<script src="http://google.github.io/traceur-compiler/bin/traceur.js"
        type="text/javascript"></script>
<script src="http://google.github.io/traceur-compiler/src/bootstrap.js"
        type="text/javascript"></script> 
<script>
        traceur.options.experimental = true;
</script>
<script>
$traceurRuntime.ModuleStore.getAnonymousModule(function() {
	"use strict";

	var Calc = function Calc() {
		console.log('Calc constructor');
	};

	($traceurRuntime.createClass)(Calc, {add: function(a, b) {
		return a + b;
	}}, {});

	var c = new Calc();
	console.log(c.add(4, 5));
	return {};
});
</script>

```

**（3）終端機轉換**

當作終端機工具使用時， Traceur 是一個 node.js 的模組，首先需要用 npm 安裝。

```bash

$ npm install -g traceur

```

安裝成功後，就可以在終端機下使用 traceur 了。

traceur 直接執行 ES6 腳本檔案，會在標準輸出顯示執行結果，以前面的 calc.js 為例：

```bash

$ traceur calc.js
Calc constructor
9

```

如果要將 ES6 腳本轉為 ES5 存擋，要採用下面的寫法：

```bash

$ traceur --script calc.es6.js --out calc.es5.js 

```

上面程式碼的 `--script` 選項表示指定輸入檔案， `--out` 選項表示指定輸出檔案。

為了防止有些特性編譯不成功，最好加上 `--experimental` 選項。

```bash

$ traceur --script calc.es6.js --out calc.es5.js --experimental

```

終端機下轉換的檔案，就可以放到瀏覽器中執行。

**（4）Node.js 環境的用法**

Traceur 的 Node.js 用法如下（假設已安裝 traceur 模組）。

```javascript

var traceur = require('traceur');
var fs = require('fs');

// 將 ES6 腳本轉為字串
var contents = fs.readFileSync('es6-file.js').toString();

var result = traceur.compile(contents, {
  filename: 'es6-file.js',
  sourceMap: true,
  // 其他設定
  modules: 'commonjs'
});

if (result.error)
  throw result.error;

// result 物件的 js 屬性就是轉換後的 ES5 程式碼  
fs.writeFileSync('out.js', result.js);
// sourceMap 屬性對應 map 檔案
fs.writeFileSync('out.js.map', result.sourceMap);

```

## ECMAScript 7

2013 年 3 月， ES6 的草案封閉，不再接受新功能了。新的功能將被加入 ES7 。

ES7 可能包括的功能有：

（1）**Object.observe** ：用來監聽物件（以及陣列）的變化。一旦監聽的對象發生變化，就會觸發回乎函式。

（2）**Multi-Threading** ：支援多線程。目前， Intel 和 Mozilla 有一個共同的研究項目 RiverTrail ，致力於讓 JavaScript 多線程執行。預計這個項目的研究成果會被納入 ECMAScript 標準。

（3）**Traits** ：它將是“類別”功能（ class ）的一個替代。通過它，不同的物件可以共享同樣的特性。

其他可能包括的功能還有：更精確的數值計算、改善的記憶體回收、增強的跨站點安全、類型化的更貼近硬體的低等級操作、國際化支援（Internationalization Support）、更多的資料結構等等。
