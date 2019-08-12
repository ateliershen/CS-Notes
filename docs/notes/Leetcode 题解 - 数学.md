<!-- GFM-TOC -->
* [素數分解](#素數分解)
* [整除](#整除)
* [最大公約數最小公倍數](#最大公約數最小公倍數)
    * [1. 生成素數序列](#1-生成素數序列)
    * [2. 最大公約數](#2-最大公約數)
    * [3. 使用位操作和減法求解最大公約數](#3-使用位操作和減法求解最大公約數)
* [進制轉換](#進制轉換)
    * [1. 7 進制](#1-7-進制)
    * [2. 16 進制](#2-16-進制)
    * [3. 26 進制](#3-26-進制)
* [階乘](#階乘)
    * [1. 統計階乘尾部有多少個 0](#1-統計階乘尾部有多少個-0)
* [字符串加法減法](#字符串加法減法)
    * [1. 二進制加法](#1-二進制加法)
    * [2. 字符串加法](#2-字符串加法)
* [相遇問題](#相遇問題)
    * [1. 改變數組元素使所有的數組元素都相等](#1-改變數組元素使所有的數組元素都相等)
* [多數投票問題](#多數投票問題)
    * [1. 數組中出現次數多於 n / 2 的元素](#1-數組中出現次數多於-n--2-的元素)
* [其它](#其它)
    * [1. 平方數](#1-平方數)
    * [2. 3 的 n 次方](#2-3-的-n-次方)
    * [3. 乘積數組](#3-乘積數組)
    * [4. 找出數組中的乘積最大的三個數](#4-找出數組中的乘積最大的三個數)
<!-- GFM-TOC -->


# 素數分解

每一個數都可以分解成素數的乘積，例如 84 = 2<sup>2</sup> \* 3<sup>1</sup> \* 5<sup>0</sup> \* 7<sup>1</sup> \* 11<sup>0</sup> \* 13<sup>0</sup> \* 17<sup>0</sup> \* …

# 整除

令 x = 2<sup>m0</sup> \* 3<sup>m1</sup> \* 5<sup>m2</sup> \* 7<sup>m3</sup> \* 11<sup>m4</sup> \* …

令 y = 2<sup>n0</sup> \* 3<sup>n1</sup> \* 5<sup>n2</sup> \* 7<sup>n3</sup> \* 11<sup>n4</sup> \* …

如果 x 整除 y（y mod x == 0），則對於所有 i，mi <= ni。

# 最大公約數最小公倍數

x 和 y 的最大公約數為：gcd(x,y) =  2<sup>min(m0,n0)</sup> \* 3<sup>min(m1,n1)</sup> \* 5<sup>min(m2,n2)</sup> \* ...

x 和 y 的最小公倍數為：lcm(x,y) =  2<sup>max(m0,n0)</sup> \* 3<sup>max(m1,n1)</sup> \* 5<sup>max(m2,n2)</sup> \* ...

## 1. 生成素數序列

[204. Count Primes (Easy)](https://leetcode.com/problems/count-primes/description/)

埃拉託斯特尼篩法在每次找到一個素數時，將能被素數整除的數排除掉。

```java
public int countPrimes(int n) {
    boolean[] notPrimes = new boolean[n + 1];
    int count = 0;
    for (int i = 2; i < n; i++) {
        if (notPrimes[i]) {
            continue;
        }
        count++;
        // 從 i * i 開始，因為如果 k < i，那麼 k * i 在之前就已經被去除過了
        for (long j = (long) (i) * i; j < n; j += i) {
            notPrimes[(int) j] = true;
        }
    }
    return count;
}
```

## 2. 最大公約數

```java
int gcd(int a, int b) {
    return b == 0 ? a : gcd(b, a % b);
}
```

最小公倍數為兩數的乘積除以最大公約數。

```java
int lcm(int a, int b) {
    return a * b / gcd(a, b);
}
```

## 3. 使用位操作和減法求解最大公約數

[編程之美：2.7](#)

對於 a 和 b 的最大公約數 f(a, b)，有：

- 如果 a 和 b 均為偶數，f(a, b) = 2\*f(a/2, b/2);
- 如果 a 是偶數 b 是奇數，f(a, b) = f(a/2, b);
- 如果 b 是偶數 a 是奇數，f(a, b) = f(a, b/2);
- 如果 a 和 b 均為奇數，f(a, b) = f(b, a-b);

乘 2 和除 2 都可以轉換為移位操作。

```java
public int gcd(int a, int b) {
    if (a < b) {
        return gcd(b, a);
    }
    if (b == 0) {
        return a;
    }
    boolean isAEven = isEven(a), isBEven = isEven(b);
    if (isAEven && isBEven) {
        return 2 * gcd(a >> 1, b >> 1);
    } else if (isAEven && !isBEven) {
        return gcd(a >> 1, b);
    } else if (!isAEven && isBEven) {
        return gcd(a, b >> 1);
    } else {
        return gcd(b, a - b);
    }
}
```

# 進制轉換

## 1. 7 進制

[504. Base 7 (Easy)](https://leetcode.com/problems/base-7/description/)

```java
public String convertToBase7(int num) {
    if (num == 0) {
        return "0";
    }
    StringBuilder sb = new StringBuilder();
    boolean isNegative = num < 0;
    if (isNegative) {
        num = -num;
    }
    while (num > 0) {
        sb.append(num % 7);
        num /= 7;
    }
    String ret = sb.reverse().toString();
    return isNegative ? "-" + ret : ret;
}
```

Java 中 static String toString(int num, int radix) 可以將一個整數轉換為 radix 進製表示的字符串。

```java
public String convertToBase7(int num) {
    return Integer.toString(num, 7);
}
```

## 2. 16 進制

[405. Convert a Number to Hexadecimal (Easy)](https://leetcode.com/problems/convert-a-number-to-hexadecimal/description/)

```html
Input:
26

Output:
"1a"

Input:
-1

Output:
"ffffffff"
```

負數要用它的補碼形式。

```java
public String toHex(int num) {
    char[] map = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};
    if (num == 0) return "0";
    StringBuilder sb = new StringBuilder();
    while (num != 0) {
        sb.append(map[num & 0b1111]);
        num >>>= 4; // 因為考慮的是補碼形式，因此符號位就不能有特殊的意義，需要使用無符號右移，左邊填 0
    }
    return sb.reverse().toString();
}
```

## 3. 26 進制

[168. Excel Sheet Column Title (Easy)](https://leetcode.com/problems/excel-sheet-column-title/description/)

```html
1 -> A
2 -> B
3 -> C
...
26 -> Z
27 -> AA
28 -> AB
```

因為是從 1 開始計算的，而不是從 0 開始，因此需要對 n 執行 -1 操作。

```java
public String convertToTitle(int n) {
    if (n == 0) {
        return "";
    }
    n--;
    return convertToTitle(n / 26) + (char) (n % 26 + 'A');
}
```

# 階乘

## 1. 統計階乘尾部有多少個 0

[172. Factorial Trailing Zeroes (Easy)](https://leetcode.com/problems/factorial-trailing-zeroes/description/)

尾部的 0 由 2 * 5 得來，2 的數量明顯多於 5 的數量，因此只要統計有多少個 5 即可。

對於一個數 N，它所包含 5 的個數為：N/5 + N/5<sup>2</sup> + N/5<sup>3</sup> + ...，其中 N/5 表示不大於 N 的數中 5 的倍數貢獻一個 5，N/5<sup>2</sup> 表示不大於 N 的數中 5<sup>2</sup> 的倍數再貢獻一個 5 ...。

```java
public int trailingZeroes(int n) {
    return n == 0 ? 0 : n / 5 + trailingZeroes(n / 5);
}
```

如果統計的是 N! 的二進制表示中最低位 1 的位置，只要統計有多少個 2 即可，該題目出自 [編程之美：2.2](#) 。和求解有多少個 5 一樣，2 的個數為 N/2 + N/2<sup>2</sup> + N/2<sup>3</sup> + ...

# 字符串加法減法

## 1. 二進制加法

[67. Add Binary (Easy)](https://leetcode.com/problems/add-binary/description/)

```html
a = "11"
b = "1"
Return "100".
```

```java
public String addBinary(String a, String b) {
    int i = a.length() - 1, j = b.length() - 1, carry = 0;
    StringBuilder str = new StringBuilder();
    while (carry == 1 || i >= 0 || j >= 0) {
        if (i >= 0 && a.charAt(i--) == '1') {
            carry++;
        }
        if (j >= 0 && b.charAt(j--) == '1') {
            carry++;
        }
        str.append(carry % 2);
        carry /= 2;
    }
    return str.reverse().toString();
}
```

## 2. 字符串加法

[415. Add Strings (Easy)](https://leetcode.com/problems/add-strings/description/)

字符串的值為非負整數。

```java
public String addStrings(String num1, String num2) {
    StringBuilder str = new StringBuilder();
    int carry = 0, i = num1.length() - 1, j = num2.length() - 1;
    while (carry == 1 || i >= 0 || j >= 0) {
        int x = i < 0 ? 0 : num1.charAt(i--) - '0';
        int y = j < 0 ? 0 : num2.charAt(j--) - '0';
        str.append((x + y + carry) % 10);
        carry = (x + y + carry) / 10;
    }
    return str.reverse().toString();
}
```

# 相遇問題

## 1. 改變數組元素使所有的數組元素都相等

[462. Minimum Moves to Equal Array Elements II (Medium)](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/description/)

```html
Input:
[1,2,3]

Output:
2

Explanation:
Only two moves are needed (remember each move increments or decrements one element):

[1,2,3]  =>  [2,2,3]  =>  [2,2,2]
```

每次可以對一個數組元素加一或者減一，求最小的改變次數。

這是個典型的相遇問題，移動距離最小的方式是所有元素都移動到中位數。理由如下：

設 m 為中位數。a 和 b 是 m 兩邊的兩個元素，且 b > a。要使 a 和 b 相等，它們總共移動的次數為 b - a，這個值等於 (b - m) + (m - a)，也就是把這兩個數移動到中位數的移動次數。

設數組長度為 N，則可以找到 N/2 對 a 和 b 的組合，使它們都移動到 m 的位置。

**解法 1** 

先排序，時間複雜度：O(NlogN)

```java
public int minMoves2(int[] nums) {
    Arrays.sort(nums);
    int move = 0;
    int l = 0, h = nums.length - 1;
    while (l <= h) {
        move += nums[h] - nums[l];
        l++;
        h--;
    }
    return move;
}
```

**解法 2** 

使用快速選擇找到中位數，時間複雜度 O(N)

```java
public int minMoves2(int[] nums) {
    int move = 0;
    int median = findKthSmallest(nums, nums.length / 2);
    for (int num : nums) {
        move += Math.abs(num - median);
    }
    return move;
}

private int findKthSmallest(int[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k) {
            break;
        }
        if (j < k) {
            l = j + 1;
        } else {
            h = j - 1;
        }
    }
    return nums[k];
}

private int partition(int[] nums, int l, int h) {
    int i = l, j = h + 1;
    while (true) {
        while (nums[++i] < nums[l] && i < h) ;
        while (nums[--j] > nums[l] && j > l) ;
        if (i >= j) {
            break;
        }
        swap(nums, i, j);
    }
    swap(nums, l, j);
    return j;
}

private void swap(int[] nums, int i, int j) {
    int tmp = nums[i];
    nums[i] = nums[j];
    nums[j] = tmp;
}
```

# 多數投票問題

## 1. 數組中出現次數多於 n / 2 的元素

[169. Majority Element (Easy)](https://leetcode.com/problems/majority-element/description/)

先對數組排序，最中間那個數出現次數一定多於 n / 2。

```java
public int majorityElement(int[] nums) {
    Arrays.sort(nums);
    return nums[nums.length / 2];
}
```

可以利用 Boyer-Moore Majority Vote Algorithm 來解決這個問題，使得時間複雜度為 O(N)。可以這麼理解該算法：使用 cnt 來統計一個元素出現的次數，當遍歷到的元素和統計元素不相等時，令 cnt--。如果前面查找了 i 個元素，且 cnt == 0，說明前 i 個元素沒有 majority，或者有 majority，但是出現的次數少於 i / 2，因為如果多於 i / 2 的話 cnt 就一定不會為 0。此時剩下的 n - i 個元素中，majority 的數目依然多於 (n - i) / 2，因此繼續查找就能找出 majority。

```java
public int majorityElement(int[] nums) {
    int cnt = 0, majority = nums[0];
    for (int num : nums) {
        majority = (cnt == 0) ? num : majority;
        cnt = (majority == num) ? cnt + 1 : cnt - 1;
    }
    return majority;
}
```

# 其它

## 1. 平方數

[367. Valid Perfect Square (Easy)](https://leetcode.com/problems/valid-perfect-square/description/)

```html
Input: 16
Returns: True
```

平方序列：1,4,9,16,..

間隔：3,5,7,...

間隔為等差數列，使用這個特性可以得到從 1 開始的平方序列。

```java
public boolean isPerfectSquare(int num) {
    int subNum = 1;
    while (num > 0) {
        num -= subNum;
        subNum += 2;
    }
    return num == 0;
}
```

## 2. 3 的 n 次方

[326. Power of Three (Easy)](https://leetcode.com/problems/power-of-three/description/)

```java
public boolean isPowerOfThree(int n) {
    return n > 0 && (1162261467 % n == 0);
}
```

## 3. 乘積數組

[238. Product of Array Except Self (Medium)](https://leetcode.com/problems/product-of-array-except-self/description/)

```html
For example, given [1,2,3,4], return [24,12,8,6].
```

給定一個數組，創建一個新數組，新數組的每個元素為原始數組中除了該位置上的元素之外所有元素的乘積。

要求時間複雜度為 O(N)，並且不能使用除法。

```java
public int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] products = new int[n];
    Arrays.fill(products, 1);
    int left = 1;
    for (int i = 1; i < n; i++) {
        left *= nums[i - 1];
        products[i] *= left;
    }
    int right = 1;
    for (int i = n - 2; i >= 0; i--) {
        right *= nums[i + 1];
        products[i] *= right;
    }
    return products;
}
```

## 4. 找出數組中的乘積最大的三個數

[628. Maximum Product of Three Numbers (Easy)](https://leetcode.com/problems/maximum-product-of-three-numbers/description/)

```html
Input: [1,2,3,4]
Output: 24
```

```java
public int maximumProduct(int[] nums) {
    int max1 = Integer.MIN_VALUE, max2 = Integer.MIN_VALUE, max3 = Integer.MIN_VALUE, min1 = Integer.MAX_VALUE, min2 = Integer.MAX_VALUE;
    for (int n : nums) {
        if (n > max1) {
            max3 = max2;
            max2 = max1;
            max1 = n;
        } else if (n > max2) {
            max3 = max2;
            max2 = n;
        } else if (n > max3) {
            max3 = n;
        }

        if (n < min1) {
            min2 = min1;
            min1 = n;
        } else if (n < min2) {
            min2 = n;
        }
    }
    return Math.max(max1*max2*max3, max1*min1*min2);
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
