layout: post
title: "理解深度优先和广度优先"
description: "从一个简单的迷宫问题入手，深入理解DFS和BFS"
date: 2013-04-09 13:14:20
updated: 2013-04-13 02:49:30
categories: Algorithm
tags:
- C
- Data Structure
- Algorithm
---

#### 问题：
迷宫, 1为墙壁， 0为可以走的路， 只能横着走和竖着走，求解左上角到右下角的路线。
```
int maze[5][5] = {
    0, 1, 0, 0, 0,
    0, 1, 0, 1, 0,
    0, 0, 0, 0, 0,
    0, 0, 0, 1, 0,
};
```

<!-- more -->

### 思路：

__解法一：__

深度优先搜索(DFS, Depth First Search):每次搜索完各个方向相邻的点之后，取其中一个相邻的点走下去，一直走到无路可走了再退回来(回溯)，取另一个相邻的点再走下去。
```
将起点标记为已走过并压栈；
while (栈非空) {
    从栈顶弹出一个点P；
    if (p这个点是终点) {
        break;
    }
    沿右、下、左、上四个方向探索相邻的点;
    if (和p相邻的点有路可走，并且还没走过) {
        将相邻的点标记为已走过并压栈，它的前趋就是p点;
    }
}
if (p点是终点) {
    打印p点的坐标；
    while (p点有前趋) {
        p点 = p点的前趋;
        打印p点的坐标;
    }
} else {
    没有路线可以到达终点;
}
```

___附源码如下：___
```
#include <stdio.h>
#define MAX_ROW 5
#define MAX_COL 5

struct point {
    int row, col;
} stack[512];

int top = 0;

void push(struct point p)
{
	stack[top++] = p;
}

struct point pop(void)
{
	return stack[--top];
}

int is_empty(void)
{
	return top == 0;
}

int maze[MAX_ROW][MAX_COL] = {
	0, 1, 0, 0, 0,
	0, 1, 0, 1, 0,
	0, 0, 0, 0, 0,
	0, 1, 1, 1, 0,
	0, 0, 0, 1, 0,
};

void print_maze(void)
{
	int i, j;
	for (i = 0; i < MAX_ROW; i++) {
		for (j = 0; j < MAX_COL; j++) {
			printf("%d ", maze[i][j]);
		}
		putchar('\n');
	}
	printf("************\n");
}

struct point predecessor[MAX_ROW][MAX_COL] = {
	{ {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}},
	{ {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}},
	{ {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}},
	{ {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}},
	{ {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}, {-1, -1}},
};

void visit(int row, int col, struct point pre)
{
	struct point visit_point = {
		row, col
	};
	maze[row][col] = 2;
	predecessor[row][col] = pre;
	push(visit_point);
}

int main(void)
{
	struct point p = {
		0, 0
	};
	maze[p.row][p.col] = 2;
	push(p);

	while (!is_empty()) {
		p = pop();
		if (p.row == MAX_ROW - 1 && p.col == MAX_COL - 1) {
			break;
		}
		if (p.col + 1 < MAX_COL && maze[p.row][p.col + 1] == 0) {
			visit(p.row, p.col + 1, p);
		}
		if (p.row + 1 < MAX_ROW && maze[p.row + 1][p.col] == 0) {
			visit(p.row + 1, p.col, p);
		}
		if (p.col - 1 >= 0 && maze[p.row][p.col - 1] == 0) {
			visit(p.row, p.col - 1, p);
		}
		if (p.row - 1 >= 0 && maze[p.row - 1][p.col] == 0) {
			visit(p.row - 1, p.col, p);
		}
		print_maze();
	}
	if (p.col == MAX_COL - 1 && p.row == MAX_ROW - 1) {
		printf("(%d, %d)\n", p.row, p.col);
		while (predecessor[p.row][p.col].row != -1) {
			p = predecessor[p.row][p.col];
			printf("(%d, %d)\n", p.row, p.col);
		}
	} else {
		printf("No path!\n");
	}
	return 0;
}
```

__DFS 优化：__

代码没有什么难懂的地方，不过有很多可以优化的地方，例如在predecessor这个数据结构上，浪费了太多的存储空间，可以做以下优化：

1. 重新定义predecessor存储方式
```
struct point predecessor[MAX_ROW][MAX_COL] = { 0 };
```
所有的值定义为0,前趋点为上点，则将他赋值为1，前趋点为下点，则赋值为-1，前趋点为左点，则赋值为2,前趋点为右点，则赋值为-2；通过定义四个不同的值区分前趋点，减少了存储空间，相应的函数也要对应修改即可。

2. 用递归取代predecessor数据结构


__解法二：__
广度优先搜索(BFS, Breadth First Search):
BFS沿各个方向上同时展开搜索，每个可以走通的方向轮流往前走一步。

```
将起点标记为已经走过的队列
while (队列非空) {
    出队一个点p;
    if (p为终点) {
        break;
    }
    否则沿右下左上四个方向探索相邻的点
    if (和p相邻的点有路走，且没有走过) {
        将相邻的点标记为已经走过并入队列，他的前趋就是刚出队的p点;
    }
    if (p点是终点) {
        打印p点的坐标;
        while (p点有前趋) {
            p点 = p 点的前趋;
            打印 p 点的坐标;
        }
    } else {
        没有到达终点的路线;
    }
}
```

BFS相比较于DFS，BFS可以找到从起点到终点的最短路径。

___附源码如下:___
```
#include <stdio.h>
#define MAX_ROW 5
#define MAX_COL 5

struct point {
	int row, col, predecessor;
} queue[512];

int head = 0, tail = 0;

void enqueue(struct point p)
{
	queue[tail++] = p;
}

struct point dequeue(void)
{
	return queue[head++];
}

int is_empty(void)
{
	return head == tail;
}

int maze[MAX_ROW][MAX_COL] = {
	0, 1, 0, 0, 0,
	0, 1, 0, 1, 0,
	0, 0, 0, 0, 0,
	0, 1, 1, 1, 0,
	0, 0, 0, 1, 0,
};

void print_maze(void)
{
	int i, j;
	for (i = 0; i < MAX_ROW; i++) {
		for (j = 0; j < MAX_COL; j++) {
			printf("%d ", maze[i][j]);
		}
		putchar('\n');
	}
	printf("************\n");
}

void visit(int row, int col)
{
	struct point visit_point = {
		row, col, head - 1
	};
	maze[row][col] = 2;
	enqueue(visit_point);
}

int main(void)
{
	struct point p = {
		0, 0, -1
	};
	maze[p.row][p.col] = 2;
	enqueue(p);

	while (!is_empty()) {
		p = dequeue();
		if (p.row == MAX_ROW - 1 && p.col == MAX_COL - 1) {
			break;
		}
		if (p.row + 1 < MAX_ROW && maze[p.row + 1][p.col] == 0) {
			visit(p.row + 1, p.col);
		}
		if (p.col + 1 < MAX_COL && maze[p.row][p.col + 1] == 0) {
			visit(p.row, p.col + 1);
		}
		if (p.col - 1 >= 0 && maze[p.row][p.col - 1] == 0) {
			visit(p.row, p.col - 1);
		}
		if (p.row - 1 >= 0 && maze[p.row - 1][p.col] == 0) {
			visit(p.row - 1, p.col);
		}
		print_maze();
	}
	if (p.row == MAX_ROW - 1 && p.col == MAX_COL - 1) {
		printf("(%d, %d)\n", p.row, p.col);
		while (p.predecessor != -1) {
			p = queue[p.predecessor];
			printf("(%d, %d)\n", p.row, p.col);
		}
	} else {
		printf("No path!\n");
	}
}
```

####参考：
1. 《Linux C编程一站式学习》[栈与队列](http://learn.akae.cn/media/ch12.html)
