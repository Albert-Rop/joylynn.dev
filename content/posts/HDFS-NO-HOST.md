---
title: "hdfs 配置错误引发的思考"
date: 2022-02-17T21:09:55+08:00
draft: true
---
### 背景
线上跑 flink 任务时，flink 任务报错如下：
```shell
java.io.IOException: Incomplete HDFS URI, no host
``` 

# 原因
`core-site.xml` 配置错误，与 `hdfs-default.xml` 不匹配
`core-site.xml` 配置为:
```xml
<property>
	<name>fs.defaultFS</name>
	<value>hdfs://10-1-0-13,10-1-0-17,10-1-0-5:8020</value>
	<final>true</final>
</property>
```

`hdfs-default.xml` 配置为:
```xml
<property>
		 <name>dfs.nameservices</name>
		 <value>hdfsCluster</value>
 </property>
```

# 延伸
`fs.defaultFS` 用途：
	缩短命令行
		  ``` 
          hdfs dfs -ls /
		  # 代替
		  hdfs dfs -ls hdfs://hdfs/
		  ```
	指定默认的文件系统
		默认值为 `file:///`，是本地文件系统
		改为 `hdfs://<address>:<port>/` 指定连接的HDFS
		指定端口，是为 datanote 发送心跳给 namenode 所使用的端口。
	如果是高可用的 URI，则可以为 nameservice ID
		  ```
		  	 <property>
		  		<name>dfs.ha.nn.not-become-active-in-safemode</name>
		  		<value>true</value>
		  	</property>
		  ```
		注意要在 `hdfs-default.xml` 中进行 nameservice 配置
		  ```
		  	<property>
		  	    <name>dfs.nameservices</name>
		  	    <value>hdfsCluster</value>
		  	</property>
		  ```