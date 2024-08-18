+++
title = "最長公因字串"
description = "兩字串分別由多個字串 C 串接而成，找出最長的 C"

[taxonomies]
categories = ["Algorithm"]
+++

最近在 LeetCode 上面看到一題簡單卻有意思的題目 [Greatest Common Divisor of Strings - LeetCode](https://leetcode.com/problems/greatest-common-divisor-of-strings/?envType=study-plan-v2&envId=leetcode-75)，除了遍歷所有字首和使用輾轉相除法的解法外，我還看到了一個需要一些思考與沒那麼 trivial 的解法：

```kotlin
class Solution {
    fun gcdOfStrings(str1: String, str2: String): String {
        if (str1 + str2 != str2 + str1) {
            return ""
        }
        
        return str1.substring(0, gcd(str1.length, str2.length))
    }

    fun gcd(a: Int, b: Int) : Int {
        if (b == 0) {
            return a
        } else {
            return gcd(b, a % b)
        }
    }
}
```

> 以下推導皆基於 $S_1, S_2$ 為**有限非空字串**的假設。

## 判斷公因字串是否存在

首先我們可以觀察，如果公因字串 $C$ 存在的話，兩個字串都將是由多個 $C$ 串接起來的字串（CCC...），因此 $S_1 + S_2 = S_2 + S_1$；反過來說如果 $S_1 + S_2 \ne S_2 + S_1$ 就代表它們沒有公因字串，所以我們可以直接回傳空字串。

那 $S_1 + S_2 = S_2 + S_1$ 就**一定有**公因字串嗎？如果是這個樣子的話，那我們需要證明以下命題：

> $S_1 + S_2 = S_2 + S_1$ $\iff$ $S_1, S_2$ 有公因字串 $C$

$\impliedby$ 方向已經在上面證明過了，現在證明 $\implies$ 方向。

### $S_1 + S_2 = S_2 + S_1$ $\implies$ $S_1, S_2$ 有公因字串 $C$

我們使用數學歸納法，以 $S_1 + S_2$ 的字串長度 $L$ 為變數。

**起始步驟**：當 $L = 2$，則 $S_1$ 與 $S_2$ 的長度皆為 $1$，很明顯 $S_1 = S_2 = C$。

**推遞步驟**：假設在 $2 \le L < k$ 時皆成立，當 $L = k$ 時，若 $S_1$ 與 $S_2$ 長度相同，可得 $S_1 = S_2 = C$；反之不失一般性假設 $S_1$ 比較長，我們用 `()` 代表 $S_1$、`[]` 代表 $S_2$，則 $S_1 + S_2 = S_2 + S_1$ 可以這樣表示：

- $S_1 + S_2$：`(________)[___]`
- $S_2 + S_1$：`[___](________)`

我們把兩個 $S_1$ 重疊的地方用 $x$ 表示，$S_2$ 用 $y$ 表示：

- $S_1 + S_2$：`( y || x )[ y ]`
- $S_2 + S_1$：`[ y ]( x || y )`

我們得到 $S_1 = y + x$ （上式）且 $S_1 = x + y$ （下式），也就是 $y + x = x + y$，又 $x, y$ 皆為非空字串且 $x + y$ 的長度小於 $k$，所以根據假設（在 $2 \le L < k$ 時皆成立）我們知道 $x, y$ 兩字串有公因字串 $z$。

因為 $z$ 是 $x, y$ 的因字串，所以 $z$ 是 $S_1 = x+y$ 的因字串也是 $S_2 = y$ 的因字串，最終得到 $z$ 是 $S_1, S_2$ 的公因字串，完成證明：

> $S_1 + S_2 = S_2 + S_1$ $\implies$ $S_1, S_2$ 有公因字串 $C$

再回頭看一次程式碼：

```kotlin
if (str1 + str2 != str2 + str1) {
    return ""
}

return str1.substring(0, gcd(str1.length, str2.length))
```

如果 $S_1 + S_2 = S_2 + S_1$ 我們就可以肯定地說**公因字串存在**。

## 尋找最長公因字串

公因字串存在且 $S_1, S_2$ 長度有限，因此也存在一個最長公因字串。

令 $S_1 = nC, S_2 = mC$，代表 $S_1, S_2$ 各由 $n, m$ 個任意公因字串 $C$ 串接而成。我們想一下就會發現最長公因字串就是由 $\gcd(n, m)$ 個 $C$ 串接而成，因為如果把 $C$ 想成數字 $1$，我們就會發現它根本就是**最大公因數**問題。

所以 `str1.substring(0, gcd(str1.length, str2.length))` 就是最長公因字串。