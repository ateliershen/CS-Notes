<!-- GFM-TOC -->
* [40. 最小的 K 個數](#40-最小的-k-個數)
* [41.1 數據流中的中位數](#411-數據流中的中位數)
* [41.2 字符流中第一個不重複的字符](#412-字符流中第一個不重複的字符)
* [42. 連續子數組的最大和](#42-連續子數組的最大和)
* [43. 從 1 到 n 整數中 1 出現的次數](#43-從-1-到-n-整數中-1-出現的次數)
* [44. 數字序列中的某一位數字](#44-數字序列中的某一位數字)
* [45. 把數組排成最小的數](#45-把數組排成最小的數)
* [46. 把數字翻譯成字符串](#46-把數字翻譯成字符串)
* [47. 禮物的最大價值](#47-禮物的最大價值)
* [48. 最長不含重複字符的子字符串](#48-最長不含重複字符的子字符串)
* [49. 醜數](#49-醜數)
<!-- GFM-TOC -->


# 40. 最小的 K 個數

[NowCoder](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 解題思路

### 快速選擇

- 複雜度：O(N) + O(1)
- 只有當允許修改數組元素時才可以使用

快速排序的 partition() 方法，會返回一個整數 j 使得 a[l..j-1] 小於等於 a[j]，且 a[j+1..h] 大於等於 a[j]，此時 a[j] 就是數組的第 j 大元素。可以利用這個特性找出數組的第 K 個元素，這種找第 K 個元素的算法稱為快速選擇算法。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int[] nums, int k) {
    ArrayList<Integer> ret = new ArrayList<>();
    if (k > nums.length || k <= 0)
        return ret;
    findKthSmallest(nums, k - 1);
    /* findKthSmallest 會改變數組，使得前 k 個數都是最小的 k 個數 */
    for (int i = 0; i < k; i++)
        ret.add(nums[i]);
    return ret;
}

public void findKthSmallest(int[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k)
            break;
        if (j > k)
            h = j - 1;
        else
            l = j + 1;
    }
}

private int partition(int[] nums, int l, int h) {
    int p = nums[l];     /* 切分元素 */
    int i = l, j = h + 1;
    while (true) {
        while (i != h && nums[++i] < p) ;
        while (j != l && nums[--j] > p) ;
        if (i >= j)
            break;
        swap(nums, i, j);
    }
    swap(nums, l, j);
    return j;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

### 大小為 K 的最小堆

- 複雜度：O(NlogK) + O(K)
- 特別適合處理海量數據

應該使用大頂堆來維護最小堆，而不能直接創建一個小頂堆並設置一個大小，企圖讓小頂堆中的元素都是最小元素。

維護一個大小為 K 的最小堆過程如下：在添加一個元素之後，如果大頂堆的大小大於 K，那麼需要將大頂堆的堆頂元素去除。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int[] nums, int k) {
    if (k > nums.length || k <= 0)
        return new ArrayList<>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>((o1, o2) -> o2 - o1);
    for (int num : nums) {
        maxHeap.add(num);
        if (maxHeap.size() > k)
            maxHeap.poll();
    }
    return new ArrayList<>(maxHeap);
}
```

# 41.1 數據流中的中位數

[NowCoder](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

如何得到一個數據流中的中位數？如果從數據流中讀出奇數個數值，那麼中位數就是所有數值排序之後位於中間的數值。如果從數據流中讀出偶數個數值，那麼中位數就是所有數值排序之後中間兩個數的平均值。

## 解題思路

```java
/* 大頂堆，存儲左半邊元素 */
private PriorityQueue<Integer> left = new PriorityQueue<>((o1, o2) -> o2 - o1);
/* 小頂堆，存儲右半邊元素，並且右半邊元素都大於左半邊 */
private PriorityQueue<Integer> right = new PriorityQueue<>();
/* 當前數據流讀入的元素個數 */
private int N = 0;

public void Insert(Integer val) {
    /* 插入要保證兩個堆存於平衡狀態 */
    if (N % 2 == 0) {
        /* N 為偶數的情況下插入到右半邊。
         * 因為右半邊元素都要大於左半邊，但是新插入的元素不一定比左半邊元素來的大，
         * 因此需要先將元素插入左半邊，然後利用左半邊為大頂堆的特點，取出堆頂元素即為最大元素，此時插入右半邊 */
        left.add(val);
        right.add(left.poll());
    } else {
        right.add(val);
        left.add(right.poll());
    }
    N++;
}

public Double GetMedian() {
    if (N % 2 == 0)
        return (left.peek() + right.peek()) / 2.0;
    else
        return (double) right.peek();
}
```

# 41.2 字符流中第一個不重複的字符

[NowCoder](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

請實現一個函數用來找出字符流中第一個只出現一次的字符。例如，當從字符流中只讀出前兩個字符 "go" 時，第一個只出現一次的字符是 "g"。當從該字符流中讀出前六個字符“google" 時，第一個只出現一次的字符是 "l"。

## 解題思路

```java
private int[] cnts = new int[256];
private Queue<Character> queue = new LinkedList<>();

public void Insert(char ch) {
    cnts[ch]++;
    queue.add(ch);
    while (!queue.isEmpty() && cnts[queue.peek()] > 1)
        queue.poll();
}

public char FirstAppearingOnce() {
    return queue.isEmpty() ? '#' : queue.peek();
}
```

# 42. 連續子數組的最大和

[NowCoder](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

{6, -3, -2, 7, -15, 1, 2, 2}，連續子數組的最大和為 8（從第 0 個開始，到第 3 個為止）。

## 解題思路

```java
public int FindGreatestSumOfSubArray(int[] nums) {
    if (nums == null || nums.length == 0)
        return 0;
    int greatestSum = Integer.MIN_VALUE;
    int sum = 0;
    for (int val : nums) {
        sum = sum <= 0 ? val : sum + val;
        greatestSum = Math.max(greatestSum, sum);
    }
    return greatestSum;
}
```

# 43. 從 1 到 n 整數中 1 出現的次數

[NowCoder](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 解題思路

```java
public int NumberOf1Between1AndN_Solution(int n) {
    int cnt = 0;
    for (int m = 1; m <= n; m *= 10) {
        int a = n / m, b = n % m;
        cnt += (a + 8) / 10 * m + (a % 10 == 1 ? b + 1 : 0);
    }
    return cnt;
}
```

> [Leetcode : 233. Number of Digit One](https://leetcode.com/problems/number-of-digit-one/discuss/64381/4+-lines-O(log-n)-C++JavaPython)

# 44. 數字序列中的某一位數字

## 題目描述

數字以 0123456789101112131415... 的格式序列化到一個字符串中，求這個字符串的第 index 位。

## 解題思路

```java
public int getDigitAtIndex(int index) {
    if (index < 0)
        return -1;
    int place = 1;  // 1 表示個位，2 表示 十位...
    while (true) {
        int amount = getAmountOfPlace(place);
        int totalAmount = amount * place;
        if (index < totalAmount)
            return getDigitAtIndex(index, place);
        index -= totalAmount;
        place++;
    }
}

/**
 * place 位數的數字組成的字符串長度
 * 10, 90, 900, ...
 */
private int getAmountOfPlace(int place) {
    if (place == 1)
        return 10;
    return (int) Math.pow(10, place - 1) * 9;
}

/**
 * place 位數的起始數字
 * 0, 10, 100, ...
 */
private int getBeginNumberOfPlace(int place) {
    if (place == 1)
        return 0;
    return (int) Math.pow(10, place - 1);
}

/**
 * 在 place 位數組成的字符串中，第 index 個數
 */
private int getDigitAtIndex(int index, int place) {
    int beginNumber = getBeginNumberOfPlace(place);
    int shiftNumber = index / place;
    String number = (beginNumber + shiftNumber) + "";
    int count = index % place;
    return number.charAt(count) - '0';
}
```

# 45. 把數組排成最小的數

[NowCoder](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

輸入一個正整數數組，把數組裡所有數字拼接起來排成一個數，打印能拼接出的所有數字中最小的一個。例如輸入數組 {3，32，321}，則打印出這三個數字能排成的最小數字為 321323。

## 解題思路

可以看成是一個排序問題，在比較兩個字符串 S1 和 S2 的大小時，應該比較的是 S1+S2 和 S2+S1 的大小，如果 S1+S2 < S2+S1，那麼應該把 S1 排在前面，否則應該把 S2 排在前面。

```java
public String PrintMinNumber(int[] numbers) {
    if (numbers == null || numbers.length == 0)
        return "";
    int n = numbers.length;
    String[] nums = new String[n];
    for (int i = 0; i < n; i++)
        nums[i] = numbers[i] + "";
    Arrays.sort(nums, (s1, s2) -> (s1 + s2).compareTo(s2 + s1));
    String ret = "";
    for (String str : nums)
        ret += str;
    return ret;
}
```

# 46. 把數字翻譯成字符串

[Leetcode](https://leetcode.com/problems/decode-ways/description/)

## 題目描述

給定一個數字，按照如下規則翻譯成字符串：1 翻譯成“a”，2 翻譯成“b”... 26 翻譯成“z”。一個數字有多種翻譯可能，例如 12258 一共有 5 種，分別是 abbeh，lbeh，aveh，abyh，lyh。實現一個函數，用來計算一個數字有多少種不同的翻譯方法。

## 解題思路

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0)
        return 0;
    int n = s.length();
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = s.charAt(0) == '0' ? 0 : 1;
    for (int i = 2; i <= n; i++) {
        int one = Integer.valueOf(s.substring(i - 1, i));
        if (one != 0)
            dp[i] += dp[i - 1];
        if (s.charAt(i - 2) == '0')
            continue;
        int two = Integer.valueOf(s.substring(i - 2, i));
        if (two <= 26)
            dp[i] += dp[i - 2];
    }
    return dp[n];
}
```

# 47. 禮物的最大價值

[NowCoder](https://www.nowcoder.com/questionTerminal/72a99e28381a407991f2c96d8cb238ab)

## 題目描述

在一個 m\*n 的棋盤的每一個格都放有一個禮物，每個禮物都有一定價值（大於 0）。從左上角開始拿禮物，每次向右或向下移動一格，直到右下角結束。給定一個棋盤，求拿到禮物的最大價值。例如，對於如下棋盤

```
1    10   3    8
12   2    9    6
5    7    4    11
3    7    16   5
```

禮物的最大價值為 1+12+5+7+7+16+5=53。

## 解題思路

應該用動態規劃求解，而不是深度優先搜索，深度優先搜索過於複雜，不是最優解。

```java
public int getMost(int[][] values) {
    if (values == null || values.length == 0 || values[0].length == 0)
        return 0;
    int n = values[0].length;
    int[] dp = new int[n];
    for (int[] value : values) {
        dp[0] += value[0];
        for (int i = 1; i < n; i++)
            dp[i] = Math.max(dp[i], dp[i - 1]) + value[i];
    }
    return dp[n - 1];
}
```

# 48. 最長不含重複字符的子字符串

## 題目描述

輸入一個字符串（只包含 a\~z 的字符），求其最長不含重複字符的子字符串的長度。例如對於 arabcacfr，最長不含重複字符的子字符串為 acfr，長度為 4。

## 解題思路

```java
public int longestSubStringWithoutDuplication(String str) {
    int curLen = 0;
    int maxLen = 0;
    int[] preIndexs = new int[26];
    Arrays.fill(preIndexs, -1);
    for (int curI = 0; curI < str.length(); curI++) {
        int c = str.charAt(curI) - 'a';
        int preI = preIndexs[c];
        if (preI == -1 || curI - preI > curLen) {
            curLen++;
        } else {
            maxLen = Math.max(maxLen, curLen);
            curLen = curI - preI;
        }
        preIndexs[c] = curI;
    }
    maxLen = Math.max(maxLen, curLen);
    return maxLen;
}
```

# 49. 醜數

[NowCoder](https://www.nowcoder.com/practice/6aa9e04fc3794f68acf8778237ba065b?tpId=13&tqId=11186&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

把只包含因子 2、3 和 5 的數稱作醜數（Ugly Number）。例如 6、8 都是醜數，但 14 不是，因為它包含因子 7。習慣上我們把 1 當做是第一個醜數。求按從小到大的順序的第 N 個醜數。

## 解題思路

```java
public int GetUglyNumber_Solution(int N) {
    if (N <= 6)
        return N;
    int i2 = 0, i3 = 0, i5 = 0;
    int[] dp = new int[N];
    dp[0] = 1;
    for (int i = 1; i < N; i++) {
        int next2 = dp[i2] * 2, next3 = dp[i3] * 3, next5 = dp[i5] * 5;
        dp[i] = Math.min(next2, Math.min(next3, next5));
        if (dp[i] == next2)
            i2++;
        if (dp[i] == next3)
            i3++;
        if (dp[i] == next5)
            i5++;
    }
    return dp[N - 1];
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
