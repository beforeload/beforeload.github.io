title: Inverted Index in Hadoop
date: 2013-04-18 12:26:32
categories: hadoop
tags: [Hadoop, 数据结构]
---

##倒排索引

###简介:
倒排索引是文档检索系统中最常用的数据结构，被广泛地应用于全文搜索引擎。它主要是用来存储某个单词(或词组)在一个文档或一组文档中的存储位置的映射，即提供了一种根据内容来查找文档的方式。由于不是更具文档来确定文档包含的内容，而是进行想法的操作，因而成为倒排索引(Inverted Index)。[1]

我的理解就是找到单词出现的文档的名称，多数情况下为一个列表。

一个单词可能在不同的文件中出现，所以我们需要定义一个权重，表示单词(即搜索的内容)跟文档的___相关度___。相关度的衡量多数情况下用___词频___来表示。

更加复杂的算法TF-IDF(Term Frequency-Inverse Document Frequency)统计单词在多少个文档中出现，甚至考虑单词在文档中出现的位置(例如标题处反应这个单词的重要性)。

理论的东西到此结束，下面写一下倒排索引的设计与实现。

###问题分析：
信息的关键： __单词__，__文档URI__及__词频__

###设计：
####Map过程：
TextInputFormat: 输入文件处理 -> 文本每行的偏移量及其内容   
``<key, value>`` =>  单词，文档URI和词频    
两个值对应三个值，需要增加Combine过程进行词频统计。

___key___： __单词:URI__ (例如：MapReduce:1.txt)   
___value___：__词频__，相同单词词频组成列表传递给Combiner过程，实现的功能类似于WordCount      

####Combine过程：

Combine过程会把相同的key值对应的value值累加    
Map过程得到的结果为    
```
"MapRuduce:file01.txt"  list(1)          =>     "MapReduce:file01.txt"  1    
"is:file01.txt"         list(1,1)        =>     "is:file01.txt"         2    
"powerful:file01.txt"   list(1)          =>     "powerful:file01.txt"   1    
"simple:file01.txt"     list(1)          =>     "simple:file01.txt"     1        
```

___key___： __单词__
___value___: __URI:词频__(如：1.txt:1)

__好处__：可以利用MapReduce框架默认的HashPartitioner类完成Shuffle过程。


####Reduce过程：
Combiner过程就已经把相同的单词的所有记录发送给同一个Reducer进行处理，Reduce过程就变得很简单，只需要将相同的key和value值组合成倒排索引文件所需的格式即可，剩下的交给MapReducer框架自动完成。

####问题
1. 文件数目;
2. 文件大小;
3. Reduce过程没有统计词频，有可能会造成词频未统计完全的单词。    

__备注及解决办法：__

0. 单个文件不宜过大，具体值与默认HDFS块大小及相关配置有关；
1. 重写InputFormat类将每个文件作为一个split；
2. 执行两次MapReduce，第一次统计词频，第二次MapReduce用于生成倒排索引。


####优化思路：
1. 利用复合键值对等实现包含更多信息的倒排索引。


____附Java源码：____

```Java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileSplit;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class InvertedIndex {
	public static class InvertedIndexMapper extends
			Mapper<Object, Text, Text, Text> {
		private Text keyInfo = new Text(); // 存放单词和URI的组合
		private Text valueInfo = new Text(); // 存储词频
		private FileSplit split; // 存储Split对象

		public void map(Object key, Text value, Context context)
				throws IOException, InterruptedException {

			// 获得<key, value>对所属的FileSplit对象
			split = (FileSplit) context.getInputSplit();

			StringTokenizer itr = new StringTokenizer(value.toString());

			while (itr.hasMoreTokens()) {
				// key 值由单词和URI组成，如“MapReduce:1.txt”
				keyInfo.set(itr.nextToken() + ":" 
                        + split.getPath().toString());
			    valueInfo.set("1");
			    context.write(keyInfo, valueInfo);
			}
		}
	}

	public static class InvertedIndexCombiner extends
			Reducer<Text, Text, Text, Text> {
		private Text info = new Text();

		public void reduce(Text key, Iterable<Text> values, Context context)
				throws IOException, InterruptedException {
			// 统计词频
			int sum = 0;
			for (Text value : values) {
				sum += Integer.parseInt(value.toString());
			}

			int splitIndex = key.toString().indexOf(":");
			// 重新设置value值由URI和词频组成
			info.set(key.toString().substring(splitIndex + 1) + ":" + sum);
			// 重新设置key值为单词
			key.set(key.toString().substring(0, splitIndex));
			context.write(key, info);

		}
	}

	public static class InvertedIndexReducer extends
			Reducer<Text, Text, Text, Text> {
		private Text result = new Text();

		public void reduce(Text key, Iterable<Text> values, Context context)
				throws IOException, InterruptedException {
			// 生成文档列表
			String fileList = new String();
			for (Text value : values) {
				fileList += value.toString() + ";";
			}

			result.set(fileList);

			context.write(key, result);
		}
	}

	public static void main(String[] args) throws Exception {
		Configuration conf = new Configuration();
		String[] otherArgs = new GenericOptionsParser(conf, args)
				.getRemainingArgs();
		if (otherArgs.length != 2) {
			System.out.println("Usage: invertedIndex <in> <out>");
			System.exit(2);
		}

		Job job = new Job(conf, "InvertedIndex");
		job.setJarByClass(InvertedIndex.class);
		job.setMapperClass(InvertedIndexMapper.class);
		job.setMapOutputKeyClass(Text.class);
		job.setMapOutputValueClass(Text.class);
		job.setCombinerClass(InvertedIndexCombiner.class);
		job.setReducerClass(InvertedIndexReducer.class);
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(Text.class);

		FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		System.exit(job.waitForCompletion(true) ? 0 : 1);
	}

}
```

__参考：__    
[1]. 《实战Hadoop》

