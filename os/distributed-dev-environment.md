### 分布式开发环境搭建

#### 基于Hadoop的分布式系统开发环境在Ubuntu 13.04中的搭建

由于实验学习以及开发测试的需求，需要安装集群和伪分布式开发环境。

分为以下安装过程：

1. 安装jdk
2. 安装eclipse
3. 安装hadoop

#### 安装jdk

__(1) 下载JDK__

Ubuntu上下载最好不要使用FTP下载，不然会出现例如下面的问题。

    gzip: stdin: invalid compressed data--format violatedtar: Unexpected EOF in archivetar: Unexpected EOF in archivetar: Error is not recoverable: exiting now

从[官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)上根据自己的系统下载相应的JDK。

    sudo mkdir /usr/java
    tar zxvf jdk-7u25-linux-x64.tar.gz
    sudo mv jdk-7u25-linux-x64.tar.gz /usr/java/

__(2) 然后修改配置文件__

    sudo vi /etc/profile

按G可以直接到文件尾端，填写下面内容:

    export JAVA_HOME=/usr/java/jdk1.7.0_25
    export JRE_HOME=$JAVA_HOME/jre
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin

保存退出。

__(3) 检测是否成功__ 

    source /etc/profile
    java -version

如果显示下面结果，表示安装成功。

    java version "1.7.0_25"
    Java(TM) SE Runtime Environment (build 1.7.0_25-b15)
    Java HotSpot(TM) 64-Bit Server VM (build 23.25-b01, mixed mode)

#### 安装eclipse

__(1) 下载eclipse***.tar.gz__

    tar zxvf eclipse-standard-kepler-R-linux-gtk-x86_64.tar.gz
    mv eclipse /usr/local
    /usr/local/eclipse/eclipse    #启动eclipse

__(2) 通过修改快捷键，可以迅速启动eclipse__

    System Settings -> Keyboard -> Shortcuts -> Custom Shortcuts

___在弹窗中输入：___

    Name: eclipse
    Command: /usr/local/eclipse/eclipse

Apply退出后，`Ctrl+Alt+E`，添加快捷键。以后只需要通过`Ctrl+Alt+E`，即可启动Eclipse。
    
##### ssh 无密码登录

__(1) 安装SSH__
    
    sudo apt-get install openssh-server

如果你想让别的机器访问自己的电脑，只需要把公钥告诉别人，其他机器通过public key加密后，把数据传到自己的机器上，自己的机器用私钥进行解密。

__(2) 生成密钥的步骤也很简单：__
    
    ssh-keygen -t rsa -C "your_email@example.com"

详细步骤请参见[Generating SSH Keys](//help.github.com/articles/generating-ssh-keys)

    cp id_rsa.pub authorized_keys

__(3) 测试一下，是否可以无密码连接__

    ssh localhost

集群配置的时候可以直接从namenode中，把新增的电脑上的public key发到namenode中，添加到它的authorized_keys中，
然后使用`scp`命令拷贝到新增机器上即可。

##### 安装Hadoop

__(1) 下载解压hadoop__

    tar zxvf hadoop-0.20.2.tar.gz    #解压


__(2) 配置四个文件__

1) ~/hadoop-0.20.2/conf/hadoop-env.sh

    export JAVA_HOME=/usr/java/jdk1.7.0_25

2) ~/hadoop-0.20.2/conf/core-site.xml

    <configuration> 
      <property>
        <name>fs.default.name</name>
        <value>hdfs://localhost:9000</value>
      </property>
    </configuration>

3) ~/hadoop-0.20.2/conf/hdfs-site.xml

    <configuration>
      <property>
        <name>dfs.replication</name>
        <value>1</value>
      </property>
    </configuration>

4) ~/hadoop-0.20.2/conf/mapred-site.xml

    <configuration>
      <property>
        <name>mapred.job.tracker</name>
        <value>localhost:9001</value>
      </property>
    </configuration>

__(3) 修改～/.bashrc__

    echo 'export HADOOP_HOME=/home/beforeload/hadoop-0.20.2' >> ~/.bashrc
    echo 'export PATH=$PATH:$HADOOP_HOME/bin' >> ~/.bashrc
    source ~/.bashrc

现在在任何目录下，都可以输入hadoop命令了。

__(4) 格式化HDFS并启动hadoop__

    hadoop namenode -format
    start-all.sh
    jps   #查看运行的进程

__(5) 浏览NameNode 和 JobTracker__

1. NameNode http://localhost:50070/dfshealth.jsp
2. JobTracker http://localhost:50030/jobtracker.jsp

__(6) 重要参数__

    echo $JAVA_HOME     
    echo $HADOOP_HOME

