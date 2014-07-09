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
是引用自 https://issues.apache.org/jira/browse/HDFS-5438 的问题原因和会引起的BUG：
>The incremental block reports from data nodes and block commits are asynchronous. This becomes troublesome when the gen stamp for a block is changed during a write pipeline recovery.

- If an incremental block report is delayed from a node but NN had enough replicas already, a report with the old gen stamp may be received after block completion. This replica will be correctly marked corrupt. But if the node had participated in the pipeline recovery, a new (delayed) report with the correct gen stamp will come soon. However, this report won't have any effect on the corrupt state of the replica.
- If block reports are received while the block is still under construction (i.e. client's call to make block committed has not been received by NN), they are blindly accepted regardless of the gen stamp. If a failed node reports in with the old gen stamp while pipeline recovery is on-going, it will be accepted and counted as valid during commit of the block.

>Due to the above two problems, correct replicas can be marked corrupt and corrupt replicas can be accepted during commit. So far we have observed two cases in production.

- The client hangs forever to close a file. All replicas are marked corrupt.
- After the successful close of a file, read fails. Corrupt replicas are accepted during commit and valid replicas are marked corrupt afterward.

> 新增加的block报告和block commit是异步的。当block的GS在pipeline recovery的过程中发生改变时就会有问题。
- 如果新增的block report延迟，NN上的replica信息已经就绪，当block完成后report一个旧的GS block时。这个replica将会标记为异常。如果node参加了pipeline recovery，带有正常GS的新的（延迟的）report将会到达，但是这个report不会对错误状态的replica造成任何影响。
- 当block report收到时block仍然处于under construnction时（例如客户端做的block commit没有被NN接收到）,它们将盲目的接收可能造成问题的GS.如果失败的node在pipeline recovery进行时报告了旧的GS,它将接受并算作是在block commit时的验证的block。

> 以上的两个问题，正确的replica将标记为异常和异常的replica将在commit时接受。将会带来以下两个问题
- client始终hang住，始终无法正常关闭文件。所有的replica都将标记为异常。
- 当关闭文件成功后，也会读取失败。异常的replica在commit时也会被接受，并且过后replica被标记为异常。

> 而在HDFS-5438中主要修正如下：
- NN中增加列表，在开始pipeling recovery时维护已经就续的report。带有新的GS的新的report将不会再被接收，直至replica被恢复，同时它会被标记为异常。

- 如果异常的replica收到block reprot replica不再异常，则NN将其从异常的replica map中移出。
- client不能关闭文件是因为block未达到一定数据的验证的replica数，这将导致一直hang住。原本其中在新建block时会有一定次数的重试，同样的也在completeFile()方法上加入此功能，但timeout翻倍每次的时间，使重试更重。默认的５次重试次数，将造成client至少等待５分钟以上。如果NN无响应，则会等待时间更长。

##解决问题的办法
- 快速解决问题可以将此文件删除，但造成的后果就是jobtracker上的历史jar包，或HBase Region上的compaction产生的文件将会丢失。
- 长效解决此问题的办法是升级到CDH 5 Beta 2之后的版本

