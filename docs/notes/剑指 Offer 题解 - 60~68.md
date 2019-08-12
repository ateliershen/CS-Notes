<!-- GFM-TOC -->
* [60. n 個骰子的點數](#60-n-個骰子的點數)
* [61. 撲克牌順子](#61-撲克牌順子)
* [62. 圓圈中最後剩下的數](#62-圓圈中最後剩下的數)
* [63. 股票的最大利潤](#63-股票的最大利潤)
* [64. 求 1+2+3+...+n](#64-求-123n)
* [65. 不用加減乘除做加法](#65-不用加減乘除做加法)
* [66. 構建乘積數組](#66-構建乘積數組)
* [67. 把字符串轉換成整數](#67-把字符串轉換成整數)
* [68. 樹中兩個節點的最低公共祖先](#68-樹中兩個節點的最低公共祖先)
<!-- GFM-TOC -->


# 60. n 個骰子的點數

[Lintcode](https://www.lintcode.com/en/problem/dices-sum/)

## 題目描述

把 n 個骰子扔在地上，求點數和為 s 的概率。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/195f8693-5ec4-4987-8560-f25e365879dd.png" width="300px"> </div><br>

## 解題思路

### 動態規劃

使用一個二維數組 dp 存儲點數出現的次數，其中 dp[i][j] 表示前 i 個骰子產生點數 j 的次數。

空間複雜度：O(N<sup>2</sup>)

```java
public List<Map.Entry<Integer, Double>> dicesSum(int n) {
    final int face = 6;
    final int pointNum = face * n;
    long[][] dp = new long[n + 1][pointNum + 1];

    for (int i = 1; i <= face; i++)
        dp[1][i] = 1;

    for (int i = 2; i <= n; i++)
        for (int j = i; j <= pointNum; j++)     /* 使用 i 個骰子最小點數為 i */
            for (int k = 1; k <= face && k <= j; k++)
                dp[i][j] += dp[i - 1][j - k];

    final double totalNum = Math.pow(6, n);
    List<Map.Entry<Integer, Double>> ret = new ArrayList<>();
    for (int i = n; i <= pointNum; i++)
        ret.add(new AbstractMap.SimpleEntry<>(i, dp[n][i] / totalNum));

    return ret;
}
```

### 動態規劃 + 旋轉數組

空間複雜度：O(N)

```java
public List<Map.Entry<Integer, Double>> dicesSum(int n) {
    final int face = 6;
    final int pointNum = face * n;
    long[][] dp = new long[2][pointNum + 1];

    for (int i = 1; i <= face; i++)
        dp[0][i] = 1;

    int flag = 1;                                     /* 旋轉標記 */
    for (int i = 2; i <= n; i++, flag = 1 - flag) {
        for (int j = 0; j <= pointNum; j++)
            dp[flag][j] = 0;                          /* 旋轉數組清零 */

        for (int j = i; j <= pointNum; j++)
            for (int k = 1; k <= face && k <= j; k++)
                dp[flag][j] += dp[1 - flag][j - k];
    }

    final double totalNum = Math.pow(6, n);
    List<Map.Entry<Integer, Double>> ret = new ArrayList<>();
    for (int i = n; i <= pointNum; i++)
        ret.add(new AbstractMap.SimpleEntry<>(i, dp[1 - flag][i] / totalNum));

    return ret;
}
```

# 61. 撲克牌順子

[NowCoder](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

五張牌，其中大小鬼為癩子，牌面為 0。判斷這五張牌是否能組成順子。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/eaa506b6-0747-4bee-81f8-3cda795d8154.png" width="350px"> </div><br>


## 解題思路

```java
public boolean isContinuous(int[] nums) {

    if (nums.length < 5)
        return false;

    Arrays.sort(nums);

    // 統計癩子數量
    int cnt = 0;
    for (int num : nums)
        if (num == 0)
            cnt++;

    // 使用癩子去補全不連續的順子
    for (int i = cnt; i < nums.length - 1; i++) {
        if (nums[i + 1] == nums[i])
            return false;
        cnt -= nums[i + 1] - nums[i] - 1;
    }

    return cnt >= 0;
}
```

# 62. 圓圈中最後剩下的數

[NowCoder](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

讓小朋友們圍成一個大圈。然後，隨機指定一個數 m，讓編號為 0 的小朋友開始報數。每次喊到 m-1 的那個小朋友要出列唱首歌，然後可以在禮品箱中任意的挑選禮物，並且不再回到圈中，從他的下一個小朋友開始，繼續 0...m-1 報數 .... 這樣下去 .... 直到剩下最後一個小朋友，可以不用表演。

## 解題思路

約瑟夫環，圓圈長度為 n 的解可以看成長度為 n-1 的解再加上報數的長度 m。因為是圓圈，所以最後需要對 n 取餘。

```java
public int LastRemaining_Solution(int n, int m) {
    if (n == 0)     /* 特殊輸入的處理 */
        return -1;
    if (n == 1)     /* 遞歸返回條件 */
        return 0;
    return (LastRemaining_Solution(n - 1, m) + m) % n;
}
```

# 63. 股票的最大利潤

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

## 題目描述

可以有一次買入和一次賣出，買入必須在前。求最大收益。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/42661013-750f-420b-b3c1-437e9a11fb65.png" width="220px"> </div><br>

## 解題思路

使用貪心策略，假設第 i 輪進行賣出操作，買入操作價格應該在 i 之前並且價格最低。

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0)
        return 0;
    int soFarMin = prices[0];
    int maxProfit = 0;
    for (int i = 1; i < prices.length; i++) {
        soFarMin = Math.min(soFarMin, prices[i]);
        maxProfit = Math.max(maxProfit, prices[i] - soFarMin);
    }
    return maxProfit;
}
```

# 64. 求 1+2+3+...+n

[NowCoder](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

要求不能使用乘除法、for、while、if、else、switch、case 等關鍵字及條件判斷語句 A ? B : C。

## 解題思路

使用遞歸解法最重要的是指定返回條件，但是本題無法直接使用 if 語句來指定返回條件。

條件與 && 具有短路原則，即在第一個條件語句為 false 的情況下不會去執行第二個條件語句。利用這一特性，將遞歸的返回條件取非然後作為 && 的第一個條件語句，遞歸的主體轉換為第二個條件語句，那麼當遞歸的返回條件為 true 的情況下就不會執行遞歸的主體部分，遞歸返回。

本題的遞歸返回條件為 n <= 0，取非後就是 n > 0；遞歸的主體部分為 sum += Sum_Solution(n - 1)，轉換為條件語句後就是 (sum += Sum_Solution(n - 1)) > 0。

```java
public int Sum_Solution(int n) {
    int sum = n;
    boolean b = (n > 0) && ((sum += Sum_Solution(n - 1)) > 0);
    return sum;
}
```

# 65. 不用加減乘除做加法

[NowCoder](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

寫一個函數，求兩個整數之和，要求不得使用 +、-、\*、/ 四則運算符號。

## 解題思路

a ^ b 表示沒有考慮進位的情況下兩數的和，(a & b) << 1 就是進位。

遞歸會終止的原因是 (a & b) << 1 最右邊會多一個 0，那麼繼續遞歸，進位最右邊的 0 會慢慢增多，最後進位會變為 0，遞歸終止。

```java
public int Add(int a, int b) {
    return b == 0 ? a : Add(a ^ b, (a & b) << 1);
}
```

# 66. 構建乘積數組

[NowCoder](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

給定一個數組 A[0, 1,..., n-1]，請構建一個數組 B[0, 1,..., n-1]，其中 B 中的元素 B[i]=A[0]\*A[1]\*...\*A[i-1]\*A[i+1]\*...\*A[n-1]。要求不能使用除法。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/4240a69f-4d51-4d16-b797-2dfe110f30bd.png" width="250px"> </div><br>


## 解題思路

```java
public int[] multiply(int[] A) {
    int n = A.length;
    int[] B = new int[n];
    for (int i = 0, product = 1; i < n; product *= A[i], i++)       /* 從左往右累乘 */
        B[i] = product;
    for (int i = n - 1, product = 1; i >= 0; product *= A[i], i--)  /* 從右往左累乘 */
        B[i] *= product;
    return B;
}
```

# 67. 把字符串轉換成整數

[NowCoder](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

將一個字符串轉換成一個整數，字符串不是一個合法的數值則返回 0，要求不能使用字符串轉換整數的庫函數。

```html
Iuput:
+2147483647
1a33

Output:
2147483647
0
```

## 解題思路

```java
public int StrToInt(String str) {
    if (str == null || str.length() == 0)
        return 0;
    boolean isNegative = str.charAt(0) == '-';
    int ret = 0;
    for (int i = 0; i < str.length(); i++) {
        char c = str.charAt(i);
        if (i == 0 && (c == '+' || c == '-'))  /* 符號判定 */
            continue;
        if (c < '0' || c > '9')                /* 非法輸入 */
            return 0;
        ret = ret * 10 + (c - '0');
    }
    return isNegative ? -ret : ret;
}
```

# 68. 樹中兩個節點的最低公共祖先

## 解題思路

### 二叉查找樹

[Leetcode : 235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/)

二叉查找樹中，兩個節點 p, q 的公共祖先 root 滿足 root.val >= p.val && root.val <= q.val。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/047faac4-a368-4565-8331-2b66253080d3.jpg" width="220"/> </div><br>

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null)
        return root;
    if (root.val > p.val && root.val > q.val)
        return lowestCommonAncestor(root.left, p, q);
    if (root.val < p.val && root.val < q.val)
        return lowestCommonAncestor(root.right, p, q);
    return root;
}
```

### 普通二叉樹

[Leetcode : 236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

在左右子樹中查找是否存在 p 或者 q，如果 p 和 q 分別在兩個子樹中，那麼就說明根節點就是最低公共祖先。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/d27c99f0-7881-4f2d-9675-c75cbdee3acd.jpg" width="250"/> </div><br>

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q)
        return root;
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    return left == null ? right : right == null ? left : root;
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
