---
title: Functional Programming 入門 in JavaScript
---

本文會試著用最簡單的方式來幫助你入門 functional programming。

## 什麼是 Functional Programming

其實定義有很多種。簡單的可以是「只要有用到 first class function 就算」，而困難的可以到「必須使用 mathematical function 才算」。而這邊我們採用的定義是：

1. 用到 first class function
2. 盡量避免 side effect

## 什麼是 First Class Function?

原本的 JS function 都是這麼定義的

```javascript
function functionName(parameter) {
  // function body
  return something;
}
```

有了 function 當然就要用：

```javascript
const retValue = functionName(123);
```

以上就是基本的 function 慨念。那什麼是 first class function 呢？

寫過 JS 一定會看過這種東西

```javascript
$('button').onClick(() => {
  console.log('this btn got clicked');
});
```

這邊定義了一個匿名函數，然後塞給 `onClick` 當參數，這就是一種 first class function 的使用範例。差異是：**「在任何可以放 value 的地方你都可以放一個匿名函數。」** 這也是為什麼叫做 first class 的原因。

### Higher Order Function

所謂的 higher order 指的是 function 除了可以「接受/回傳 value」還可以「接受/回傳 function」。其中一個例子就是上面的 button onClick，它的參數是一個匿名函數。這邊再舉另外一個例子：

```javascript
const add = (a) => {
    return (b) => a + b;
};
```

在這個例子裡 add function 會接受一個參數 a 然後回傳一個 function。
這麼做的好處是你可以做 partial parameter apply：

```javascript
const add3 = add(3)
console.log(add3(4)) // 7
```

不過雖然這 Higher Order 聽起來是個很厲害的形容詞，不過其實跟「first class function」、「function as value」其實是同樣的意思。因為如果把 function 當作是一般的 value，那就沒有必要特別區分 higher order function 跟 first order function 了。

## 盡量避免 side effect

什麼是 side effect 呢？讓我們來直接看看例子吧！以下都是 side effect：

- 讀寫檔案，例如使用 readFile
- 印東西，例如使用：console.log
- 修改「外部」變數

最後一個就比較有趣一點了，因為必須討論什麼是「外部」：

```javascript
let a = 3;
function f() {
    a = 4;
    let b = 5;
    b++;
    return b;
};
```

以上面的例子來說，f function 做了一種 side effect：「修改外部變數 a 」，不過修改 b 可以不算 side effect，因為它的生命週期就只侷限在 function f 裡面而已。

我們也可以很簡單的直接把所有的變數修改都看成是  side effect。

### 為什麼要避免 side effect？

假設我們有個 function f 會把外部變數 a 加ㄧ，然後有另外一個 function g，但你其實不知道 g 的實作是什麼：

```javascript
let a = 3
function f() {
    a = a + 1;
}

function g() {
    // don't know the detail
}

g();
```

請問：在這個程式執行後，你能知道 a 變成多少嗎？答案是不行，因為你不會知道 g 有沒有用到 f。

但是如果 f 一開始就沒有修改 a，那 g 怎麼使用 f 都不會影響 a。

也就是如果有 side effect 那這個程式就會變得相對難以理解。而只要一個 function 沒有 side effect 我們可以稱它為 pure function。而 pure function 除了比較好理解以外，也可以提高 reuseability, testability 等等等等。接下來都會提到。

## 那麼怎麼避免 side effect 呢？

很簡單：don't do it（無誤

其實這個就是大多數人入門 FP 碰到的問題，也就是這篇教學想要幫助大家的地方。

接下來讓我們來看看怎麼用 first order function 來幫助我們避免 side effect。

## Avoid side effect: Basic

讓我們從最基本的 array operation 來舉例。

### Example 1: Map an Array

假設我們要把一個 array of number 中的所有數字都平方，那麼使用變數的話通常會這麼做：

```javascript
let origin = [1, 2, 3];
let squared = [];
for (let n of origin) {
  squared.push(n * n);
}
```

push function 會修改 squared 的內容，所以是一種 side effect。

而在 FP 的話通常會這麼做

```javascript
const squared2 = origin.map(n => n * n);
```

使用 map function 就不需要使用 push 這個 function。

### Example 2: Filter an Array

給定一個數字陣列，如果要把所有整正數抓出來，那一般會這麼做：

```javascript
let origin = [-1, 3, -7, 6, 9];
let positives = [];
for (let n of origin) {
    if (n >= 0) positives.push(n);
}
```

而 FP 通常都會提供 filter function 可以直接使用

```javascript
const positives = origin.filter(n => n >= 0);
```

### Example 3: Sum an Array

給定一個數字陣列，如果要把所有數字加總的話，一般會這麼做：

```javascript
let origin = [1, 2, 3];
let sum = 0;
for (let n of origin) {
    sum += n;
}
```

而在 FP 中通常會提供各種 helper function 所以

```javascript
sum(origin);
```

但如果沒有的話也可以用 `reduce`

```javascript
origin.reduce((acc, n) => acc + n, 0);
```

其中第一個參數是一個 aggregation function 他會把「當前的加總」跟「現在拿到的數字」加在一起。而第二個參數是「當前加總」的初始值。

它運作方式其實跟 loop 很像，也就是範例中的 `n` 就是每一次 iterate 時拿到的數字，而每次 iterate，它都會使用這個 aggregation function，就跟 for loop 的 body 做的事情一樣。

```javascript
origin.reduce(
    (acc, n) => acc + n, // 相當於 for loop body: sum += n;
    0 // 相當於 let sum = 0;
);
```

而因為它幾乎跟 loop 作用一樣，所以 loop 能做的它都能做，最主要的差別是：這中間完全沒有使用到變數。有興趣也可以試試看用 `reduce` 來實作 `map`, `filter`。


## Map, Filter 這些 function 是怎麼運作的？

雖然大多時候我們只需要知道怎麼使用這些 function ，但了解怎麼實做也是很有幫助的，尤其是當你碰到複雜的資料結構的時候。

最簡單的實作就是允許範圍內的 mutability：

```javascript
const map = (arr, f) => {
  let ret = [];
  for (let item of arr) {
      ret.push(f);
  }
  return ret;
}

console.log(map([1, 2, 3], add3)); // [4, 5, 6]
```

其結構非常簡單，還就只是透過一個 for loop 去把每個元素拿出來，然後 apply f function 後再塞回 ret。

雖然這樣有使用到「變數」，嚴格說起來這個實作並不能符合 FP。不過因為它並沒有修改外部變數，所以從「封裝」的角度來說，它用起來跟 pure function 沒有什麼差異，而因為效能考量所以真正的實作也會與此類似。

所以這邊有個有趣的但非必要的小作業：如何不使用變數來實作 map function 呢？

- 提示一：會使用到 array function: splice
- 提示二：會需要使用 recursion

如果覺得這個題目有點困難的話，可以先練習實作 sum function，而它用起來像這樣：

```javascript
const total = sum(1, 50); // 從 1 加到 50
```

當然不能用數學公式，也不能用變數喔～


## Avoid side effect: Inverse of Control

你可能聽過 Inverse of Control 這個詞，通常都是在 OOP/Design Pattern 相關的文章中看到的。但其實這個觀念在 FP 可以說是基礎中的基礎，因為不用這個觀念的話，很多 function 實作起來很麻煩。

### Write a test for setTimeout

假設你今天想要為你的程式寫測試，而一個程式用到了 `setTimeout`，好巧不巧你的 timeout 設定是一小時，如果不做什麼特殊處理的話每次跑測試都要一個小時，這肯定是無法接受的。

```javascript
function doSomething() {
    doFirstThing();
    setTimeout(doSecondThing, 60 * 60 * 1000);
}
```

這個時候有兩種做法：1. 用 mock libary 例如 `sinonjs`, 2. 用 Dependency Injection。

雖然前者是常見的方法，但是後者的泛用性更高，而且只需要匿名函數就能辦到。

setTimeout 是一個 side-effectful function ，我們可以用 Dependency Injection 來做動態替換：

```javascript
function doSomething(timeout) {
    doFirstThing();
    timeout(doSecondThing, 60 * 60 * 1000);
}
```

注意上面的 timeout 是 doSomething 的一個參數喔！

如此一來，在 test 就可以這樣寫：

```javascript
function test() {
    const myTimeout = (f, time) => f();
    doSomething(myTimeout);
}
```

myTimeout 會直接呼叫 f 也就是 doSecondThing，這樣就不需要等待一個小時。

而在 production 我們可以傳入 setTimeout 來達成原始邏輯：

```javascript
doSomething(setTimeout);
```

這樣的作法除了可以幫助我們寫測試以外，我們還可以替換不同的邏輯，例如假設有個特殊的需求是要把所有的 "timeout" 都減半，那有了 dependency injection 我們可以這麼做：

```javascript
const halfTimeout = (f, time) => {
    setTimeout(f, time / 2);
};
doSomething(halfTimeout);
```

這就呼應了我們前面講的：除了可以提高 testability，也可以提高 reuseability。

## FP 在前端的應用

雖然 UI 有跟 user 有高度互動的關係，所以會用各種變數來存整個 UI 的狀態 (UI State)，但是這並不代表 FP 的 pure 觀念不能使用在 UI。相反的它在 UI 可是非常重要的，其中以 React 這個 frontend library 為最常見。[官方文件](https://zh-hant.reactjs.org/docs/react-component.html#render)就直接寫上了「render() function 應該是 pure 的。」

也就是，正是因為 UI 有很多狀態需要管理，所以一但程式複雜起來，就必須要對狀態的數量做限制，只要是不必要的狀態都必須避免，而這也符合了 Single Source of Truth 的原則。

除了 React 以外，就連 Angular 這個原本著重於 view model binding 方法的也開始採用 RxJS 這種大量使用 FP 的 library。而且這個原則並不只局限於 web frontend，近幾年使用類似概念的其他 UI library 也出現在 mobile 平台上，像是 Android 的 Compose、iOS 的 SwiftUI。而且這兩者都有官方 support。


## 結語

其實入門 FP 並不困難，只要掌握 first class function 跟 pure function 的兩個觀念，並學會使用 map, filter, reduce 等常見 function，這樣就對 90% 的日常開發非常有幫助了。

雖然你可能會聽過有人說 Functor, Monad 對於理解 FP 來說很重要，但其實對日常開發來說通常不是必要的。多數狀況都單純的只是對於使用的 library 熟不熟悉罷了。

不過有趣的地方就在這裡，雖然入門 FP 其實非常容易，但正式因為基礎觀念少所以如何延伸就變得有趣許多。有興趣的話，學習 FP 絕對是最值得投資時間的技術之一。

