---
title: Add Two Numbers
date: 2018-08-12 16:09:09
tags: Algorithm
categories: learning
---

## Add Tow Numbers

来源：[LeetCode](https://leetcode.com/problems/add-two-numbers/description/)

### 题目：
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

<!-- more -->

### 例子:

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

### 算法:

1. 初始化返回的 NodeList 的头结点
2. 初始化进位`carry`为 0
3. 将列表 `l1` 和 `l2` 头结点赋值给 `p` 和 `q`
4. `p` 或者 `q` 存在遍历列表 `l1` 和 `l2`
    1. `p` 的值赋值给 `x`，`q` 的值赋值给 `y`，`p`或`q`不存在，则赋值为0
    2. 计算`sum = x + y + carry`
    3. `carry = sum / 10`
    4. 创建一个新的节点`newNode`: `newNode.val = sum % 10`,并将当前节点的`next`指向这个节点`newNode`
    5. 当前节点进一位，即： `cur = cur.next`
    6. 如果`p`存在，将`p.next`赋值给`p`
    7. 如果`q`存在，将`q.next`赋值给`q`
5. 如果进位为1，则增加一个新的节点，节点值为 1
6. 返回头结点的next

### code：

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
const addTwoNumbers = function(l1, l2) {
    const dummyHead = new ListNode();
    let carry = 0;
    let sum = 0;
    let p = l1;
    let q = l2;
    let cur = dummyHead;
    while(p || q) {
        const x = p ? p.val : 0;
        const y = q ? q.val : 0;
        sum = x + y + carry;
        carry = Math.floor(sum / 10);
        const newNode = new ListNode(sum % 10);
        cur.next = newNode;
        cur = cur.next;
        if(p) {
            p = p.next; 
        }
        if(q) {
            q = q.next;
        }
    }
    if(carry === 1) {
        cur.next = new ListNode(1);
    }
    
    return dummyHead.next;
};
```