# 物件的擴增

## 屬性的簡潔表示法

ES6 允許直接寫入變數和函式，作為物件的屬性和方法。這樣的撰寫更加簡潔。

```javascript

function f( x, y ) {
  return { x, y };
}

// 等同於

function f( x, y ) {
  return { x: x, y: y };
}

```

上面是屬性簡寫的例子，方法也可以簡寫。

```javascript

var o = {
  method() {
    return "Hello!";
  }
};

// 等同於

var o = {
  method: function() {
    return "Hello!";
  }
};

```

下面是一個更實際的例子。

```javascript

var Person = {

  name: '張三',

  // 等同於 birth: birth
  birth,

  // 等同於 hello: function ()...
  hello() { console.log('我的名字是', this.name); }

};

```

這種寫法用於函式的回傳值會非常方便。

```javascript

function getPoint() {
  var x = 1;
  var y = 10;

  return {x, y};
}

getPoint()
// {x:1, y:10}

```

## 屬性名表達式

JavaScript 語言定義物件的屬性，有兩種方法。

```javascript

// 方法一
obj.foo = true;

// 方法二
obj['a'+'bc'] = 123;

```

上方程式碼的方法一是直接用標示符號作為屬性名，方法二是用表達式作為屬性名稱，這時要將表達式放在方括號之內。

但是，如果使用字面量方式定義物件（使用大括號），在 ES5 中只能使用方法一（標示符號）定義屬性。

```javascript

var obj = {
  foo: true,
  abc: 123
};

```

ES6 允許字面量定義物件時，用方法二（表達式）作為物件的屬性名稱，即把表達式放在方括號內。

```javascript

let propKey = 'foo';

let obj = {
   [propKey]: true,
   ['a'+'bc']: 123
};

```

下面是另一個例子。

```javascript

var lastWord = "last word";

var a = {
    "first word": "hello",
    [lastWord]: "world"
};

a["first word"] // "hello"
a[lastWord]     // "world"
a["last word"]  // "world"

```

表達式還可以用於定義方法名稱。

```javascript

let obj = {
  ['h'+'ello']() {
    return 'hi';
  }
};

console.log(obj.hello()); // hi

```

## Object.is()

Object.is() 用來比較兩個值是否嚴格相等。它與嚴格比較運算元（===）的行為基本上一致，不同之處只有兩個：一是 +0 不等於 -0，二是 NaN 等於自身。

```javascript

+0 === -0   //true
NaN === NaN // false

Object.is(+0, -0)   // false
Object.is(NaN, NaN) // true

```

ES5 可以通過下面的程式碼，部署 Object.is()。

```javascript

Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 針對 +0 不等於 -0 的情況
      return x !== 0 || 1 / x === 1 / y;
    }
    // 針對 NaN 的情況
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false,
  writable: true
});

```

## Object.assign()

Object.assign 方法用來將來源物件（source）的所有可枚舉屬性，複製到目標物件（target）。它至少需要兩個物件作為參數，第一個參數是目標物件，後面的參數都是來源物件。只要有一個參數不是物件，就會拋出 TypeError 錯誤。

```javascript

var target = { a: 1 };

var source1 = { b: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}

```

注意，如果目標物件與來源物件有同名屬性，或多個來源物件有同名屬性，則後面的屬性會覆蓋前面的屬性。

```javascript

var target = { a: 1, b: 1 };

var source1 = { b: 2, c: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}

```

assign 方法有很多用處。

**（1）為物件增加屬性**

```javascript

class Point {
  constructor(x, y) {
    Object.assign(this, {x, y});
  }
}

```

上面方法通過 assign 方法，將 x 屬性和 y 屬性增加到 Point 類別的物件實例。

**（2）為物件增加方法**

```javascript

Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同於下面的寫法
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};

```

上面程式碼使用了物件屬性的簡潔表示法，直接將兩個函式放在大括號中，再使用 assign 方法增加到SomeClass.prototype 之中。

**（3）複製物件**

```javascript

function clone(origin) {
  return Object.assign({}, origin);
}

```

上面程式碼將原始物件複製到一個空物件，就得到了相同於原始物件的物件。

不過，採用這種方法複製，只能複製原始物件自身的值，不能複製它繼承的值。如果想要保持繼承鏈，可以採用下方的程式碼。

```javascript

function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}

```

**（4）為屬性指定預設值**

```javascript

const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  let options = Object.assign({}, DEFAULTS, options);
}

```

上方程式碼中，DEFAULTS 物件是預設值，options 物件是使用者提供的參數。assign 方法將DEFAULTS 和 options 合併成一個新物件，如果兩者有同名屬性，則 option 的屬性值會覆蓋 DEFAULTS 的屬性值。

## \_\_proto__ 屬性，Object.setPrototypeOf()，Object.getPrototypeOf()

**（1）\_\_proto__ 屬性**

\_\_proto__ 屬性用來讀取或設定目前物件的 prototype 物件。該屬性一度被正式寫入 ES6 草案，但後來又被移除。目前，所有瀏覽器（包括 IE11）都部署了這個屬性。

```javascript

// es6 的寫法

var obj = {
  __proto__: someOtherObj,
  method: function() { ... }
}

// es5 的寫法

var obj = Object.create(someOtherObj);
obj.method = function() { ... }

```

有了這個屬性，實際上已經不需要通過 Object.create() 來產生新物件了。

**（2）Object.setPrototypeOf()**

Object.setPrototypeOf 方法的作用與 \_\_proto__ 相同，用來設定一個物件的 prototype 物件。它是 ES6 正式推薦的設定原型物件的方法。

```javascript

// 格式
Object.setPrototypeOf(object, prototype)

// 用法
var o = Object.setPrototypeOf({}, null);

```

該方法等同於下面的函式。

```javascript

function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}

```

**（3）Object.getPrototypeOf()**

該方法與 setPrototypeOf 方法為一組，用於讀取一個物件的 prototype 物件。

```javascript

Object.getPrototypeOf(obj)

```

## Symbol

ES6 引入了一種新的原始資料型態 Symbol，表示獨一無二的 ID。它透過 Symbol 函式產生。

```javascript

let symbol1 = Symbol();

typeof symbol
// "symbol"

```

上面程式碼中，變數 symbol1 就是一個獨一無二的 ID。typeof 運算元的結果說明變數 symbol1 是Symbol 資料型態，而不是字串之類的其他類型。

Symbol 函式可以接受一個字串作為參數，表示 Symbol 實例的名稱。

```javascript

var mySymbol = Symbol('Test');

mySymbol.name
// Test

```

上方程式碼說明 Symbol 函式的字串參數，用來指定產生的 Symbol 的名稱，可以通過 name 屬性讀取。之所以要新增 name 屬性，是因為鍵名是 Symbol 類型，而有些場合需要一個字串類型的值來指代這個鍵。

注意，Symbol 函式前不能使用 new 指令，否則會出現錯誤。這是因為產生的 Symbol 是一個原始類型的值，不是物件。

Symbol 類型的值不能與其他類型的值進行運算，會出現錯誤。

```javascript

var sym = Symbol('My symbol');
'' + sym
// TypeError: Cannot convert a Symbol value to a string

```

但是，Symbol 類型的值可以轉為字串。

```javascript

String(sym)
// 'Symbol(My symbol)'

sym.toString()
// 'Symbol(My symbol)'

```

symbol 的最大特性，就是每一個 Symbol 都是不相等的，保證產生一個獨一無二的值。

```javascript

let w1 = Symbol();
let w2 = Symbol();
let w3 = Symbol();

w1 === w2 // false
w1 === w3 // false
w2 === w3 // false

function f(w) {
  switch (w) {
    case w1:
      ...
    case w2:
      ...
    case w3:
      ...
  }
}

```

上方程式碼中，w1、w2、w3 三個變數都等於 `Symbol()`，但是它們的值是不相等的。

由於這種特點，Symbol 類型適合作為標示符號，用於物件的屬性名，保證了屬性名之間不會發生衝突。如果一個物件由多個模組構成，這樣就不會出現同名的屬性，也防止了鍵值被不小心改寫或覆蓋。Symbol 類型還可以用於定義一組常數，防止它們的值發生衝突。

```javascript

var mySymbol = Symbol();

// 第一種寫法
var a = {};
a[mySymbol] = 'Hello!';

// 第二種寫法
var a = {
   [mySymbol]: 123
};

// 第三種寫法
var a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

a[mySymbol] // "Hello!"

```

上方程式碼通過方括號結構和 Object.defineProperty 兩種方法，將物件的屬性名稱指定為一個 Symbol 值。

注意，不能使用點結構將 Symbol 值作為物件的屬性名稱。

```javascript

var a = {};
var mySymbol = Symbol();

a.mySymbol = 'Hello!';

a[mySymbol] // undefined

```

上方程式碼中，mySymbol 屬性的值為未定義，原因在於 `a.mySymbol` 這樣的寫法並不是把一個 Symbol 值當作屬性名，而是把 mySymbol 這個字串當作屬性名進行賦值，這是因為點結構中的屬性名稱永遠都是字串。

下方的寫法為 Map 結構增加了一個成員，但是該成員永遠無法被引用。

```javascript

let a = Map();
a.set(Symbol(), 'Noise');
a.size // 1

```

為 Symbol 函式增加一個參數就可以引用了。

```javascript

let a = Map();
a.set(Symbol('my_key'), 'Noise');

```

如果要在物件內部使用 Symbol 屬性名稱，必須使用屬性名稱表達式。

```javascript

let specialMethod = Symbol();

let obj = {
  [specialMethod]: function (arg) { ... }
};

obj[specialMethod](123);

```

採用加強的物件寫法，上方程式碼的 obj 物件可以寫得更簡潔一點。

```javascript

let obj = {
  [specialMethod](arg) { ... }
};

```

Symbol 類型作為屬性名稱，不會出現在 for...in 迴圈中，也不會被 Object.keys()、Object.getOwnPropertyNames() 回傳，但是有一個對應的 Object.getOwnPropertySymbols 方法，以及 Object.getOwnPropertyKeys 方法都可以取得 Symbol 屬性名稱。

```javascript

var obj = {};

var foo = Symbol("foo");

Object.defineProperty(obj, foo, {
    value: "foobar",
});

Object.getOwnPropertyNames(obj)
// []

Object.getOwnPropertySymbols(obj)
// [Symbol(foo)]

```

上方程式碼中，使用 Object.getOwnPropertyNames 方法得不到 Symbol 屬性名稱，需要使用 Object.getOwnPropertySymbols 方法。

Reflect.ownKeys 方法回傳所有類型的鍵名。

```javascript

let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};

Reflect.ownKeys(obj)
// [Symbol(my_key), 'enum', 'nonEnum']

```

## Proxy

Proxy 用於修改某些操作的預設行為，等同於在語言層面做出修改，所以屬於一種“元編程”（meta programming），即對編程語言進行編程。

Proxy 可以理解成在目標物件之前，架設一層“攔截”，外界對該物件的存取，都必須先通過這層攔截，因此提供了一種機制，可以對外界的存取進行過濾和改寫。proxy 這個詞的原意是代理，用在這裡表示由它來“代理”某些操作。

ES6 原生提供 Proxy 建構函式，用來產生 Proxy 實例。

```javascript

var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

proxy.time // 35
proxy.name // 35
proxy.title // 35

```

作為建構函式，Proxy 接受兩個參數。第一個參數是所要代理的目標物件（上例是一個空物件），意即如果沒有 Proxy 的介入，操作原來要存取的就是這個物件；第二個參數是一個設定物件，對於每一個被代理的操作，需要提供一個對應的處理函式，該函式將攔截對應的操作。比如，上方程式碼中，設定物件有一個 get 方法，用來攔截對目標物件屬性的存取請求。get 方法的兩個參數分別是目標物件和所要存取的屬性。可以看到，由於攔截函式總是回傳 35，所以存取任何屬性都得到 35。

注意，要使 Proxy 產生作用，必須針對 Proxy 實例（上例是 proxy 物件）進行操作，而不是針對目標物件（上例是空物件）進行操作。

Proxy 實例也可以作為其他物件的原型物件。

```javascript

var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

let obj = Object.create(proxy);

obj.time // 35

```

上方程式碼中，proxy 物件是 obj 物件的原型，obj 物件本身並沒有 time 屬性，所以根據原型鏈，會在 proxy 物件上讀取該屬性，導致被攔截。

對於沒有設定攔截的操作，則直接落在目標函式上，按照原先的方式產生結果。

下面是另一個攔截讀取操作的例子。

```javascript

var person = {
  name: "張三"
};

var proxy = new Proxy(person, {
  get: function(target, property) {
    if (property in target) {
      return target[property];
    } else {
      throw new ReferenceError("Property \"" + property + "\" does not exist.");
    }
  }
});

proxy.name // "張三"
proxy.age  // 拋出一個錯誤

```

上方程式碼說明了，如果存取目標物件不存在的屬性，就會拋出一個錯誤。如果沒有這個攔截函式，存取不存在的屬性，只會回傳 undefined。

除了存取函式 get，Proxy 還可以設定存取函式 set，用來攔截某個屬性的賦值行為。假定 Person 物件有一個 age 屬性，該屬性應該是一個不大於 200 的整數，那麼可以使用 Proxy 物件保證 age 的屬性值符合要求。

```javascript

let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }

    // 對於 age 以外的屬性，直接儲存
    obj[prop] = value;
  }
};

let person = new Proxy({}, validator);

person.age = 100;

person.age           // 100
person.age = 'young' // 出現錯誤
person.age = 300     // 出現錯誤

```

上方程式碼中，由於設定了存取函式 set，任何不符合要求的 age 屬性賦值，都會拋出一個錯誤。利用 set 方法，還可以資料綁定，即每當物件發生變化時，會自動更新DOM。

ownKeys 方法用來攔截 Object.keys() 操作。

```javascript

let target = {};

let handler = {
  ownKeys(target) {
    return ['hello', 'world'];
  }
};

let proxy = new Proxy(target, handler);

Object.keys(proxy)
// [ 'hello', 'world' ]

```

上方程式碼攔截了對於 target 物件的 Object.keys() 操作，回傳預先設定的陣列。

Proxy 支援的攔截操作一覽。

- defineProperty(target, propKey, propDesc)：回傳一個布林值，攔截 Object.defineProperty(proxy, propKey, propDesc）
- deleteProperty(target, propKey) ：回傳一個布林值，攔截 delete proxy[propKey]
- enumerate(target)：回傳一個迭代器，攔截 for (x in proxy)
- get(target, propKey, receiver)：回傳類型不限，攔截物件屬性的讀取
- getOwnPropertyDescriptor(target, propKey) ：回傳屬性的描述物件，攔截 Object.getOwnPropertyDescriptor(proxy, propKey)
- getPrototypeOf(target) ：回傳一個物件，攔截 Object.getPrototypeOf(proxy)
- has(target, propKey)：回傳一個布林值，攔截 propKey in proxy
- isExtensible(target)：回傳一個布林值，攔截 Object.isExtensible(proxy)
- ownKeys(target)：回傳一個陣列，攔截 Object.getOwnPropertyPropertyNames(proxy)、Object.getOwnPropertyPropertySymbols(proxy)、Object.keys(proxy)
- preventExtensions(target)：回傳一個布林值，攔截 Object.preventExtensions(proxy)
- set(target, propKey, value, receiver)：回傳一個布林值，攔截物件屬性的設定
- setPrototypeOf(target, proto)：回傳一個布林值，攔截 Object.setPrototypeOf(proxy, proto)

如果目標物件是函式，那麼還有兩種額外操作可以攔截。

- apply 方法：攔截 Proxy 實例作為函式呼叫的操作，例如 proxy(···)、proxy.call(···)、proxy.apply(···)。
- construct 方法：攔截 Proxy 實例作為建構函式呼叫的操作，比如 new proxy(···)。

Proxy.revocable 方法回傳一個可取消的 Proxy 實例。

```javascript

let target = {};
let handler = {};

let {proxy, revoke} = Proxy.revocable(target, handler);
    
proxy.foo = 123;
proxy.foo // 123
    
revoke();
proxy.foo // TypeError: Revoked

```

Proxy.revocable 方法回傳一個物件，該物件的 proxy 屬性是 Proxy 實例，revoke 屬性是一個函式，可以取消 Proxy 實例。上方程式碼中，當執行 revoke 函式之後，再存取 Proxy 實例，就會拋出一個錯誤。

## Object.observe()，Object.unobserve()

Object.observe 方法用來監聽物件（以及陣列）的變化。一旦監聽物件發生變化，就會觸發回呼函式。

```javascript

var user = {};
Object.observe(user, function(changes){    
  changes.forEach(function(change) {
    user.fullName = user.firstName+" "+user.lastName;         
  });
});

user.firstName = 'Michael';
user.lastName = 'Jackson';
user.fullName // 'Michael Jackson'

```

上方程式碼中，Object.observer 方法監聽 user 物件。一旦該物件發生變化，就自動產生 fullName 屬性。

一般情況下，Object.observe 方法接受兩個參數，第一個參數是監聽的物件，第二個參數是一個回呼函式。一旦監聽物件發生變化（例如新增或刪除一個屬性），就會觸發這個回呼函式。很明顯，利用這個方法可以做很多事情，例如自動更新 DOM。

```javascript

var div = $("#foo");

Object.observe(user, function(changes){    
  changes.forEach(function(change) {
    var fullName = user.firstName+" "+user.lastName;         
    div.text(fullName);
  });
});

```

上方程式碼中，只要 user 物件發生變化，就會自動更新 DOM。如果配合 jQuery 的 change 方法，就可以實作資料物件與 DOM 物件的雙向自動綁定。

回呼函式的 changes 參數是一個陣列，代表物件發生的變化。下方是一個更完整的例子。

```javascript

var o = {};

function observer(changes){
    changes.forEach(function(change) {
		console.log('發生變動的屬性：' + change.name);
		console.log('變動前的值：' + change.oldValue);
		console.log('變動後的值：' + change.object[change.name]);
		console.log('變動類型：' + change.type);
    });
}

Object.observe(o, observer);

```

參照上面程式碼，Object.observe 方法指定的回呼函式，接受一個陣列（changes）作為參數。該陣列的成員與物件的變化一一對應，也就是說，物件發生多少個變化，該陣列就有多少個成員。每個成員是一個物件（change），它的 name 屬性表示發生變化來源物件的屬性名，oldValue 屬性表示發生變化前的值，object 屬性指向變動後的來源物件，type 屬性表示變化的種類。基本上，change 物件是下面的樣子。

```javascript

var change = {
  object: {...}, 
  type: 'update', 
  name: 'p2', 
  oldValue: 'Property 2'
}

```

Object.observe 方法目前共支援監聽六種變化。

- add：增加屬性
- update：屬性值的變化
- delete：刪除屬性
- setPrototype：設定原型
- reconfigure：屬性的 attributes 物件發生變化
- preventExtensions：物件被禁止擴增（當一個物件變得不可擴增時，也就不必再監聽了）

Object.observe 方法還可以接受第三個參數，用來指定監聽的事件種類。

```javascript

Object.observe(o, observer, ['delete']);

```

上方的程式碼表示，只在發生 delete 事件時，才會呼叫回呼函式。

Object.unobserve 方法用來取消監聽。

```javascript

Object.unobserve(o, observer);

```

注意，Object.observe 和 Object.unobserve 這兩個方法不屬於 ES6，而是屬於 ES7 的一部分。不過，Chrome 瀏覽器從第 33 版起就已經支援。
