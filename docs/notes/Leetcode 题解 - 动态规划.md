<!-- GFM-TOC -->
* [斐波那契數列](#斐波那契數列)
    * [1. 爬樓梯](#1-爬樓梯)
    * [2. 強盜搶劫](#2-強盜搶劫)
    * [3. 強盜在環形街區搶劫](#3-強盜在環形街區搶劫)
    * [4. 信件錯排](#4-信件錯排)
    * [5. 母牛生產](#5-母牛生產)
* [矩陣路徑](#矩陣路徑)
    * [1. 矩陣的最小路徑和](#1-矩陣的最小路徑和)
    * [2. 矩陣的總路徑數](#2-矩陣的總路徑數)
* [數組區間](#數組區間)
    * [1. 數組區間和](#1-數組區間和)
    * [2. 數組中等差遞增子區間的個數](#2-數組中等差遞增子區間的個數)
* [分割整數](#分割整數)
    * [1. 分割整數的最大乘積](#1-分割整數的最大乘積)
    * [2. 按平方數來分割整數](#2-按平方數來分割整數)
    * [3. 分割整數構成字母字符串](#3-分割整數構成字母字符串)
* [最長遞增子序列](#最長遞增子序列)
    * [1. 最長遞增子序列](#1-最長遞增子序列)
    * [2. 一組整數對能夠構成的最長鏈](#2-一組整數對能夠構成的最長鏈)
    * [3. 最長擺動子序列](#3-最長擺動子序列)
* [最長公共子序列](#最長公共子序列)
* [0-1 揹包](#0-1-揹包)
    * [1. 劃分數組為和相等的兩部分](#1-劃分數組為和相等的兩部分)
    * [2. 改變一組數的正負號使得它們的和為一給定數](#2-改變一組數的正負號使得它們的和為一給定數)
    * [3. 01 字符構成最多的字符串](#3-01-字符構成最多的字符串)
    * [4. 找零錢的最少硬幣數](#4-找零錢的最少硬幣數)
    * [5. 找零錢的硬幣數組合](#5-找零錢的硬幣數組合)
    * [6. 字符串按單詞列表分割](#6-字符串按單詞列表分割)
    * [7. 組合總和](#7-組合總和)
* [股票交易](#股票交易)
    * [1. 需要冷卻期的股票交易](#1-需要冷卻期的股票交易)
    * [2. 需要交易費用的股票交易](#2-需要交易費用的股票交易)
    * [3. 只能進行兩次的股票交易](#3-只能進行兩次的股票交易)
    * [4. 只能進行 k 次的股票交易](#4-只能進行-k-次的股票交易)
* [字符串編輯](#字符串編輯)
    * [1. 刪除兩個字符串的字符使它們相等](#1-刪除兩個字符串的字符使它們相等)
    * [2. 編輯距離](#2-編輯距離)
    * [3. 複製粘貼字符](#3-複製粘貼字符)
<!-- GFM-TOC -->


遞歸和動態規劃都是將原問題拆成多個子問題然後求解，他們之間最本質的區別是，動態規劃保存了子問題的解，避免重複計算。

# 斐波那契數列

## 1. 爬樓梯

[70. Climbing Stairs (Easy)](https://leetcode.com/problems/climbing-stairs/description/)

題目描述：有 N 階樓梯，每次可以上一階或者兩階，求有多少種上樓梯的方法。

定義一個數組 dp 存儲上樓梯的方法數（為了方便討論，數組下標從 1 開始），dp[i] 表示走到第 i 個樓梯的方法數目。

第 i 個樓梯可以從第 i-1 和 i-2 個樓梯再走一步到達，走到第 i 個樓梯的方法數為走到第 i-1 和第 i-2 個樓梯的方法數之和。

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i]=dp[i-1]+dp[i-2]" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/14fe1e71-8518-458f-a220-116003061a83.png" width="200px"> </div><br>

考慮到 dp[i] 只與 dp[i - 1] 和 dp[i - 2] 有關，因此可以只用兩個變量來存儲 dp[i - 1] 和 dp[i - 2]，使得原來的 O(N) 空間複雜度優化為 O(1) 複雜度。

```java
public int climbStairs(int n) {
    if (n <= 2) {
        return n;
    }
    int pre2 = 1, pre1 = 2;
    for (int i = 2; i < n; i++) {
        int cur = pre1 + pre2;
        pre2 = pre1;
        pre1 = cur;
    }
    return pre1;
}
```

## 2. 強盜搶劫

[198. House Robber (Easy)](https://leetcode.com/problems/house-robber/description/)

題目描述：搶劫一排住戶，但是不能搶鄰近的住戶，求最大搶劫量。

定義 dp 數組用來存儲最大的搶劫量，其中 dp[i] 表示搶到第 i 個住戶時的最大搶劫量。

由於不能搶劫鄰近住戶，如果搶劫了第 i -1 個住戶，那麼就不能再搶劫第 i 個住戶，所以

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i]=max(dp[i-2]+nums[i],dp[i-1])" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/2de794ca-aa7b-48f3-a556-a0e2708cb976.jpg" width="350px"> </div><br>

```java
public int rob(int[] nums) {
    int pre2 = 0, pre1 = 0;
    for (int i = 0; i < nums.length; i++) {
        int cur = Math.max(pre2 + nums[i], pre1);
        pre2 = pre1;
        pre1 = cur;
    }
    return pre1;
}
```

## 3. 強盜在環形街區搶劫

[213. House Robber II (Medium)](https://leetcode.com/problems/house-robber-ii/description/)

```java
public int rob(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int n = nums.length;
    if (n == 1) {
        return nums[0];
    }
    return Math.max(rob(nums, 0, n - 2), rob(nums, 1, n - 1));
}

private int rob(int[] nums, int first, int last) {
    int pre2 = 0, pre1 = 0;
    for (int i = first; i <= last; i++) {
        int cur = Math.max(pre1, pre2 + nums[i]);
        pre2 = pre1;
        pre1 = cur;
    }
    return pre1;
}
```

## 4. 信件錯排

題目描述：有 N 個 信 和 信封，它們被打亂，求錯誤裝信方式的數量。

定義一個數組 dp 存儲錯誤方式數量，dp[i] 表示前 i 個信和信封的錯誤方式數量。假設第 i 個信裝到第 j 個信封裡面，而第 j 個信裝到第 k 個信封裡面。根據 i 和 k 是否相等，有兩種情況：

- i==k，交換 i 和 k 的信後，它們的信和信封在正確的位置，但是其餘 i-2 封信有 dp[i-2] 種錯誤裝信的方式。由於 j 有 i-1 種取值，因此共有 (i-1)\*dp[i-2] 種錯誤裝信方式。
- i != k，交換 i 和 j 的信後，第 i 個信和信封在正確的位置，其餘 i-1 封信有 dp[i-1] 種錯誤裝信方式。由於 j 有 i-1 種取值，因此共有 (i-1)\*dp[i-1] 種錯誤裝信方式。

綜上所述，錯誤裝信數量方式數量為：

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i]=(i-1)*dp[i-2]+(i-1)*dp[i-1]" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/da1f96b9-fd4d-44ca-8925-fb14c5733388.png" width="350px"> </div><br>

## 5. 母牛生產

[程序員代碼面試指南-P181](#)

題目描述：假設農場中成熟的母牛每年都會生 1 頭小母牛，並且永遠不會死。第一年有 1 只小母牛，從第二年開始，母牛開始生小母牛。每隻小母牛 3 年之後成熟又可以生小母牛。給定整數 N，求 N 年後牛的數量。

第 i 年成熟的牛的數量為：

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i]=dp[i-1]+dp[i-3]" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/879814ee-48b5-4bcb-86f5-dcc400cb81ad.png" width="250px"> </div><br>

# 矩陣路徑

## 1. 矩陣的最小路徑和

[64. Minimum Path Sum (Medium)](https://leetcode.com/problems/minimum-path-sum/description/)

```html
[[1,3,1],
 [1,5,1],
 [4,2,1]]
Given the above grid map, return 7. Because the path 1→3→1→1→1 minimizes the sum.
```

題目描述：求從矩陣的左上角到右下角的最小路徑和，每次只能向右和向下移動。

```java
public int minPathSum(int[][] grid) {
    if (grid.length == 0 || grid[0].length == 0) {
        return 0;
    }
    int m = grid.length, n = grid[0].length;
    int[] dp = new int[n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (j == 0) {
                dp[j] = dp[j];        // 只能從上側走到該位置
            } else if (i == 0) {
                dp[j] = dp[j - 1];    // 只能從左側走到該位置
            } else {
                dp[j] = Math.min(dp[j - 1], dp[j]);
            }
            dp[j] += grid[i][j];
        }
    }
    return dp[n - 1];
}
```

## 2. 矩陣的總路徑數

[62. Unique Paths (Medium)](https://leetcode.com/problems/unique-paths/description/)

題目描述：統計從矩陣左上角到右下角的路徑總數，每次只能向右或者向下移動。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/dc82f0f3-c1d4-4ac8-90ac-d5b32a9bd75a.jpg" width=""> </div><br>

```java
public int uniquePaths(int m, int n) {
    int[] dp = new int[n];
    Arrays.fill(dp, 1);
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[j] = dp[j] + dp[j - 1];
        }
    }
    return dp[n - 1];
}
```

也可以直接用數學公式求解，這是一個組合問題。機器人總共移動的次數 S=m+n-2，向下移動的次數 D=m-1，那麼問題可以看成從 S 中取出 D 個位置的組合數量，這個問題的解為 C(S, D)。

```java
public int uniquePaths(int m, int n) {
    int S = m + n - 2;  // 總共的移動次數
    int D = m - 1;      // 向下的移動次數
    long ret = 1;
    for (int i = 1; i <= D; i++) {
        ret = ret * (S - D + i) / i;
    }
    return (int) ret;
}
```

# 數組區間

## 1. 數組區間和

[303. Range Sum Query - Immutable (Easy)](https://leetcode.com/problems/range-sum-query-immutable/description/)

```html
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3
```

求區間 i \~ j 的和，可以轉換為 sum[j + 1] - sum[i]，其中 sum[i] 為 0 \~ i - 1 的和。

```java
class NumArray {

    private int[] sums;

    public NumArray(int[] nums) {
        sums = new int[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            sums[i] = sums[i - 1] + nums[i - 1];
        }
    }

    public int sumRange(int i, int j) {
        return sums[j + 1] - sums[i];
    }
}
```

## 2. 數組中等差遞增子區間的個數

[413. Arithmetic Slices (Medium)](https://leetcode.com/problems/arithmetic-slices/description/)

```html
A = [0, 1, 2, 3, 4]

return: 6, for 3 arithmetic slices in A:

[0, 1, 2],
[1, 2, 3],
[0, 1, 2, 3],
[0, 1, 2, 3, 4],
[ 1, 2, 3, 4],
[2, 3, 4]
```

dp[i] 表示以 A[i] 為結尾的等差遞增子區間的個數。

當 A[i] - A[i-1] == A[i-1] - A[i-2]，那麼 [A[i-2], A[i-1], A[i]] 構成一個等差遞增子區間。而且在以 A[i-1] 為結尾的遞增子區間的後面再加上一個 A[i]，一樣可以構成新的遞增子區間。

```html
dp[2] = 1
    [0, 1, 2]
dp[3] = dp[2] + 1 = 2
    [0, 1, 2, 3], // [0, 1, 2] 之後加一個 3
    [1, 2, 3]     // 新的遞增子區間
dp[4] = dp[3] + 1 = 3
    [0, 1, 2, 3, 4], // [0, 1, 2, 3] 之後加一個 4
    [1, 2, 3, 4],    // [1, 2, 3] 之後加一個 4
    [2, 3, 4]        // 新的遞增子區間
```

綜上，在 A[i] - A[i-1] == A[i-1] - A[i-2] 時，dp[i] = dp[i-1] + 1。

因為遞增子區間不一定以最後一個元素為結尾，可以是任意一個元素結尾，因此需要返回 dp 數組累加的結果。

```java
public int numberOfArithmeticSlices(int[] A) {
    if (A == null || A.length == 0) {
        return 0;
    }
    int n = A.length;
    int[] dp = new int[n];
    for (int i = 2; i < n; i++) {
        if (A[i] - A[i - 1] == A[i - 1] - A[i - 2]) {
            dp[i] = dp[i - 1] + 1;
        }
    }
    int total = 0;
    for (int cnt : dp) {
        total += cnt;
    }
    return total;
}
```

# 分割整數

## 1. 分割整數的最大乘積

[343. Integer Break (Medim)](https://leetcode.com/problems/integer-break/description/)

題目描述：For example, given n = 2, return 1 (2 = 1 + 1); given n = 10, return 36 (10 = 3 + 3 + 4).

```java
public int integerBreak(int n) {
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j <= i - 1; j++) {
            dp[i] = Math.max(dp[i], Math.max(j * dp[i - j], j * (i - j)));
        }
    }
    return dp[n];
}
```

## 2. 按平方數來分割整數

[279. Perfect Squares(Medium)](https://leetcode.com/problems/perfect-squares/description/)

題目描述：For example, given n = 12, return 3 because 12 = 4 + 4 + 4; given n = 13, return 2 because 13 = 4 + 9.

```java
public int numSquares(int n) {
    List<Integer> squareList = generateSquareList(n);
    int[] dp = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        int min = Integer.MAX_VALUE;
        for (int square : squareList) {
            if (square > i) {
                break;
            }
            min = Math.min(min, dp[i - square] + 1);
        }
        dp[i] = min;
    }
    return dp[n];
}

private List<Integer> generateSquareList(int n) {
    List<Integer> squareList = new ArrayList<>();
    int diff = 3;
    int square = 1;
    while (square <= n) {
        squareList.add(square);
        square += diff;
        diff += 2;
    }
    return squareList;
}
```

## 3. 分割整數構成字母字符串

[91. Decode Ways (Medium)](https://leetcode.com/problems/decode-ways/description/)

題目描述：Given encoded message "12", it could be decoded as "AB" (1 2) or "L" (12).

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    int n = s.length();
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = s.charAt(0) == '0' ? 0 : 1;
    for (int i = 2; i <= n; i++) {
        int one = Integer.valueOf(s.substring(i - 1, i));
        if (one != 0) {
            dp[i] += dp[i - 1];
        }
        if (s.charAt(i - 2) == '0') {
            continue;
        }
        int two = Integer.valueOf(s.substring(i - 2, i));
        if (two <= 26) {
            dp[i] += dp[i - 2];
        }
    }
    return dp[n];
}
```

# 最長遞增子序列

已知一個序列 {S<sub>1</sub>, S<sub>2</sub>,...,S<sub>n</sub>}，取出若干數組成新的序列 {S<sub>i1</sub>, S<sub>i2</sub>,..., S<sub>im</sub>}，其中 i1、i2 ... im 保持遞增，即新序列中各個數仍然保持原數列中的先後順序，稱新序列為原序列的一個 **子序列** 。

如果在子序列中，當下標 ix > iy 時，S<sub>ix</sub> > S<sub>iy</sub>，稱子序列為原序列的一個 **遞增子序列** 。

定義一個數組 dp 存儲最長遞增子序列的長度，dp[n] 表示以 S<sub>n</sub> 結尾的序列的最長遞增子序列長度。對於一個遞增子序列 {S<sub>i1</sub>, S<sub>i2</sub>,...,S<sub>im</sub>}，如果 im < n 並且 S<sub>im</sub> < S<sub>n</sub>，此時 {S<sub>i1</sub>, S<sub>i2</sub>,..., S<sub>im</sub>, S<sub>n</sub>} 為一個遞增子序列，遞增子序列的長度增加 1。滿足上述條件的遞增子序列中，長度最長的那個遞增子序列就是要找的，在長度最長的遞增子序列上加上 S<sub>n</sub> 就構成了以 S<sub>n</sub> 為結尾的最長遞增子序列。因此 dp[n] = max{ dp[i]+1 | S<sub>i</sub> < S<sub>n</sub> && i < n} 。

因為在求 dp[n] 時可能無法找到一個滿足條件的遞增子序列，此時 {S<sub>n</sub>} 就構成了遞增子序列，需要對前面的求解方程做修改，令 dp[n] 最小為 1，即：

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[n]=max\{1,dp[i]+1|S_i<S_n\&\&i<n\}" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ee994da4-0fc7-443d-ac56-c08caf00a204.jpg" width="350px"> </div><br>

對於一個長度為 N 的序列，最長遞增子序列並不一定會以 S<sub>N</sub> 為結尾，因此 dp[N] 不是序列的最長遞增子序列的長度，需要遍歷 dp 數組找出最大值才是所要的結果，max{ dp[i] | 1 <= i <= N} 即為所求。

## 1. 最長遞增子序列

[300. Longest Increasing Subsequence (Medium)](https://leetcode.com/problems/longest-increasing-subsequence/description/)

```java
public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];
    for (int i = 0; i < n; i++) {
        int max = 1;
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                max = Math.max(max, dp[j] + 1);
            }
        }
        dp[i] = max;
    }
    return Arrays.stream(dp).max().orElse(0);
}
```

使用 Stream 求最大值會導致運行時間過長，可以改成以下形式：

```java
int ret = 0;
for (int i = 0; i < n; i++) {
    ret = Math.max(ret, dp[i]);
}
return ret;
```

以上解法的時間複雜度為 O(N<sup>2</sup>)，可以使用二分查找將時間複雜度降低為 O(NlogN)。

定義一個 tails 數組，其中 tails[i] 存儲長度為 i + 1 的最長遞增子序列的最後一個元素。對於一個元素 x，

- 如果它大於 tails 數組所有的值，那麼把它添加到 tails 後面，表示最長遞增子序列長度加 1；
- 如果 tails[i-1] < x <= tails[i]，那麼更新 tails[i] = x。

例如對於數組 [4,3,6,5]，有：

```html
tails      len      num
[]         0        4
[4]        1        3
[3]        1        6
[3,6]      2        5
[3,5]      2        null
```

可以看出 tails 數組保持有序，因此在查找 S<sub>i</sub> 位於 tails 數組的位置時就可以使用二分查找。

```java
public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] tails = new int[n];
    int len = 0;
    for (int num : nums) {
        int index = binarySearch(tails, len, num);
        tails[index] = num;
        if (index == len) {
            len++;
        }
    }
    return len;
}

private int binarySearch(int[] tails, int len, int key) {
    int l = 0, h = len;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (tails[mid] == key) {
            return mid;
        } else if (tails[mid] > key) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

## 2. 一組整數對能夠構成的最長鏈

[646. Maximum Length of Pair Chain (Medium)](https://leetcode.com/problems/maximum-length-of-pair-chain/description/)

```html
Input: [[1,2], [2,3], [3,4]]
Output: 2
Explanation: The longest chain is [1,2] -> [3,4]
```

題目描述：對於 (a, b) 和 (c, d) ，如果 b < c，則它們可以構成一條鏈。

```java
public int findLongestChain(int[][] pairs) {
    if (pairs == null || pairs.length == 0) {
        return 0;
    }
    Arrays.sort(pairs, (a, b) -> (a[0] - b[0]));
    int n = pairs.length;
    int[] dp = new int[n];
    Arrays.fill(dp, 1);
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (pairs[j][1] < pairs[i][0]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }
    return Arrays.stream(dp).max().orElse(0);
}
```

## 3. 最長擺動子序列

[376. Wiggle Subsequence (Medium)](https://leetcode.com/problems/wiggle-subsequence/description/)

```html
Input: [1,7,4,9,2,5]
Output: 6
The entire sequence is a wiggle sequence.

Input: [1,17,5,10,13,15,10,5,16,8]
Output: 7
There are several subsequences that achieve this length. One is [1,17,10,13,10,16,8].

Input: [1,2,3,4,5,6,7,8,9]
Output: 2
```

要求：使用 O(N) 時間複雜度求解。

```java
public int wiggleMaxLength(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int up = 1, down = 1;
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > nums[i - 1]) {
            up = down + 1;
        } else if (nums[i] < nums[i - 1]) {
            down = up + 1;
        }
    }
    return Math.max(up, down);
}
```

# 最長公共子序列

對於兩個子序列 S1 和 S2，找出它們最長的公共子序列。

定義一個二維數組 dp 用來存儲最長公共子序列的長度，其中 dp[i][j] 表示 S1 的前 i 個字符與 S2 的前 j 個字符最長公共子序列的長度。考慮 S1<sub>i</sub> 與 S2<sub>j</sub> 值是否相等，分為兩種情況：

- 當 S1<sub>i</sub>==S2<sub>j</sub> 時，那麼就能在 S1 的前 i-1 個字符與 S2 的前 j-1 個字符最長公共子序列的基礎上再加上 S1<sub>i</sub> 這個值，最長公共子序列長度加 1，即 dp[i][j] = dp[i-1][j-1] + 1。
- 當 S1<sub>i</sub> != S2<sub>j</sub> 時，此時最長公共子序列為 S1 的前 i-1 個字符和 S2 的前 j 個字符最長公共子序列，或者 S1 的前 i 個字符和 S2 的前 j-1 個字符最長公共子序列，取它們的最大者，即 dp[i][j] = max{ dp[i-1][j], dp[i][j-1] }。

綜上，最長公共子序列的狀態轉移方程為：

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i][j]=\left\{\begin{array}{rcl}dp[i-1][j-1]&&{S1_i==S2_j}\\max(dp[i-1][j],dp[i][j-1])&&{S1_i<>S2_j}\end{array}\right." class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ecd89a22-c075-4716-8423-e0ba89230e9a.jpg" width="450px"> </div><br>

對於長度為 N 的序列 S<sub>1</sub> 和長度為 M 的序列 S<sub>2</sub>，dp[N][M] 就是序列 S<sub>1</sub> 和序列 S<sub>2</sub> 的最長公共子序列長度。

與最長遞增子序列相比，最長公共子序列有以下不同點：

- 針對的是兩個序列，求它們的最長公共子序列。
- 在最長遞增子序列中，dp[i] 表示以 S<sub>i</sub> 為結尾的最長遞增子序列長度，子序列必須包含 S<sub>i</sub> ；在最長公共子序列中，dp[i][j] 表示 S1 中前 i 個字符與 S2 中前 j 個字符的最長公共子序列長度，不一定包含 S1<sub>i</sub> 和 S2<sub>j</sub>。
- 在求最終解時，最長公共子序列中 dp[N][M] 就是最終解，而最長遞增子序列中 dp[N] 不是最終解，因為以 S<sub>N</sub> 為結尾的最長遞增子序列不一定是整個序列最長遞增子序列，需要遍歷一遍 dp 數組找到最大者。

```java
public int lengthOfLCS(int[] nums1, int[] nums2) {
    int n1 = nums1.length, n2 = nums2.length;
    int[][] dp = new int[n1 + 1][n2 + 1];
    for (int i = 1; i <= n1; i++) {
        for (int j = 1; j <= n2; j++) {
            if (nums1[i - 1] == nums2[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[n1][n2];
}
```

# 0-1 揹包

有一個容量為 N 的揹包，要用這個揹包裝下物品的價值最大，這些物品有兩個屬性：體積 w 和價值 v。

定義一個二維數組 dp 存儲最大價值，其中 dp[i][j] 表示前 i 件物品體積不超過 j 的情況下能達到的最大價值。設第 i 件物品體積為 w，價值為 v，根據第 i 件物品是否添加到揹包中，可以分兩種情況討論：

- 第 i 件物品沒添加到揹包，總體積不超過 j 的前 i 件物品的最大價值就是總體積不超過 j 的前 i-1 件物品的最大價值，dp[i][j] = dp[i-1][j]。
- 第 i 件物品添加到揹包中，dp[i][j] = dp[i-1][j-w] + v。

第 i 件物品可添加也可以不添加，取決於哪種情況下最大價值更大。因此，0-1 揹包的狀態轉移方程為：

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[i][j]=max(dp[i-1][j],dp[i-1][j-w]+v)" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/8cb2be66-3d47-41ba-b55b-319fc68940d4.png" width="400px"> </div><br>

```java
// W 為揹包總體積
// N 為物品數量
// weights 數組存儲 N 個物品的重量
// values 數組存儲 N 個物品的價值
public int knapsack(int W, int N, int[] weights, int[] values) {
    int[][] dp = new int[N + 1][W + 1];
    for (int i = 1; i <= N; i++) {
        int w = weights[i - 1], v = values[i - 1];
        for (int j = 1; j <= W; j++) {
            if (j >= w) {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - w] + v);
            } else {
                dp[i][j] = dp[i - 1][j];
            }
        }
    }
    return dp[N][W];
}
```

**空間優化** 

在程序實現時可以對 0-1 揹包做優化。觀察狀態轉移方程可以知道，前 i 件物品的狀態僅與前 i-1 件物品的狀態有關，因此可以將 dp 定義為一維數組，其中 dp[j] 既可以表示 dp[i-1][j] 也可以表示 dp[i][j]。此時，

<!--<div align="center"><img src="https://latex.codecogs.com/gif.latex?dp[j]=max(dp[j],dp[j-w]+v)" class="mathjax-pic"/></div> <br>-->

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9ae89f16-7905-4a6f-88a2-874b4cac91f4.jpg" width="300px"> </div><br>

因為 dp[j-w] 表示 dp[i-1][j-w]，因此不能先求 dp[i][j-w]，防止將 dp[i-1][j-w] 覆蓋。也就是說要先計算 dp[i][j] 再計算 dp[i][j-w]，在程序實現時需要按倒序來循環求解。

```java
public int knapsack(int W, int N, int[] weights, int[] values) {
    int[] dp = new int[W + 1];
    for (int i = 1; i <= N; i++) {
        int w = weights[i - 1], v = values[i - 1];
        for (int j = W; j >= 1; j--) {
            if (j >= w) {
                dp[j] = Math.max(dp[j], dp[j - w] + v);
            }
        }
    }
    return dp[W];
}
```

**無法使用貪心算法的解釋** 

0-1 揹包問題無法使用貪心算法來求解，也就是說不能按照先添加性價比最高的物品來達到最優，這是因為這種方式可能造成揹包空間的浪費，從而無法達到最優。考慮下面的物品和一個容量為 5 的揹包，如果先添加物品 0 再添加物品 1，那麼只能存放的價值為 16，浪費了大小為 2 的空間。最優的方式是存放物品 1 和物品 2，價值為 22.

| id | w | v | v/w |
| --- | --- | --- | --- |
| 0 | 1 | 6 | 6 |
| 1 | 2 | 10 | 5 |
| 2 | 3 | 12 | 4 |

**變種** 

- 完全揹包：物品數量為無限個

- 多重揹包：物品數量有限制

- 多維費用揹包：物品不僅有重量，還有體積，同時考慮這兩種限制

- 其它：物品之間相互約束或者依賴

## 1. 劃分數組為和相等的兩部分

[416. Partition Equal Subset Sum (Medium)](https://leetcode.com/problems/partition-equal-subset-sum/description/)

```html
Input: [1, 5, 11, 5]

Output: true

Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

可以看成一個揹包大小為 sum/2 的 0-1 揹包問題。

```java
public boolean canPartition(int[] nums) {
    int sum = computeArraySum(nums);
    if (sum % 2 != 0) {
        return false;
    }
    int W = sum / 2;
    boolean[] dp = new boolean[W + 1];
    dp[0] = true;
    for (int num : nums) {                 // 0-1 揹包一個物品只能用一次
        for (int i = W; i >= num; i--) {   // 從後往前，先計算 dp[i] 再計算 dp[i-num]
            dp[i] = dp[i] || dp[i - num];
        }
    }
    return dp[W];
}

private int computeArraySum(int[] nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    return sum;
}
```

## 2. 改變一組數的正負號使得它們的和為一給定數

[494. Target Sum (Medium)](https://leetcode.com/problems/target-sum/description/)

```html
Input: nums is [1, 1, 1, 1, 1], S is 3.
Output: 5
Explanation:

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3

There are 5 ways to assign symbols to make the sum of nums be target 3.
```

該問題可以轉換為 Subset Sum 問題，從而使用 0-1 揹包的方法來求解。

可以將這組數看成兩部分，P 和 N，其中 P 使用正號，N 使用負號，有以下推導：

```html
                  sum(P) - sum(N) = target
sum(P) + sum(N) + sum(P) - sum(N) = target + sum(P) + sum(N)
                       2 * sum(P) = target + sum(nums)
```

因此只要找到一個子集，令它們都取正號，並且和等於 (target + sum(nums))/2，就證明存在解。

```java
public int findTargetSumWays(int[] nums, int S) {
    int sum = computeArraySum(nums);
    if (sum < S || (sum + S) % 2 == 1) {
        return 0;
    }
    int W = (sum + S) / 2;
    int[] dp = new int[W + 1];
    dp[0] = 1;
    for (int num : nums) {
        for (int i = W; i >= num; i--) {
            dp[i] = dp[i] + dp[i - num];
        }
    }
    return dp[W];
}

private int computeArraySum(int[] nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    return sum;
}
```

DFS 解法：

```java
public int findTargetSumWays(int[] nums, int S) {
    return findTargetSumWays(nums, 0, S);
}

private int findTargetSumWays(int[] nums, int start, int S) {
    if (start == nums.length) {
        return S == 0 ? 1 : 0;
    }
    return findTargetSumWays(nums, start + 1, S + nums[start])
            + findTargetSumWays(nums, start + 1, S - nums[start]);
}
```

## 3. 01 字符構成最多的字符串

[474. Ones and Zeroes (Medium)](https://leetcode.com/problems/ones-and-zeroes/description/)

```html
Input: Array = {"10", "0001", "111001", "1", "0"}, m = 5, n = 3
Output: 4

Explanation: There are totally 4 strings can be formed by the using of 5 0s and 3 1s, which are "10","0001","1","0"
```

這是一個多維費用的 0-1 揹包問題，有兩個揹包大小，0 的數量和 1 的數量。

```java
public int findMaxForm(String[] strs, int m, int n) {
    if (strs == null || strs.length == 0) {
        return 0;
    }
    int[][] dp = new int[m + 1][n + 1];
    for (String s : strs) {    // 每個字符串只能用一次
        int ones = 0, zeros = 0;
        for (char c : s.toCharArray()) {
            if (c == '0') {
                zeros++;
            } else {
                ones++;
            }
        }
        for (int i = m; i >= zeros; i--) {
            for (int j = n; j >= ones; j--) {
                dp[i][j] = Math.max(dp[i][j], dp[i - zeros][j - ones] + 1);
            }
        }
    }
    return dp[m][n];
}
```

## 4. 找零錢的最少硬幣數

[322. Coin Change (Medium)](https://leetcode.com/problems/coin-change/description/)

```html
Example 1:
coins = [1, 2, 5], amount = 11
return 3 (11 = 5 + 5 + 1)

Example 2:
coins = [2], amount = 3
return -1.
```

題目描述：給一些面額的硬幣，要求用這些硬幣來組成給定面額的錢數，並且使得硬幣數量最少。硬幣可以重複使用。

- 物品：硬幣
- 物品大小：面額
- 物品價值：數量

因為硬幣可以重複使用，因此這是一個完全揹包問題。完全揹包只需要將 0-1 揹包的逆序遍歷 dp 數組改為正序遍歷即可。

```java
public int coinChange(int[] coins, int amount) {
    if (amount == 0 || coins == null || coins.length == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    for (int coin : coins) {
        for (int i = coin; i <= amount; i++) { //將逆序遍歷改為正序遍歷
            if (i == coin) {
                dp[i] = 1;
            } else if (dp[i] == 0 && dp[i - coin] != 0) {
                dp[i] = dp[i - coin] + 1;
            } else if (dp[i - coin] != 0) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    return dp[amount] == 0 ? -1 : dp[amount];
}
```

## 5. 找零錢的硬幣數組合

[518\. Coin Change 2 (Medium)](https://leetcode.com/problems/coin-change-2/description/)

```text-html-basic
Input: amount = 5, coins = [1, 2, 5]
Output: 4
Explanation: there are four ways to make up the amount:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

完全揹包問題，使用 dp 記錄可達成目標的組合數目。

```java
public int change(int amount, int[] coins) {
    if (amount == 0 || coins == null || coins.length == 0) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    dp[0] = 1;
    for (int coin : coins) {
        for (int i = coin; i <= amount; i++) {
            dp[i] += dp[i - coin];
        }
    }
    return dp[amount];
}
```

## 6. 字符串按單詞列表分割

[139. Word Break (Medium)](https://leetcode.com/problems/word-break/description/)

```html
s = "leetcode",
dict = ["leet", "code"].
Return true because "leetcode" can be segmented as "leet code".
```

dict 中的單詞沒有使用次數的限制，因此這是一個完全揹包問題。

該問題涉及到字典中單詞的使用順序，也就是說物品必須按一定順序放入揹包中，例如下面的 dict 就不夠組成字符串 "leetcode"：

```html
["lee", "tc", "cod"]
```

求解順序的完全揹包問題時，對物品的迭代應該放在最裡層，對揹包的迭代放在外層，只有這樣才能讓物品按一定順序放入揹包中。

```java
public boolean wordBreak(String s, List<String> wordDict) {
    int n = s.length();
    boolean[] dp = new boolean[n + 1];
    dp[0] = true;
    for (int i = 1; i <= n; i++) {
        for (String word : wordDict) {   // 對物品的迭代應該放在最裡層
            int len = word.length();
            if (len <= i && word.equals(s.substring(i - len, i))) {
                dp[i] = dp[i] || dp[i - len];
            }
        }
    }
    return dp[n];
}
```

## 7. 組合總和

[377. Combination Sum IV (Medium)](https://leetcode.com/problems/combination-sum-iv/description/)

```html
nums = [1, 2, 3]
target = 4

The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

Note that different sequences are counted as different combinations.

Therefore the output is 7.
```

涉及順序的完全揹包。

```java
public int combinationSum4(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int[] maximum = new int[target + 1];
    maximum[0] = 1;
    Arrays.sort(nums);
    for (int i = 1; i <= target; i++) {
        for (int j = 0; j < nums.length && nums[j] <= i; j++) {
            maximum[i] += maximum[i - nums[j]];
        }
    }
    return maximum[target];
}
```

# 股票交易

## 1. 需要冷卻期的股票交易

[309. Best Time to Buy and Sell Stock with Cooldown(Medium)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

題目描述：交易之後需要有一天的冷卻時間。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/83acbb02-872a-4178-b22a-c89c3cb60263.jpg" width="300px"> </div><br>


```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int N = prices.length;
    int[] buy = new int[N];
    int[] s1 = new int[N];
    int[] sell = new int[N];
    int[] s2 = new int[N];
    s1[0] = buy[0] = -prices[0];
    sell[0] = s2[0] = 0;
    for (int i = 1; i < N; i++) {
        buy[i] = s2[i - 1] - prices[i];
        s1[i] = Math.max(buy[i - 1], s1[i - 1]);
        sell[i] = Math.max(buy[i - 1], s1[i - 1]) + prices[i];
        s2[i] = Math.max(s2[i - 1], sell[i - 1]);
    }
    return Math.max(sell[N - 1], s2[N - 1]);
}
```

## 2. 需要交易費用的股票交易

[714. Best Time to Buy and Sell Stock with Transaction Fee (Medium)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

```html
Input: prices = [1, 3, 2, 8, 4, 9], fee = 2
Output: 8
Explanation: The maximum profit can be achieved by:
Buying at prices[0] = 1
Selling at prices[3] = 8
Buying at prices[4] = 4
Selling at prices[5] = 9
The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

題目描述：每交易一次，都要支付一定的費用。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1e2c588c-72b7-445e-aacb-d55dc8a88c29.png" width="300px"> </div><br>

```java
public int maxProfit(int[] prices, int fee) {
    int N = prices.length;
    int[] buy = new int[N];
    int[] s1 = new int[N];
    int[] sell = new int[N];
    int[] s2 = new int[N];
    s1[0] = buy[0] = -prices[0];
    sell[0] = s2[0] = 0;
    for (int i = 1; i < N; i++) {
        buy[i] = Math.max(sell[i - 1], s2[i - 1]) - prices[i];
        s1[i] = Math.max(buy[i - 1], s1[i - 1]);
        sell[i] = Math.max(buy[i - 1], s1[i - 1]) - fee + prices[i];
        s2[i] = Math.max(s2[i - 1], sell[i - 1]);
    }
    return Math.max(sell[N - 1], s2[N - 1]);
}
```


## 3. 只能進行兩次的股票交易

[123. Best Time to Buy and Sell Stock III (Hard)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

```java
public int maxProfit(int[] prices) {
    int firstBuy = Integer.MIN_VALUE, firstSell = 0;
    int secondBuy = Integer.MIN_VALUE, secondSell = 0;
    for (int curPrice : prices) {
        if (firstBuy < -curPrice) {
            firstBuy = -curPrice;
        }
        if (firstSell < firstBuy + curPrice) {
            firstSell = firstBuy + curPrice;
        }
        if (secondBuy < firstSell - curPrice) {
            secondBuy = firstSell - curPrice;
        }
        if (secondSell < secondBuy + curPrice) {
            secondSell = secondBuy + curPrice;
        }
    }
    return secondSell;
}
```

## 4. 只能進行 k 次的股票交易

[188. Best Time to Buy and Sell Stock IV (Hard)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```java
public int maxProfit(int k, int[] prices) {
    int n = prices.length;
    if (k >= n / 2) {   // 這種情況下該問題退化為普通的股票交易問題
        int maxProfit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        return maxProfit;
    }
    int[][] maxProfit = new int[k + 1][n];
    for (int i = 1; i <= k; i++) {
        int localMax = maxProfit[i - 1][0] - prices[0];
        for (int j = 1; j < n; j++) {
            maxProfit[i][j] = Math.max(maxProfit[i][j - 1], prices[j] + localMax);
            localMax = Math.max(localMax, maxProfit[i - 1][j] - prices[j]);
        }
    }
    return maxProfit[k][n - 1];
}
```

# 字符串編輯

## 1. 刪除兩個字符串的字符使它們相等

[583. Delete Operation for Two Strings (Medium)](https://leetcode.com/problems/delete-operation-for-two-strings/description/)

```html
Input: "sea", "eat"
Output: 2
Explanation: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".
```

可以轉換為求兩個字符串的最長公共子序列問題。

```java
public int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            }
        }
    }
    return m + n - 2 * dp[m][n];
}
```

## 2. 編輯距離

[72. Edit Distance (Hard)](https://leetcode.com/problems/edit-distance/description/)

```html
Example 1:

Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation:
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
Example 2:

Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation:
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

題目描述：修改一個字符串成為另一個字符串，使得修改次數最少。一次修改操作包括：插入一個字符、刪除一個字符、替換一個字符。

```java
public int minDistance(String word1, String word2) {
    if (word1 == null || word2 == null) {
        return 0;
    }
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 1; i <= m; i++) {
        dp[i][0] = i;
    }
    for (int i = 1; i <= n; i++) {
        dp[0][i] = i;
    }
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = Math.min(dp[i - 1][j - 1], Math.min(dp[i][j - 1], dp[i - 1][j])) + 1;
            }
        }
    }
    return dp[m][n];
}
```

## 3. 複製粘貼字符

[650. 2 Keys Keyboard (Medium)](https://leetcode.com/problems/2-keys-keyboard/description/)

題目描述：最開始只有一個字符 A，問需要多少次操作能夠得到 n 個字符 A，每次操作可以複製當前所有的字符，或者粘貼。

```
Input: 3
Output: 3
Explanation:
Intitally, we have one character 'A'.
In step 1, we use Copy All operation.
In step 2, we use Paste operation to get 'AA'.
In step 3, we use Paste operation to get 'AAA'.
```

```java
public int minSteps(int n) {
    if (n == 1) return 0;
    for (int i = 2; i <= Math.sqrt(n); i++) {
        if (n % i == 0) return i + minSteps(n / i);
    }
    return n;
}
```

```java
public int minSteps(int n) {
    int[] dp = new int[n + 1];
    int h = (int) Math.sqrt(n);
    for (int i = 2; i <= n; i++) {
        dp[i] = i;
        for (int j = 2; j <= h; j++) {
            if (i % j == 0) {
                dp[i] = dp[j] + dp[i / j];
                break;
            }
        }
    }
    return dp[n];
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
