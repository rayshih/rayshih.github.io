---
title: 怎麼樣算是學會了 Functional Programming？
---

我在寫一篇入門教學文時，想到幾乎沒有人在問是不是學會 OOP 了，但好像有不少人不知道怎麼入門 FP。於是就在我的 [twitter](https://twitter.com/existential_ray/status/1419793240472100864) 跟 [facebook f2e.tw group](https://www.facebook.com/groups/f2e.tw/posts/4098364913534176/) 裡面問了這個問題「你們怎麼驗證一個人會不會 FP 了？」而大家的回答都滿有趣的。這邊就讓我來收集分類一下答案：（由簡單到難）

**注意：以下只是列出所有的提到的答案，除了 pure function 跟 first class function 以外都可以直接忽略。**

### Pure function

- Side effect

### First class function

- Curry
- Function Composition

### Common functions

- `fold`/`reduce`
    - use them as APIs
    - can do the implementation

### Complex construct

- Fixed point
- Y combinator
- Mutural recursive functions

### Computation Theory

- FP is turing complete

### Abstract Math

- Functor, Applicative, Monad
- Category Theory
- Yoneda Lemma

-----

## 我認為怎麼樣算是會 FP 了

我會用能不能實作 function composition 為測試標準，因為它可以測試對於 functions are first class citizen 的理解程度。甚至在這之上可以推導出 Monad。

簡單論述一下題目本身：實作一個 function `compose`，它的功能是：給定兩個 functions `f`, `g` 當參數，回傳一個新的 function `h`，然後它的作用等於產生以下的宣告

```javascript=
// by javascript
const h = compose(f, g);
// equals to
const h = (x) => f(g(x));
```

如果這題做得出來，對我來說就算會 FP 了。

當然會了不等於熟練了，所以以下有延伸題：

### 延伸題一

根據上題，如果參數不是固定的，那如何在不用變數的情況下，做出 multi-function composition？

```javascript=
// the same compose function, it can do following
const g = compose([f1, f2, f3])
// equals to
const g = x => f1(f2(f3(x)))
// and
const g = compose([f1, f2, f3, f4])
// equals to
const g = x => f1(f2(f3(f4(x))))
```

### 延伸題二

CPS 是指「不用 return 取得計算結果，改用 callback 來取得」，像這樣

```javascript=
const add1 = (a, callback) => callback(a + 1);

add1(3, ans => console.log(ans));
```

如果今天 `f`, `g` 都是 CPS 形式，那如何實作 `compose`？

## 回到「大家認為怎麼樣算是會 FP 」這個話題

包含延伸題，應該是 cover 第一到第三點，也就是會實作 fold/reduce，甚至包含了 Monad 的實作。剩下沒有 cover 到的：

- Fixed Point/Y combinator: 其作用是能在不支援 recursion 的語言中實作 recursion
- FP is turing complete: 白話來說就是證明 FP 可以做任何事情
- Monad/Category Theory/Yoneda Lemma: 用嚴謹數學理論來 model FP

儘管非常有趣，在大部分的情況下，這些在實務上都用不到，所以對我來說我不會用它們來評斷會不會 FP。

有興趣或是想學 FP 的可以試著自己寫寫看上面的題目，也歡迎留言或私訊我，有任何問題我有時間都會幫忙解答的。
