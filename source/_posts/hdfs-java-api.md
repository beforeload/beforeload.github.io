---
layout: post
title: "Hadoop 的 Java API 使用案例"
description: "HDFS 文件上传、创建、重命名、删除等操作"
date: 2013-04-06 15:14:42
update: 2013-04-13 18:50:12
categories: Distributed Computing
tags: [Java, Hadoop, Distributed Computing]
---

### 案例
1. 上传本地文件到 HDFS
2. 创建 HDFS 文件
3. 重命名 HDFS 文件
4. 删除 HDFS 上的文件
5. 查看 HDFS 文件的最后修改时间
6. 查看某个 HDFS 文件是否存在
7. 查找某个文件在 HDFS 集群的位置
8. 获取 HDFS 集群上所有节点的名称

<!-- more -->

#### 上传本地文件到 HDFS
```Java
public static void copyFile(String src, String dst, String config) throws IOException{
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    Path srcPath = new Path(src);
    Path dstPath = new Path(dst);
    hdfs.copyFromLocalFile(srcPath, dstPath);
    System.out.println("Upload to " + conf.get("fs.default.name"));
    
    FileStatus files[] = hdfs.listStatus(dstPath);
    for (FileStatus file : files) {
        System.out.println(file.getPath());
    }
    hdfs.close();
}
```

#### 创建 HDFS 文件
```Java
public static void createFile(String dst, String config) throws IOException{
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    String content = "Hello World,beforeload";
    byte[] buff = content.getBytes();
    FileSystem hdfs = FileSystem.get(conf);
    Path dfsPath = new Path(dst);
    FSDataOutputStream os = hdfs.create(dfsPath);
    os.write(buff,0,buff.length);
    os.write(content.getBytes("UTF-8"));
    os.close();
    hdfs.close();
}
```

#### 重命名 HDFS 文件
```Java
public static void rename(String oldName, String newName, String config)
        throws IOException {
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    Path oldPath = new Path(oldName);
    Path newPath = new Path(newName);
    boolean isRename = hdfs.rename(oldPath, newPath);
}
```
 
#### 删除 HDFS 上的文件
```Java
public static void deleteFile(String path, String config) throws IOException {
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    Path deletePath = new Path(path);
    boolean isDeleted = hdfs.delete(deletePath, false);
    // 递归删除
    // boolean isDelete = hdfs.delete(deletePath, true);
    System.out.println("delete? "+ isDeleted);
}
```

#### 查看 HDFS 文件的最后修改时间
```Java
public static void getTime(String path, String config) throws IOException{
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    Path filePath = new Path(path);
    FileStatus fileStatus = hdfs.getFileStatus(filePath);
    long modifyTime = fileStatus.getModificationTime();
    System.out.println("Modification time is:" + modifyTime);
}
```

#### 查看某个 HDFS 文件是否存在
```Java
public static void isExist(String path, String config) throws IOException {
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    boolean isExist = hdfs.exists(new Path(path));
    System.out.println("Exist?"+ isExist);
}
```

#### 查找某个文件在 HDFS 集群的位置
```Java
public static void getFileBlockLocation(String path, String config)
        throws IOException {
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem hdfs = FileSystem.get(conf);
    Path filePath = new Path(path);
    FileStatus fileStatus = hdfs.getFileStatus(filePath);
    BlockLocation[] blockLocations = hdfs.getFileBlockLocations(fileStatus,
            0, fileStatus.getLen());

    for (int i = 0; i < blockLocations.length; i++) {
        String[] hosts = blockLocations[i].getHosts();
        System.out.println("block" + i + "location:" + hosts[i]);
    }
}
```

#### 获取 HDFS 集群上所有节点的名称
```Java
public static void getHostName(String config) throws IOException {
    Configuration conf = new Configuration();
    conf.addResource(new Path(config));
    FileSystem fs = FileSystem.get(conf);
    DistributedFileSystem hdfs = (DistributedFileSystem) fs;
    DatanodeInfo[] dataNodeStats = hdfs.getDataNodeStats();
    String[] names = new String[dataNodeStats.length];
    for (int i = 0; i < dataNodeStats.length; i++) {
        names[i] = dataNodeStats[i].getHostName();
        System.out.println("node " + i + " name " + names[i]);
    }
}
```

### 问题
1. "Wrong FS expected: file:///"

这个问题其实严格意义上并不属于API调用方面的问题，具体问题出现的原因不得而知，不过在查阅资料一番后还是得出了问题的解决方法。

* stackoverflow上给出[问题](http://stackoverflow.com/questions/7969519/what-is-the-loading-order-of-the-configuration-files-in-hadoop/7995180#7995180)的解决方法，不过经过尝试后，也只能发出感叹：”It doesn't work!" 
* 幸好在[Doug的博客](http://www.opensourceconnections.com/2013/03/24/hdfs-debugging-wrong-fs-expected-file-exception)上给出了解答，通过添加一行代码即可
```Java
conf.addResource(new Path("/root/hadoop-0.20.2/conf/core-site.xml"));
```

### 讨论

___附源码如下：___
```Java
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.BlockLocation;
import org.apache.hadoop.fs.FSDataOutputStream;
import org.apache.hadoop.fs.FileStatus;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hdfs.DistributedFileSystem;
import org.apache.hadoop.hdfs.protocol.DatanodeInfo;

public class Utils {
	public static void main(String[] args) throws IOException {
		String src, dst1, config, dst2, oldName, newName;
		src = "/root/word.txt";
		dst1 = "/";
		config = "/root/hadoop-0.20.2/conf/core-site.xml";
		// copyFile(src, dst1, config);

		dst2 = "/test.txt";
		// createFile(dst2, config);

		oldName = dst2;
		newName = "/test1.txt";
		// rename(oldName, newName, config);

		// deleteFile(dst2, config);
		// getTime(dst1, config);

		// isExist(dst2, config);
		// isExist(dst1, config);

		// getFileBlockLocation(dst2, config);

		getHostName(config);
	}

	public static void copyFile(String src, String dst, String config)
			throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		Path srcPath = new Path(src);
		Path dstPath = new Path(dst);
		hdfs.copyFromLocalFile(srcPath, dstPath);
		System.out.println("Upload to " + conf.get("fs.default.name"));

		FileStatus files[] = hdfs.listStatus(dstPath);
		for (FileStatus file : files) {
			System.out.println(file.getPath());
		}
		hdfs.close();
	}

	public static void createFile(String dst, String config) throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		String content = "Hello World,beforeload";
		byte[] buff = content.getBytes();
		FileSystem hdfs = FileSystem.get(conf);
		Path dfsPath = new Path(dst);
		FSDataOutputStream os = hdfs.create(dfsPath);
		os.write(buff, 0, buff.length);
		os.write(content.getBytes("UTF-8"));
		os.close();
		hdfs.close();
	}

	public static void rename(String oldName, String newName, String config)
			throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		Path oldPath = new Path(oldName);
		Path newPath = new Path(newName);
		boolean isRename = hdfs.rename(oldPath, newPath);
	}

	public static void deleteFile(String path, String config)
			throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		Path deletePath = new Path(path);
		boolean isDeleted = hdfs.delete(deletePath, false);
		// 递归删除
		// boolean isDelete = hdfs.delete(deletePath, true);
		System.out.println("delete? " + isDeleted);
	}

	public static void getTime(String path, String config) throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		Path filePath = new Path(path);
		FileStatus fileStatus = hdfs.getFileStatus(filePath);
		long modifyTime = fileStatus.getModificationTime();
		System.out.println("Modification time is:" + modifyTime);
	}

	public static void isExist(String path, String config) throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		boolean isExist = hdfs.exists(new Path(path));
		System.out.println("Exist?" + isExist);
	}

	public static void getFileBlockLocation(String path, String config)
			throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem hdfs = FileSystem.get(conf);
		Path filePath = new Path(path);
		FileStatus fileStatus = hdfs.getFileStatus(filePath);
		BlockLocation[] blockLocations = hdfs.getFileBlockLocations(fileStatus,
				0, fileStatus.getLen());

		for (int i = 0; i < blockLocations.length; i++) {
			String[] hosts = blockLocations[i].getHosts();
			System.out.println("block" + i + "location:" + hosts[i]);
		}
	}

	public static void getHostName(String config) throws IOException {
		Configuration conf = new Configuration();
		conf.addResource(new Path(config));
		FileSystem fs = FileSystem.get(conf);
		DistributedFileSystem hdfs = (DistributedFileSystem) fs;
		DatanodeInfo[] dataNodeStats = hdfs.getDataNodeStats();
		String[] names = new String[dataNodeStats.length];
		for (int i = 0; i < dataNodeStats.length; i++) {
			names[i] = dataNodeStats[i].getHostName();
			System.out.println("node " + i + " name " + names[i]);
		}
	}
}
```

####参考书籍：
1. 《Hadoop实战》—— "Hadoop in Action"
2. 《实战Hadoop》—— "开启通向云计算的捷径"
3. [Hadoop Java API 官方文档](http://hadoop.apache.org/core/docs/current/api/org/apache/hadoop/fs/package-summary.html)
