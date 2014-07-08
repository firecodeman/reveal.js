#CDH4.1.2中File Could not complete问题定位

##问题现象
**HBase Region问题:**Region在做compaction时DFSClient会造成新产生的HFile始终不能关闭，一直huang住并持续输出类似以下的log
```
2014-06-30 23:51:25,558 WARN org.apache.hadoop.hdfs.DFSClient: Error Recovery for block BP-178649112-10.35.66.11-1353498632460:blk_8552768721295466384_58
792739 in pipeline 10.35.66.49:50010, 10.35.66.69:50010, 10.35.66.66:50010: bad datanode 10.35.66.66:50010
2014-06-30 23:51:30,840 INFO org.apache.hadoop.hdfs.DFSClient: Could not complete file /hbase/rt_web_perflog/c2b6b7e5717d30c192d39de756ba2179/.tmp/77b92b
8bf7ad443d9db7f0b416c4491b retrying...
```
**JobTracker问题:**每次在MapReduce、Sqoop或Hive程序执行完毕后都会将jar包复制到history目录中，但在复制过程中会造成DFSClient始终无法正常complete文件，造成huang住且JobTracker无法接收新的Job请求。错误信息如下:
```
2014-05-26 03:14:23,265 WARN org.apache.hadoop.hdfs.DFSClient: DFSOutputStream ResponseProcessor exception  for block BP-178649112-10.35.66.11-1353498632
460:blk_-8849258441238704784_51021934
java.io.IOException: Bad response ERROR for block BP-178649112-10.35.66.11-1353498632460:blk_-8849258441238704784_51021934 from datanode 10.35.66.55:5001
0
    at org.apache.hadoop.hdfs.DFSOutputStream$DataStreamer$ResponseProcessor.run(DFSOutputStream.java:681)
2014-05-26 03:15:19,909 INFO org.apache.hadoop.hdfs.DFSClient: Could not complete file /user/webopa/change_info_pl3/output/period/first/sevenday_20140525
/_logs/history/job_201402251404_311817_1401045017394_webopa_platform_change_info_fat.jar retrying...
```

##问题分析
