+++
title = "Lambda Calculus 簡介"
description = "對程式語言影響深遠的 lambda calculus"

[taxonomies]
categories = ["Programming Language"]
+++

## 1 Lambda Calculus

Lambda calculus 是由數學家 Alonzo Church 在 1930 年代提出的一套數學邏輯規則，在程式語言領域中有重要的地位，被視為是程式語言的雛形。它對許多函數式程式語言造成很大的影響，如第一個函數式程式語言 Lisp、純函數式程式語言 Haskell 等，此外 Haskell 的標誌中的 λ 就代表 lambda calculus。

![Haskell Logo](./haskell-logo.svg)

如果對於 Alan Turing 在相同年代提出的圖靈機有概念的話，lambda calculus 具有圖靈完備性，也就是說 lambda calculus 可以用來模擬圖靈機的運算。

---

## 2 Basics

我們首先定義 lambda calculus 的 syntax 以及 semantics。

Syntax（語法）指的是一門語言怎麼寫才是對的，semantics（語意）則是指語言的意義。在這裡，語言指的是 lambda calculus，程式語言領域中指的是程式語言本身，而在語言學中則是指自然語言。舉英文為例，句子的 syntax 可以是 S + V，其 semantics 為「S 做了動作 V」。

### 2.1 Syntax

最簡單的 lambda calculus 包含了三種 **terms**：

```
t ::= x     (variable)
      λx. t (abstraction)
      t t   (application)
```

其中 `t` 代表的是 **term**，它可以是：
1. 由任何符號代表的 **variable**。
2. 完整的函式定義 **abstraction**，包括了 `λ` 與 `.` 之間一個參數 `x`，以及 `.` 之後代表函式本體的 term `t`。
3. 將第二個 term 作為參數傳入第一個 term 的 **application**，類似於函式呼叫。

> `t` 是一個 metavariable，它不存在於 syntax 中，它只用於指涉 term，也就是說在 application term 中的兩個 `t` 可以是兩個不同的 terms。

### 2.2 Semantics

在最簡單的 lambda calculus 中沒有條件控制、迴圈等元素，計算方法只有將參數帶入函式而已，也就是將 abstraction 中的參數替換成右邊的 term：

```
(λx. t1) t2 → [x ↦ t2]t1
```

> `λx.` 後方會被視為一個整體，因此需要用括號限制 abstraction 的範圍。

其中 `→` 代表左邊的 term 經過一步 evaluation 會變成右邊的 term，而 `[x ↦ t2]t1` 代表將 `t1` 中所有的 `x` 替換為 `t2`。

> 這裡說的「將 `t1` 中所有的 `x` 替換為 `t2`」其實是不準確的說法，應該要說「將 `t1` 中所有的 **free variable** `x` 替換為 `t2`」，因為我們一般預期的行為是
> ```
> (λx. (λx. x) (x + 1)) n → (λx. x) (n + 1) → n + 1
> ```
> 而非
> ```
> (λx. (λx. x) (x + 1)) n → (λx. n) (n + 1) → n
> ```
> 所謂 free variable 簡單來說就是 `x` 不是任何 abstraction 的參數。在這個例子中，`t1 = (λx. x) (x + 1)`：
> - `(x + 1)` 的 `x` 是 `t1` 中的 **free variable**，因為這個 `x` 在 `t1` 中沒有對應的 `λx.`，所以根據 semantics 可以被替換成 `n`。
> - 而 `(λx. x)` 中的 `x` 就不是 free variable，而是 **bound variable**，因為它和旁邊的 `λx.` 綁定（bound）。

舉例而言，我們定義 identity function：

```
id = λx. x
```

從 syntax 的角度看，因為函式本體中的 `x`（第二個 `x`）是一個 variable，所以 `id` 是一個 abstraction。

接著我們透過 `id n` 觀察 semantics：
```
id n
= (λx. x) n
→ [x ↦ n]x
= n
```

`id` 確實是一個回傳參數本身的 identity function。

---

## 3 Programming

### 3.1 Multiple Arguments

Abstraction terms 中，`λ` 後面只能定義一個參數，如果要讓一個函式可以接收多個參數，則需要透過 [currying](https://zh.wikipedia.org/wiki/柯里化) 讓一個函式回傳另一個函式來達到多個參數的效果。舉例而言，我們要讓一個函數接收兩個參數並回傳第一個參數，可以這樣定義：

```
tru = λt. λf. t
```

展示使用的效果：

```
tru x y
= (λt. λf. t) x y
→ (λf. x) y          // t ↦ x
→ x                  // f ↦ y
```

當 `tru` 接收參數 `x` 後就會得到另一個函式 `(λf. x)`，接著這個函式會負責接收第二個參數 `y`。

同樣的我們也可以定義回傳第二個參數的函式：

```
fls = λt. λf. f
```

### 3.2 Church Booleans

以上介紹的兩個 term `tru` 和 `fls` 被稱為 **Church booleans**，相當於 lambda calculus 中的布林值。使用 `tru` 與 `fls` 命名的目的是避免和一般的布林值 `true` 與 `false` 搞混，Church booleans 實際上是 abstraction。

有了 `tru` 與 `fls` 後，我們可以將它們用於條件判斷，常見的 `if-then-else` 可以寫成接收三個參數的 term `test`：

```
test = λb. λt. λf. b t f
```

`test b t f` 的用法就相當於 `if b then t else f`，我們將 `tru` 或 `fls` 傳入作為第一個參數，然後傳入 then-branch 與 else-branch，如果 `b = tru`，那麼

```
test tru t f
→ tru t f
→ t
```

最後得到 `t`（`tru` 會回傳第一個參數！），反之若 `b = fls` 則會得到 `f`。

我們也可以定義邏輯運算：

```
and = λb. λc. b c fls
or = λb. λc. b tru c
```

這裡的定義和 short-circuit 的概念有異曲同工之妙，在 short-circuit 中，如果 `b` 是 `false` 那麼 `b and c` 就不可能是 `true` 了，所以可以直接回傳 `false`，在上方的定義中，如果 `b` 是 `fls`，因為 `fls` 會選擇第二個參數，所以不管 `c` 為何 `b c fls` 都會直接回傳 `fls`。Short-circuit 這種提前回傳結果的概念在大多的現代程式語言中都有實作，因此需要注意 `and` 及 `or` 的第二個參數可能會被忽略，尤其當第二個參數是函式時，會導致函式沒有被執行。

### 3.3 Pairs

在 lambda calculus 中如何表達資料結構呢？我們先看在函數式程式語言中最基本的資料結構 **pair**。

在函數式程式語言中，tuple（元組）是一個簡單的資料結構，語法通常是括號中放多個元素並以 `,` 隔開，例如 `(1, 3, 5, 7)`、`('h', 'e', 'l', 'l', 'o')`，而 pair 就是元素數量為 2 的 tuple。

最常與 pair 搭配使用的函式是 `first` 與 `second`，分別可以取出第一個與第二個元素，利用 Church booleans 我們可以為 pair 定義以下 terms：

```
pair = λf. λs. λb. b f s
first = λp. p tru
second = λp. p false
```

我們利用 `tru` 與 `fls` 能夠選擇第一個與第二個參數的特性，我們先把兩個元素放進 `pair` 中，要取出元素時再使用 `tru` 與 `fls` 取出想要的元素。例如我們可以將二維坐標 $(x, y)$ 儲存為：

```
pos = pair x y
    = (λf. λs. λb. b f s) x y
    → (λb. b x y)
```

現在只要傳入 `tru` 或 `fls` 給 `pos` 就可以取出 `x` 或 `y`，如果想要取出 `x` 座標就可以寫：

```
first pos
= (λp. p tru) (λb. b x y)
→ (λb. b x y) tru
→ tru x y
→ x
```

`first` 就僅僅是將 `tru` 傳給參數 pair `p`，然後取得第一個元素而已，`second` 則是傳 `fls` 來取得第二個元素。

### 3.4 Church Numerals

最後來看看如何在 lambda calculus 中表達自然數。這裡的概念和[皮亞諾公設](https://zh.wikipedia.org/zh-tw/皮亚诺公理)的概念相似，皮亞諾公設中提到兩點：
1. 0 是自然數。
2. 每一個確定的自然數 $a$，都有一個確定的後繼數（successor） $a'$ ，$a'$ 也是自然數。

在 lambda calculus 中，[Church numerals](https://en.wikipedia.org/wiki/Church_encoding#Church_numerals) 的定義頗有皮亞諾公設的感覺，我們需要給定 **0** 是什麼以及 successor 是什麼意思，所以 Church numerals 的定義如下：

```
c0 = λs. λz. z
c1 = λs. λz. s z
c2 = λs. λz. s (s z)
c3 = λs. λz. s (s (s z))
...
```

這裡變數名稱 `s` 代表 successor 而 `z` 代表 zero，`cn` 則是為了避免和一般的數字混淆。

可以看到每個 church numeral 只要給定了 successor 和 0 的定義，那麼它就是 well-defined。0 是 `z`、1 是 `z` 的下一個元素、2 則是 `z` 的下一個再下一個元素，依此類推。

這裡有個重要的觀察，如果換個角度想，`cn` 的作用其實就是接收一個函式 `s` 和一個元素 `z`，對 `z` 呼叫 `n` 次 `s`，也就是 $s^n(z)$。

接著介紹幾個常用於 Church numerals 的函式：

```
succ = λn. λs. λz. s (n s z)
plus = λm. λn. λs. λz. m s (n s z)
times = λm. λn. m (plus n) c0
iszero = λn. n (λx. fls) tru
```

以上的定義中 `m` 與 `n` 都是 Church numerals。`succ` 概念上就是再多呼叫一次 `s`，`plus` 則是先呼叫 `n` 次再呼叫 `m` 次而達成 `m + n` 的效果，`times` 就是對 0 加上 `m` 次 `n`，`iszero` 使用一個總是回傳 `fls` 的函式，如果呼叫一次以上結果就會是 `fls`，如果 `n = c0` 則結果會是 `tru`。

最後介紹比較複雜的 `pred`（predecessor），取得前一個 Church numeral：

```
zz = pair c0 c0
ss = λp. pair (second p) (plus c1 (second p))
pred = λn. first (n ss zz)
```

概念上是維護一個 pair 記錄上一個以及目前的 Church numeral `(last, this)`，`zz` 是一開始的狀態，`ss` 會把 $(c_n, c_m)$ 變成 $(c_m, c_{m+1})$。`pred` 對 `zz` 呼叫 `n > 0` 次 `ss` 後就會得到 $(c_{n-1}, c_n)$，然後呼叫 `first` 得到 $c_{n-1}$。

```
pred c3
→ first (c3 ss zz)                 
→ first (ss (ss (ss zz)))          
= first (ss (ss (ss (pair c0 c0))))
→ first (ss (ss (pair c0 c1)))     
→ first (ss (pair c1 c2))
→ first (pair c2 c3)
→ c2
```

---

## 4 Lambda Abstractions

這裡列出本篇文章提到的所有 lambda abstractions。

### 4.1 Combinators

```
id = λx.x
```

### 4.2 Church Booleans

```
tru = λt. λf. t
fls = λt. λf. f
test = λb. λt. λf. b t f
```

### 4.3 Church Numerals

```
c0 = λs. λz. z
c1 = λs. λz. s z
c2 = λs. λz. s (s z)
c3 = λs. λz. s (s (s z))
...

succ = λn. λs. λz. s (n s z)
plus = λm. λn. λz. m s (n s z)
times = λm. λn. m (plus n) c0
iszero = λn. n (λx. fls) tru

zz = pair c0 c0
ss = λp. pair (second p) (plus c1 (second p))
pred = λn. first (n ss zz)
```

---
## Reference

1. Types and Programming Languages, Benjamin C. Pierce
2. [λ演算 - 維基百科，自由的百科全書](https://zh.wikipedia.org/zh-tw/Λ演算)
