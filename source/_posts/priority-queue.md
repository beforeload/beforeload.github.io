title: Priority Queue
date: 2013-05-30 00:40:13
tags: Algorithm
categories: Algorithm
---

1. 以6.5-1的数据作为输入 实现heapsort, 以及图解排序过程 
2. 实现6.5-3所描述的最小优先级队列接口, 分析各接口所需的时间复杂度 
3. 练习6.5-8 

<!-- more -->

6.5-1 HEAP-EXTRACT-MAX

    if head-size[A] < 1
        then error
    max <-- A[1]
    A[1] <-- heap-size[A] - 1
    MAX-HEAPIFY(A, 1)
    return max


{% img heap-exatract-max i.minus.com/imDBy99LrOzWP.jpg %}

6.5-3 HEAP-MINIUM(A)

伪代码书上已经有了

    HEAP-MINIMUM(A)
        return A[1]

利用HEAP-EXTRACT-MAX(A)容易写出HEAP-EXTRACT-MIN(A)

#### HEAP-EXTRACT-MIN(A)

    if heap-size[A] < 1
        then error 
    max <- A[1]
    A[1] <- A[heap-size[A]] - 1
    heap-size[A] <- heap-size[A] - 1
    MIN-HEAPIFY(A, 1)
    return max

同样，由HEAD-INCREASE-KEY很容易就写出HEAD-DECREASE-KEY过程的伪代码：

#### HEAD-DECREASE-KEY

    if key > A[i]
        then error
    A[i] <- key
    while i > 1 and A[PARENT(i)] > A[i]
        do exchange A[i] <-> A[PARENT(i)]
                i <- PARENT(i)

同样，由MAX-HEAP-INSERT容易写出MIN-HEAP-INSERT

#### MIN-HEAP-INSERT

    heap-size[A] <- heap-size[A] + 1
    A[heap-size[A]] <- 无穷
    HEAP-INCREASE-KEY(A, heap-size[A], key)

6.5-8

每一个链表作为一个节点，链表的第一个元素作为key，然后组成一个堆，这个堆的大小为K(k个链表，k个节点)。

链表合并过程思路如下：

1. 通过堆的根节点，获得其链表的第一个元素
2. 以获取的元素作为目标链表的尾端
3. 因为根的第一个元素的key发生改变，对其进行MIN-HEAPFIY操作，递归进行下去。

显然，在每个堆中取一个key组成的堆是实现此过程的一个最小堆。

具体的实现代码实在没有时间写了。

