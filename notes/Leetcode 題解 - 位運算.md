<!-- GFM-TOC -->
* [1. 統計兩個數的二進制表示有多少位不同](#1-統計兩個數的二進制表示有多少位不同)
* [2. 數組中唯一一個不重複的元素](#2-數組中唯一一個不重複的元素)
* [3. 找出數組中缺失的那個數](#3-找出數組中缺失的那個數)
* [4. 數組中不重複的兩個元素](#4-數組中不重複的兩個元素)
* [5. 翻轉一個數的比特位](#5-翻轉一個數的比特位)
* [6. 不用額外變量交換兩個整數](#6-不用額外變量交換兩個整數)
* [7. 判斷一個數是不是 2 的 n 次方](#7-判斷一個數是不是-2-的-n-次方)
* [8.  判斷一個數是不是 4 的 n 次方](#8--判斷一個數是不是-4-的-n-次方)
* [9. 判斷一個數的位級表示是否不會出現連續的 0 和 1](#9-判斷一個數的位級表示是否不會出現連續的-0-和-1)
* [10. 求一個數的補碼](#10-求一個數的補碼)
* [11. 實現整數的加法](#11-實現整數的加法)
* [12. 字符串數組最大乘積](#12-字符串數組最大乘積)
* [13. 統計從 0 \~ n 每個數的二進制表示中 1 的個數](#13-統計從-0-\~-n-每個數的二進制表示中-1-的個數)
<!-- GFM-TOC -->


**基本原理** 

0s 表示一串 0，1s 表示一串 1。

```
x ^ 0s = x      x & 0s = 0      x | 0s = x
x ^ 1s = ~x     x & 1s = x      x | 1s = 1s
x ^ x = 0       x & x = x       x | x = x
```

- 利用 x ^ 1s = \~x 的特點，可以將位級表示翻轉；利用 x ^ x = 0 的特點，可以將三個數中重複的兩個數去除，只留下另一個數。
- 利用 x & 0s = 0 和 x & 1s = x 的特點，可以實現掩碼操作。一個數 num 與 mask：00111100 進行位與操作，只保留 num 中與 mask 的 1 部分相對應的位。
- 利用 x | 0s = x 和 x | 1s = 1s 的特點，可以實現設值操作。一個數 num 與 mask：00111100 進行位或操作，將 num 中與 mask 的 1 部分相對應的位都設置為 1。

位與運算技巧：

- n&(n-1) 去除 n 的位級表示中最低的那一位。例如對於二進制表示 10110100，減去 1 得到 10110011，這兩個數相與得到 10110000。
- n&(-n) 得到 n 的位級表示中最低的那一位。-n 得到 n 的反碼加 1，對於二進制表示 10110100，-n 得到 01001100，相與得到 00000100。
- n-n&(\~n+1) 去除 n 的位級表示中最高的那一位。

移位運算：

- \>\> n 為算術右移，相當於除以 2<sup>n</sup>；
- \>\>\> n 為無符號右移，左邊會補上 0。
- &lt;&lt; n 為算術左移，相當於乘以 2<sup>n</sup>。

** mask 計算** 

要獲取 111111111，將 0 取反即可，\~0。

要得到只有第 i 位為 1 的 mask，將 1 向左移動 i-1 位即可，1&lt;&lt;(i-1) 。例如 1&lt;&lt;4 得到只有第 5 位為 1 的 mask ：00010000。

要得到 1 到 i 位為 1 的 mask，1&lt;&lt;(i+1)-1 即可，例如將 1&lt;&lt;(4+1)-1 = 00010000-1 = 00001111。

要得到 1 到 i 位為 0 的 mask，只需將 1 到 i 位為 1 的 mask 取反，即 \~(1&lt;&lt;(i+1)-1)。

**Java 中的位操作** 

```html
static int Integer.bitCount();           // 統計 1 的數量
static int Integer.highestOneBit();      // 獲得最高位
static String toBinaryString(int i);     // 轉換為二進制表示的字符串
```

# 1. 統計兩個數的二進制表示有多少位不同

[461. Hamming Distance (Easy)](https://leetcode.com/problems/hamming-distance/)

```html
Input: x = 1, y = 4

Output: 2

Explanation:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

The above arrows point to positions where the corresponding bits are different.
```

對兩個數進行異或操作，位級表示不同的那一位為 1，統計有多少個 1 即可。

```java
public int hammingDistance(int x, int y) {
    int z = x ^ y;
    int cnt = 0;
    while(z != 0) {
        if ((z & 1) == 1) cnt++;
        z = z >> 1;
    }
    return cnt;
}
```

使用 z&(z-1) 去除 z 位級表示最低的那一位。

```java
public int hammingDistance(int x, int y) {
    int z = x ^ y;
    int cnt = 0;
    while (z != 0) {
        z &= (z - 1);
        cnt++;
    }
    return cnt;
}
```

可以使用 Integer.bitcount() 來統計 1 個的個數。

```java
public int hammingDistance(int x, int y) {
    return Integer.bitCount(x ^ y);
}
```

# 2. 數組中唯一一個不重複的元素

[136. Single Number (Easy)](https://leetcode.com/problems/single-number/description/)

```html
Input: [4,1,2,1,2]
Output: 4
```

兩個相同的數異或的結果為 0，對所有數進行異或操作，最後的結果就是單獨出現的那個數。

```java
public int singleNumber(int[] nums) {
    int ret = 0;
    for (int n : nums) ret = ret ^ n;
    return ret;
}
```

# 3. 找出數組中缺失的那個數

[268. Missing Number (Easy)](https://leetcode.com/problems/missing-number/description/)

```html
Input: [3,0,1]
Output: 2
```

題目描述：數組元素在 0-n 之間，但是有一個數是缺失的，要求找到這個缺失的數。

```java
public int missingNumber(int[] nums) {
    int ret = 0;
    for (int i = 0; i < nums.length; i++) {
        ret = ret ^ i ^ nums[i];
    }
    return ret ^ nums.length;
}
```

# 4. 數組中不重複的兩個元素

[260. Single Number III (Medium)](https://leetcode.com/problems/single-number-iii/description/)

兩個不相等的元素在位級表示上必定會有一位存在不同。

將數組的所有元素異或得到的結果為不存在重複的兩個元素異或的結果。

diff &= -diff 得到出 diff 最右側不為 0 的位，也就是不存在重複的兩個元素在位級表示上最右側不同的那一位，利用這一位就可以將兩個元素區分開來。

```java
public int[] singleNumber(int[] nums) {
    int diff = 0;
    for (int num : nums) diff ^= num;
    diff &= -diff;  // 得到最右一位
    int[] ret = new int[2];
    for (int num : nums) {
        if ((num & diff) == 0) ret[0] ^= num;
        else ret[1] ^= num;
    }
    return ret;
}
```

# 5. 翻轉一個數的比特位

[190. Reverse Bits (Easy)](https://leetcode.com/problems/reverse-bits/description/)

```java
public int reverseBits(int n) {
    int ret = 0;
    for (int i = 0; i < 32; i++) {
        ret <<= 1;
        ret |= (n & 1);
        n >>>= 1;
    }
    return ret;
}
```

如果該函數需要被調用很多次，可以將 int 拆成 4 個 byte，然後緩存 byte 對應的比特位翻轉，最後再拼接起來。

```java
private static Map<Byte, Integer> cache = new HashMap<>();

public int reverseBits(int n) {
    int ret = 0;
    for (int i = 0; i < 4; i++) {
        ret <<= 8;
        ret |= reverseByte((byte) (n & 0b11111111));
        n >>= 8;
    }
    return ret;
}

private int reverseByte(byte b) {
    if (cache.containsKey(b)) return cache.get(b);
    int ret = 0;
    byte t = b;
    for (int i = 0; i < 8; i++) {
        ret <<= 1;
        ret |= t & 1;
        t >>= 1;
    }
    cache.put(b, ret);
    return ret;
}
```

# 6. 不用額外變量交換兩個整數

[程序員代碼面試指南 ：P317](#)

```java
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

# 7. 判斷一個數是不是 2 的 n 次方

[231. Power of Two (Easy)](https://leetcode.com/problems/power-of-two/description/)

二進制表示只有一個 1 存在。

```java
public boolean isPowerOfTwo(int n) {
    return n > 0 && Integer.bitCount(n) == 1;
}
```

利用 1000 & 0111 == 0 這種性質，得到以下解法：

```java
public boolean isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}
```

# 8.  判斷一個數是不是 4 的 n 次方

[342. Power of Four (Easy)](https://leetcode.com/problems/power-of-four/)

這種數在二進制表示中有且只有一個奇數位為 1，例如 16（10000）。

```java
public boolean isPowerOfFour(int num) {
    return num > 0 && (num & (num - 1)) == 0 && (num & 0b01010101010101010101010101010101) != 0;
}
```

也可以使用正則表達式進行匹配。

```java
public boolean isPowerOfFour(int num) {
    return Integer.toString(num, 4).matches("10*");
}
```

# 9. 判斷一個數的位級表示是否不會出現連續的 0 和 1

[693. Binary Number with Alternating Bits (Easy)](https://leetcode.com/problems/binary-number-with-alternating-bits/description/)

```html
Input: 10
Output: True
Explanation:
The binary representation of 10 is: 1010.

Input: 11
Output: False
Explanation:
The binary representation of 11 is: 1011.
```

對於 1010 這種位級表示的數，把它向右移動 1 位得到 101，這兩個數每個位都不同，因此異或得到的結果為 1111。

```java
public boolean hasAlternatingBits(int n) {
    int a = (n ^ (n >> 1));
    return (a & (a + 1)) == 0;
}
```

# 10. 求一個數的補碼

[476. Number Complement (Easy)](https://leetcode.com/problems/number-complement/description/)

```html
Input: 5
Output: 2
Explanation: The binary representation of 5 is 101 (no leading zero bits), and its complement is 010. So you need to output 2.
```

題目描述：不考慮二進制表示中的首 0 部分。

對於 00000101，要求補碼可以將它與 00000111 進行異或操作。那麼問題就轉換為求掩碼 00000111。

```java
public int findComplement(int num) {
    if (num == 0) return 1;
    int mask = 1 << 30;
    while ((num & mask) == 0) mask >>= 1;
    mask = (mask << 1) - 1;
    return num ^ mask;
}
```

可以利用 Java 的 Integer.highestOneBit() 方法來獲得含有首 1 的數。

```java
public int findComplement(int num) {
    if (num == 0) return 1;
    int mask = Integer.highestOneBit(num);
    mask = (mask << 1) - 1;
    return num ^ mask;
}
```

對於 10000000 這樣的數要擴展成 11111111，可以利用以下方法：

```html
mask |= mask >> 1    11000000
mask |= mask >> 2    11110000
mask |= mask >> 4    11111111
```

```java
public int findComplement(int num) {
    int mask = num;
    mask |= mask >> 1;
    mask |= mask >> 2;
    mask |= mask >> 4;
    mask |= mask >> 8;
    mask |= mask >> 16;
    return (mask ^ num);
}
```

# 11. 實現整數的加法

[371. Sum of Two Integers (Easy)](https://leetcode.com/problems/sum-of-two-integers/description/)

a ^ b 表示沒有考慮進位的情況下兩數的和，(a & b) << 1 就是進位。

遞歸會終止的原因是 (a & b) << 1 最右邊會多一個 0，那麼繼續遞歸，進位最右邊的 0 會慢慢增多，最後進位會變為 0，遞歸終止。

```java
public int getSum(int a, int b) {
    return b == 0 ? a : getSum((a ^ b), (a & b) << 1);
}
```

# 12. 字符串數組最大乘積

[318. Maximum Product of Word Lengths (Medium)](https://leetcode.com/problems/maximum-product-of-word-lengths/description/)

```html
Given ["abcw", "baz", "foo", "bar", "xtfn", "abcdef"]
Return 16
The two words can be "abcw", "xtfn".
```

題目描述：字符串數組的字符串只含有小寫字符。求解字符串數組中兩個字符串長度的最大乘積，要求這兩個字符串不能含有相同字符。

本題主要問題是判斷兩個字符串是否含相同字符，由於字符串只含有小寫字符，總共 26 位，因此可以用一個 32 位的整數來存儲每個字符是否出現過。

```java
public int maxProduct(String[] words) {
    int n = words.length;
    int[] val = new int[n];
    for (int i = 0; i < n; i++) {
        for (char c : words[i].toCharArray()) {
            val[i] |= 1 << (c - 'a');
        }
    }
    int ret = 0;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if ((val[i] & val[j]) == 0) {
                ret = Math.max(ret, words[i].length() * words[j].length());
            }
        }
    }
    return ret;
}
```

# 13. 統計從 0 \~ n 每個數的二進制表示中 1 的個數

[338. Counting Bits (Medium)](https://leetcode.com/problems/counting-bits/description/)

對於數字 6(110)，它可以看成是 4(100) 再加一個 2(10)，因此 dp[i] = dp[i&(i-1)] + 1;

```java
public int[] countBits(int num) {
    int[] ret = new int[num + 1];
    for(int i = 1; i <= num; i++){
        ret[i] = ret[i&(i-1)] + 1;
    }
    return ret;
}
```





# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
