---
title: KMP字符串匹配算法
date: 2017-08-13 17:30:38
tags: 
    - kmp
    - leetcode
categories: 技术
---

### 字符串匹配
字符串匹配是算法题中常考的一个类型，而且通常会在更复杂的题目中和其他类型的问题搭配出现。
举例来说，有一个字符串"BBC ABCDAB ABCDABCDABDE"，来检测这其中是否含有另一个字符串"ABCDABD"就是字符串的匹配问题。

#### Brust Force
**原字符串:**   "BBC ABCDAB ABCDABCDABDE" 长度: m  
**匹配字符串:** "ABCDABD"                 长度: n   
**时间复杂度为:** O(mn)  
假设原字符串和匹配字符串的长度分别为m和n，暴力解法brust force通常是从第一个字符开始检测，如果遇到不匹配，则将用于匹配的字符向后移动一位，再继续从头开始匹配，直到找到完全匹配的位置或遍历完整个字符串，时间复杂度为mn。

<!--more-->

#### KMP
**原字符串:**   "BBC ABCDAB ABCDABCDABDE" 长度: m  
**匹配字符串:** "ABCDABD"                 长度: n   
**时间复杂度为:** O(m + n)  
与暴力解法不同，KMP在遇到不匹配的部位时，不是直接向后移动一位，而是根据一个部分匹配值表，来确定移动的位置，具体算法步骤如下:  

##### 具体步骤

1.
```
BBC ABCDAB ABCDABCDABDE
|
ABCDABD
```
首先，字符串"BBC ABCDAB ABCDABCDABDE"的第一个字符与搜索词"ABCDABD"的第一个字符，进行比较。因为B与A不匹配，所以搜索词后移一位。  

2.
```
BBC ABCDAB ABCDABCDABDE
 |
 ABCDABD
```
因为B与A不匹配，搜索词再往后移。


3.
```
BBC ABCDAB ABCDABCDABDE
    |
    ABCDABD
```
就这样，直到字符串有一个字符，与搜索词的第一个字符相同为止。

4.
```
BBC ABCDAB ABCDABCDABDE
     |
    ABCDABD
```
接着比较字符串和搜索词的下一个字符，还是相同。

5.
```
BBC ABCDAB ABCDABCDABDE
          |
    ABCDABD
```
直到字符串有一个字符，与搜索词对应的字符不相同为止。  
一个基本事实是，当空格与D不匹配时，你其实知道前面六个字符是"ABCDAB"。KMP算法的想法是，设法利用这个已知信息，不要把"搜索位置"移回已经比较过的位置，继续把它向后移，这样就提高了效率。

6.
上诉所说的已知信息指的就是部分匹配值:  

|搜索词     | A | B | C | D | A | B | D |
|:---------|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|部分匹配值 | 0 | 0 | 0 | 0 | 1 | 2 | 0 |

部分匹配值具体如何计算下面会讲到。

7.
```
BBC ABCDAB ABCDABCDABDE
          |
    ABCDABD
```
当空格与D不匹配时，查表可以最后一个匹配字符B对应的部分匹配值为2，因此可以按照下面的公式计算出匹配字符串应该向后移动的位数:
`移动位数 = 已匹配字符数 - 对应的部分匹配值`
因此在这个例子里，应该讲匹配字符串向后移动 `6 - 2 = 4` 位。

8.
```
BBC ABCDAB ABCDABCDABDE
          |
        ABCDABD
```
因为空格与Ｃ不匹配，搜索词还要继续往后移。这时，已匹配的字符数为2（"AB"），对应的"部分匹配值"为0。所以，移动位数 = 2 - 0，结果为 2，于是将搜索词向后移2位。

9.
```
BBC ABCDAB ABCDABCDABDE
          |
          ABCDABD
```
因为空格与A不匹配，继续后移一位。

10.
```
BBC ABCDAB ABCDABCDABDE
                 |
           ABCDABD
```
逐位比较，直到发现C与D不匹配。于是，移动位数 = 6 - 2，继续将搜索词向后移动4位。

11.
```
BBC ABCDAB ABCDABCDABDE

               ABCDABD
```
逐位比较，直到搜索词的最后一位，发现完全匹配，于是搜索完成。如果还要继续搜索（即找出全部匹配），移动位数 = 7 - 0，再将搜索词向后移动7位，这里就不再重复了。

##### 部分匹配值

|搜索词     | A | B | C | D | A | B | D |
|:---------|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|部分匹配值 | 0 | 0 | 0 | 0 | 1 | 2 | 0 |

`部分匹配值` 就是 `前缀` 和 `后缀` 的最长的共有元素的长度。以"ABCDABD"为例，

```
"A" 的前缀和后缀都为空集，共有元素的长度为0；
"AB" 的前缀为[A]，后缀为[B]，共有元素的长度为0；
"ABC" 的前缀为[A, AB]，后缀为[BC, C]，共有元素的长度0；
"ABCD" 的前缀为[A, AB, ABC]，后缀为[BCD, CD, D]，共有元素的长度为0；
"ABCDA" 的前缀为[A, AB, ABC, ABCD]，后缀为[BCDA, CDA, DA, A]，共有元素为"A"，长度为1；
"ABCDAB" 的前缀为[A, AB, ABC, ABCD, ABCDA]，后缀为[BCDAB, CDAB, DAB, AB, B]，共有元素为"AB"，长度为2；
"ABCDABD" 的前缀为[A, AB, ABC, ABCD, ABCDA, ABCDAB]，后缀为[BCDABD, CDABD, DABD, ABD, BD, D]，共有元素的长度为0。
```

##### 代码实现
Leetcode 28题问题描述:

```
Returns the index of the first occurrence of needle in haystack, or -1 if needle 
is not part of haystack.
```

* 部分匹配值的算法实现：

```java
private int[] generateNext(String needle) {
    int[] next = new int[needle.length()];
    char[] chs = needle.toCharArray();
    int i = 0, j = 1;
    while(j < chs.length) {
        if(chs[i] == chs[j]) {
            next[j] = i + 1;
            i++;
            j++;
        } else {
            if(i == 0) {
                next[j] = 0;
                j++;
            } else {
                i = next[i - 1];
            }
        }
    }
    return next;
}
```
给定一个字符串，通过以上代码可以得出该字符串相应的部分匹配值数组 `next`。

* 字符串匹配

```java
public int strStr(String haystack, String needle) {
    if(haystack.length() < needle.length()) return -1;
    
    int i = 0, j = 0;
    int[] next = generateNext(needle);
    char[] h_chs = haystack.toCharArray(), n_chs = needle.toCharArray();
    while(i < h_chs.length && j < n_chs.length) {
        if(h_chs[i] == n_chs[j]) {
            i++;
            j++;
        } else {
            if(j == 0) {
                i++;
            } else {
                j = next[j - 1];
            }
        }
    }
    return j == n_chs.length? i - n_chs.length : -1;
}
```
根据之前计算出的部分匹配值数组 `next`，可以通过如上代码计算出匹配字符串 `needle` 在原字符串 `haystack` 中相应的匹配位置。

#### 相关Leetcode题目

[Leetcode: 28. Implement strStr()](https://leetcode.com/problems/implement-strstr/description/)  
[Leetcode: 459. Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/description/)

### 相关参考
> * [字符串匹配的KMP算法](http://kb.cnblogs.com/page/176818/)
> 