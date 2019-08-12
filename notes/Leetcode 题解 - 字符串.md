<!-- GFM-TOC -->
* [1. 字符串循環移位包含](#1-字符串循環移位包含)
* [2. 字符串循環移位](#2-字符串循環移位)
* [3. 字符串中單詞的翻轉](#3-字符串中單詞的翻轉)
* [4. 兩個字符串包含的字符是否完全相同](#4-兩個字符串包含的字符是否完全相同)
* [5. 計算一組字符集合可以組成的迴文字符串的最大長度](#5-計算一組字符集合可以組成的迴文字符串的最大長度)
* [6. 字符串同構](#6-字符串同構)
* [7. 迴文子字符串個數](#7-迴文子字符串個數)
* [8. 判斷一個整數是否是迴文數](#8-判斷一個整數是否是迴文數)
* [9. 統計二進制字符串中連續 1 和連續 0 數量相同的子字符串個數](#9-統計二進制字符串中連續-1-和連續-0-數量相同的子字符串個數)
<!-- GFM-TOC -->


# 1. 字符串循環移位包含

[編程之美 3.1](#)

```html
s1 = AABCD, s2 = CDAA
Return : true
```

給定兩個字符串 s1 和 s2，要求判定 s2 是否能夠被 s1 做循環移位得到的字符串包含。

s1 進行循環移位的結果是 s1s1 的子字符串，因此只要判斷 s2 是否是 s1s1 的子字符串即可。

# 2. 字符串循環移位

[編程之美 2.17](#)

```html
s = "abcd123" k = 3
Return "123abcd"
```

將字符串向右循環移動 k 位。

將 abcd123 中的 abcd 和 123 單獨翻轉，得到 dcba321，然後對整個字符串進行翻轉，得到 123abcd。

# 3. 字符串中單詞的翻轉

[程序員代碼面試指南](#)

```html
s = "I am a student"
Return "student a am I"
```

將每個單詞翻轉，然後將整個字符串翻轉。

# 4. 兩個字符串包含的字符是否完全相同

[242. Valid Anagram (Easy)](https://leetcode.com/problems/valid-anagram/description/)

```html
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
```

可以用 HashMap 來映射字符與出現次數，然後比較兩個字符串出現的字符數量是否相同。

由於本題的字符串只包含 26 個小寫字符，因此可以使用長度為 26 的整型數組對字符串出現的字符進行統計，不再使用 HashMap。

```java
public boolean isAnagram(String s, String t) {
    int[] cnts = new int[26];
    for (char c : s.toCharArray()) {
        cnts[c - 'a']++;
    }
    for (char c : t.toCharArray()) {
        cnts[c - 'a']--;
    }
    for (int cnt : cnts) {
        if (cnt != 0) {
            return false;
        }
    }
    return true;
}
```

# 5. 計算一組字符集合可以組成的迴文字符串的最大長度

[409. Longest Palindrome (Easy)](https://leetcode.com/problems/longest-palindrome/description/)

```html
Input : "abccccdd"
Output : 7
Explanation : One longest palindrome that can be built is "dccaccd", whose length is 7.
```

使用長度為 256 的整型數組來統計每個字符出現的個數，每個字符有偶數個可以用來構成迴文字符串。

因為迴文字符串最中間的那個字符可以單獨出現，所以如果有單獨的字符就把它放到最中間。

```java
public int longestPalindrome(String s) {
    int[] cnts = new int[256];
    for (char c : s.toCharArray()) {
        cnts[c]++;
    }
    int palindrome = 0;
    for (int cnt : cnts) {
        palindrome += (cnt / 2) * 2;
    }
    if (palindrome < s.length()) {
        palindrome++;   // 這個條件下 s 中一定有單個未使用的字符存在，可以把這個字符放到迴文的最中間
    }
    return palindrome;
}
```

# 6. 字符串同構

[205. Isomorphic Strings (Easy)](https://leetcode.com/problems/isomorphic-strings/description/)

```html
Given "egg", "add", return true.
Given "foo", "bar", return false.
Given "paper", "title", return true.
```

記錄一個字符上次出現的位置，如果兩個字符串中的字符上次出現的位置一樣，那麼就屬於同構。

```java
public boolean isIsomorphic(String s, String t) {
    int[] preIndexOfS = new int[256];
    int[] preIndexOfT = new int[256];
    for (int i = 0; i < s.length(); i++) {
        char sc = s.charAt(i), tc = t.charAt(i);
        if (preIndexOfS[sc] != preIndexOfT[tc]) {
            return false;
        }
        preIndexOfS[sc] = i + 1;
        preIndexOfT[tc] = i + 1;
    }
    return true;
}
```

# 7. 迴文子字符串個數

[647. Palindromic Substrings (Medium)](https://leetcode.com/problems/palindromic-substrings/description/)

```html
Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

從字符串的某一位開始，嘗試著去擴展子字符串。

```java
private int cnt = 0;

public int countSubstrings(String s) {
    for (int i = 0; i < s.length(); i++) {
        extendSubstrings(s, i, i);     // 奇數長度
        extendSubstrings(s, i, i + 1); // 偶數長度
    }
    return cnt;
}

private void extendSubstrings(String s, int start, int end) {
    while (start >= 0 && end < s.length() && s.charAt(start) == s.charAt(end)) {
        start--;
        end++;
        cnt++;
    }
}
```

# 8. 判斷一個整數是否是迴文數

[9. Palindrome Number (Easy)](https://leetcode.com/problems/palindrome-number/description/)

要求不能使用額外空間，也就不能將整數轉換為字符串進行判斷。

將整數分成左右兩部分，右邊那部分需要轉置，然後判斷這兩部分是否相等。

```java
public boolean isPalindrome(int x) {
    if (x == 0) {
        return true;
    }
    if (x < 0 || x % 10 == 0) {
        return false;
    }
    int right = 0;
    while (x > right) {
        right = right * 10 + x % 10;
        x /= 10;
    }
    return x == right || x == right / 10;
}
```

# 9. 統計二進制字符串中連續 1 和連續 0 數量相同的子字符串個數

[696. Count Binary Substrings (Easy)](https://leetcode.com/problems/count-binary-substrings/description/)

```html
Input: "00110011"
Output: 6
Explanation: There are 6 substrings that have equal number of consecutive 1's and 0's: "0011", "01", "1100", "10", "0011", and "01".
```

```java
public int countBinarySubstrings(String s) {
    int preLen = 0, curLen = 1, count = 0;
    for (int i = 1; i < s.length(); i++) {
        if (s.charAt(i) == s.charAt(i - 1)) {
            curLen++;
        } else {
            preLen = curLen;
            curLen = 1;
        }

        if (preLen >= curLen) {
            count++;
        }
    }
    return count;
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>
