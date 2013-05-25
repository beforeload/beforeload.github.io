---
layout: post
title: "k-th Order Statistic"
description: "k-th Order Statistic"
date: 2013-04-05 20:12:32
categories: Algorithm
tags: [C, Algorithm]
---

###问题描述:###
######问题来源: [线性查找](http://learn.akae.cn/media/ch11s05.html)###### 

1. 实现一个算法，在一组随机排列的数中找出最小的一个。你能想到的最直观的算法一定是Θ(n)的，想想有没有比Θ(n)更快的算法？
2. 在一组随机排列的数中找出第二小的，这个问题比上一个稍复杂，你能不能想出Θ(n)的算法？
3. 进一步泛化，在一组随机排列的数中找出第k小的，这个元素称为`k-th Order Statistic`。能想到的最直观的算法肯定是先把这些数排序然后取第k个，时间复杂度和排序算法相同，可以是Θ(nlgn)。这个问题虽然比前两个问题复杂，但它也有平均情况下时间复杂度是Θ(n)的算法

<!-- more -->

###求解及算法：###
求一组数中最小值，对于数组而言是一个很easy且常见的问题。
解决算法也很容易:

    int minimum(int arr[])
    {
        int j,temp;
        temp = arr[0];
    
        for (j = 1; j < N; j++) {
            if(temp > arr[j]){
                swap(&temp,&arr[j]);
            }
        }
        return temp;
    }

算法复杂度为Θ(n),且只需要遍历一次即可得出结果；


2. 在数组中求解第二小值也不是一件难事，只要想清楚求解的思路和步骤，问题便可迎刃而解。
当然算法可能性多种多样，我所采用的方法简单粗暴：

```
/** 第二小的数 */
int second_min(int arr[])
{
    int min, smin, i;
        min = smin = arr[0];
    for (i = 0; i < N; i++) {
        if (arr[i] <= min) {
            smin = min;
            min = arr[i];
        }
    }
    return smin;
}
```
在每次找到更小的数字时，把当前的数字赋值给第二小的数字，算法复杂度同样是Θ(n),且只需遍历一次即可得出结果。

3. 对于求`k-th Order Statistic`元素，常规的解法是先排序，后根据排出的顺序，得到第k小的值。时间复杂度等同与排序的时间复杂度。当然，我们不需要完全排出顺序也能得到结果，学过`quick sort`之后，根据`pivot`元素的位置与k值比较，稍作修改快排算法即可得出解法。
求解算法如下：

<pre><code>
int partition(int start, int end)
{
    int pivot, mid, i;
    pivot = arr[start];
    mid = start;
    for (i = start + 1; i &lt;= end; i++) {
        if (arr[i] &lt; pivot) {
            mid++;
            swap(&amp;arr[i], &amp;arr[mid]);
        }
    }
    swap(&amp;arr[mid], &amp;arr[start]);
    return mid;
}

int order_statistic(int start, int end, int k)
{
    int i;
    if (start &lt; end) {
        i = partition(start, end);
        if (k == i) {
            return i;
        } else if (k &gt; i) {
            order_statistic(i + 1, end, k);
        } else {
            order_statistic(start, i - 1, k);
        }
    }
}
</code></pre>

算法复杂度等同与快排是Θ(nlgn)，平均复杂度为Θ(n)。

附上完整C语言代码：

<pre><code>#include &lt;stdio.h&gt;
#define N 10
int arr[N] = { 3, 4, 1, 3, 5, 6, 7, 9, 1, 2 };

int partition(int start, int end)
{
    int pivot, mid, i;
    pivot = arr[start];
    mid = start;
    for (i = start + 1; i &lt;= end; i++) {
        if (arr[i] &lt; pivot) {
            mid++;
            swap(&amp;arr[i], &amp;arr[mid]);
        }
    }
    swap(&amp;arr[mid], &amp;arr[start]);
    return mid;
}

int order_statistic(int start, int end, int k)
{
    int i;
    if (start &lt; end) {
        i = partition(start, end);
        if (k == i) {
            return i;
        } else if (k &gt; i) {
            order_statistic(i + 1, end, k);
        } else {
            order_statistic(start, i - 1, k);
        }
    }
}

int swap(int *a, int *b)
{
    int temp;
    temp = *b;
    *b = *a;
    *a = temp;
}

int main(int argc, const char *argv[])
{
    int i, k = 3, result;
    result = order_statistic(0, N - 1, k);
    for (i = 0; i &lt; N; i++) {
        printf("%d ", arr[i]);
    }
    printf("\n");
    printf("The %d-th Order Statistic is %d.\n", k + 1, arr[result]);
    return 0;
}
</code></pre>

### 讨论

----------------------
