---
title: Palindrome Number
date: 2018-08-12 17:57:44
tags: Algorithm
categories: learning
---

### 题目：
Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

[来源](https://leetcode.com/problems/palindrome-number/description/)

<!--more -->

### 例子：

__Example 1:__
```
Input: 121
Output: true
```

__Example 2:__
```
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

__Example 3:__
```
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

### 算法

1. 所有的负数，返回 false
2. 所有大于 0 的 10 的倍数，返回 false
3. 初始化回转数`revertNumber`为 0
4. 翻转一半的数字：
    1. 除以 10，余数是最后一位，商作为下次遍历的数字
    2. `revertNumber = revertNumber * 10 + 余数`
    3. 回转数 < 商则回到第一步，继续遍历
5. 判断商 == 回转数 || 商 == 回转数除以10后的商（121这样的case)

### 算法复杂度

* 时间复杂度: *O(log(n))* 每次迭代过程中，我们是除以 10，所有时间复杂度是log以10为底的n
* 空间复杂度：*O(1)*

### 代码

```JavaScript
/**
 * @param {number} x
 * @return {boolean}
 */
var isPalindrome = function(x) {
  if(x < 0 || (x % 10 === 0 && x !== 0)) return false;
  let revertNumber = 0;
  while(revertNumber < x) {
    const y = x % 10;
    x = (x - y) / 10;
    revertNumber = revertNumber * 10 + y;
  }
  
  return revertNumber === x || Math.floor(revertNumber / 10) === x;
};

```