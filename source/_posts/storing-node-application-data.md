title: Storing Node application data
date: 2013-06-15 20:52:00
tags: Node.js
categories: Node.js
---

##浅析Node.js应用的数据存储

主要从以下三点简要分析

1. 内存和文件系统数据存储
2. 传统的关系型数据库存储
3. 非关系型数据库存储

选择一种合适的数据存储机制主要依赖五个因素：

1. 存什么样的数据
2. 数据读写的速度要求以满足足够的性能
3. 多大规模的数据
4. 数据查询方式
5. 数据存储和保存的生命周期

### Relational Database

1. MySQL
2. Postgres

### NoSQL databases

1. Redis
2. MongoDB
3. Mongoose
