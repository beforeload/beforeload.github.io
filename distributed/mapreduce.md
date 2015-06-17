MapReduce 编程模型
===================

写在前面的话：看了N多MapReduce方面的理论知识，一直想写写自己对MapReduce的理解。

```
map:(K1, V1) -> list(K2, V2)
reduce: (k2, list(V2)) -> list(K2, V2)
```

简而言之就是 ___ 输入-> Mappers -> 中间数据 -> Reducer -> 输出 ___ 这样的一个过程，把输入``(key, value)``经过map和reduce函数转换成另一个或一批``(key, value)``对输出即可。

#### Mapper

Map阶段，MapReduce对任务输入数据分割，切割成固定大小的片段(splits)，对每个split进一步分解成一批键值对``(K1, V1)``。然后Hadoop为每个split创建Map任务(Mapper)，执行自定义的``map()``。

将split中的``(K1, V1)``键值对输入，得到结果为``(K2, V2)``的中间结果。``map()``的功能到这里并没有结束，因为我们在reduce阶段需要的输入格式是``(K2, list(V2))``，所以还需要对Mapper输出结果``(K2, V2)``进行合并(Combine过程)，即将中间结果中有相同key值(如：K2)的多组``(key, value)``对合并成一对(形成``(K2, list(V2))``)。key值范围决定了这些元组分组，对应不同的Reduce任务(Reducer)。

_Tips:_

1. 一个类作为mapper，要继承MapReduceBase基类并实现Mapper接口；
2. Mapper接口负责数据处理阶段。采用形式为``Mapper<K1, V1, K2, V2>`` Java泛型；
3. Mapper只有一个方法——map，用于处理一个单独的键/值对。
```Java
void map(K1 key, V1 value, OutputCollector<K2, V2> output, Reporter reporter)
    throws IOException
```

### Reducer

Reduce阶段，数据整合，排序。然后调用自定义函数``reduce()``，输入``(K2, list(V2))``，得到键值对``<K3, V3>``输出到HDFS上。

_Tips:_

1. Reducers数目在mapred-site.xml中决定，属性是``mapred.reduce.tasks``，默认值是 1，``job.setNumReduceTasks()``方法也可以用于设置，__这是一个很重要的值__；
2. reducer的实现首先必须在MapReduce基类上扩展，允许配置和清理。它必须实现Reducer接口实现__reduce__方法: 
```Java
void reduce(K2 key, Iterator<V2> values, OutputCollector<K3, V3> output, 
        Reporter reporter) throws IOException
```

3. ``reduce()``函数最后生成的列表``(K3, V3)``可能为空；
4. map阶段和reduce阶段中间还有partitioner的工作：负责将mapper的结果输出给不同的reducer。

#### Hadoop的MapReduce

Hadoop框架的核心是Map和Reduce操作，但不仅仅如此，还包括：

___ data spliting(数据分割), shuffling(洗牌), Partitioning(分组), Combining(合并) ___

以及各种格式的输入输出数据。

#### Shuffler

Mapper的按key值分为R份(R即为上面说到的Reducers的数目)，划分时通常采用hash函数，如``Hash(key) mod R``。目的是保证某一范围内的key一定由某个Reducer来处理。

_Tips:_

1. 洗牌之后相同的key对应的键值对放入相同的Reducer，不同的键也可以放入相同的Reducer。具体放入的位置由Partitioner决定。

#### Partitioner:重定向Mapper输出

并不是数据排序好就是最好的。利用并行计算，不能仅仅靠一个reducer，那样就不是“云”而是“雨点”。当多个reducer一起使用时，默认的做法是对键值对进行hash来确定reducer。

_Tips:_

1. Hadoop通过HashPartitioner类强制执行Partitioner策略。但HashPartitioner有时会出错；
2. 量身定制partitioner，只需要实现``configure()``和``getPartition()``两个函数，前者将Hadoop对作业的配置应用在patitioner上，后者返回一个0到reduce任务数之间的整数，指向键/值对将要发送到的reducer。

#### Combiner: 本地reduce

合并Mapper输出，即将多个key相同的``<key, value>``合并成一对。Combine过程和Reduce过程类似，很多情况下可以直接使用reduce函数，但Combiner过程是Mapper的一部分，在map函数后执行。

_Tips：_

1. Hadoop并不保证对一个Mapper执行多少次Combine过程，所以我们应该做到无论Combine过程执行多少次，得到结果都一样；
2. 中间结果的读取，JobTracker介入，负责通知中间文件的位置；
3. Mapper输出结果不在HDFS上而在本地磁盘上，出于时效性考虑，任务结束后删除，而HDFS的备份机制会造成性能损失，没有必要。

#### 讨论

很多时候Rudecer产生的R个结果不是我们真正需要的最终结果，此时会把R个结果作为另一个计算的输入，开始另一个MapReduce任务，即任务管道。

#### 总结

MapReduce的集群行为(即MapReduce运行在大规模集群上的过程)，要完成一个并行计算，需要___任务调度与执行，本地计算，Shuffle，合并Mapper输出，读取中间结果，任务管道等一系列环节共同支撑计算的过程。

