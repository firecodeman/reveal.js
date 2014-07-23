##Impala 1.3.1源代码分析
```
impala-1.3.1
    |- be (back end)后端服务代码
       |- generate-sources  产生的代码
          |- gen-cpp 生产的cpp代码
          |- impala-ir 产生的 llvm-ir代码
          |- opcode  ?
       |- src 
          |- codegen impala ir代码产生工具
          |- common
          |- exec  后台执行引擎
          |- exprs 表达式引擎
          |- runtime 运行时环境
          |- service 服务
          |- statestore 状态存储服务
          |- testutil 测试工具
          |- transport 
          |- util 工具箱 (性能测试、验证、bit操作、压缩解压等)
    |- bin 所有的shell脚本程序
    |- cloudera build的版本配置目录
    |- cmake_modules cmake公共模块
    |- comm 公共组件目录
         |- function-registry
         |- thrift thrift IDL配置目录
            |- Data.thrift 定义了数据返回的行与列的相应结构TRowBatch、TColumnValue、TResultRow
            |- DataSinks.thrift 定义了数据输出的相关结构TDataSinkType、TTableSinkType、TDataStreamSink、THdfsTableSink、TTableSink、TDataSink
            |- Descriptors.thrift 定义了数据表、格式、压缩等结构TSlotDescriptor、TTableType、THdfsFileFormat、THdfsCompression、COMPRESSION_MAP、THdfsPartition、THdfsTable、THBaseTable、TTableDescriptor、TTupleDescriptor、TDescriptorTable
            |- Exprs.thrift 定义了语法树相关的结构TExprNodeType、TAggregationOp、TAggregateExpr、TBoolLiteral、TCaseExpr、TDateLiteral、TFloatLiteral、TIntLiteral、TInPredicate、TIsNullPredicate、TLikePredicate、TLiteralPredicate、TSlotRef、TStringLiteral、TExprNode、TExpr
            |- Frontend.thrift 定义了一些DDL的的结构TGetTablesParams、TGetTablesResult、TGetDbsParams、TGetDbsResult、TColumnDesc、TDescribeTableParams、TDescribeTableResult、TCreateDbParams、TFileFormat、TTableName、TTableRowFormat、TAlterTableType、TPartitionKeyValue、TAlterTableRenameParams、TAlterTableAddReplaceColsParams、TAlterTableAddPartitionParams、TAlterTableDropColParams、TAlterTableDropPartitionParams、TAlterTableChangeColParams、TAlterTableSetFileFormatParams、TAlterTableSetLocationParams、TAlterTableParams、TCreateTableLikeParams、TCreateTableParams、TDropDbParams、TDropTableParams、TSessionState、TClientRequest、TShowDbsParams、TUseDbParams、TResultSetMetadata、TCatalogUpdate、TFinalizeParams、TQueryExecRequest、TDdlType、TDdlExecRequest、TMetadataOpcode、TMetadataOpRequest、TMetadataOpResponse、TExecRequest
            |- ImpalaInternalService.thrift 定义了内部调用RPC和相关结构,结构包括TQueryOptions、TScanRangeParams、TPlanFragmentDestination、TPlanFragmentExecParams、TQueryGlobals、ImpalaInternalServiceVersion、TExecPlanFragmentParams、TExecPlanFragmentResult、TInsertExecStatus、TReportExecStatusParams、TTransmitDataParams、TTransmitDataResult和服务ImpalaInternalService 中的ExecPlanFragment、ReportExecStatus、CancelPlanFragment、TransmitData
            |- ImpalaPlanService.thrift 定义查询计划的相关服务ImpalaPlanService CreateExecRequest、RefreshMetadata、GetExplainString、UpdateMetastore、ShutdownServer
            |- ImpalaService.thrift ImpalaService相关结构TImpalaQueryOptions、DEFAULT_QUERY_OPTIONS、TInsertResult和服务ImpalaService(继承beeswax.BeeswaxService) 中的Cancel、ResetCatalog、GetRuntimeProfile、CloseInsert、PingImpalaService　和ImpalaHiveServer2Service服务（继承自cli_service.TCLIService) 中的ResetCatalog
            |- NetworkTest.thrift 测试网络通连
            |- Partitions.thrift 定义了数据分片相关的结构TPartitionType、TDataPartition
            |- PlanNodes.thrift 查询计划树的相关结构TPlanNodeType、TExecNodePhase、TDebugAction、THdfsFileSplit、THBaseKeyRange、THBaseKeyRange、TScanRange、THBaseFilter、THBaseScanNode、TEqJoinCondition、TJoinOp、THashJoinNode、TAggregationNode、TSortNode、TMergeNode、TExchangeNode、TPlanNode、TPlan
            |- Planner.thrift 定义了查询计划相关的结构TPlanFragment、TScanRangeLocation、TScanRangeLocations
            |- RuntimeProfile.thrift  定义了运行时统计分析counter的相关结构TCounterType、TCounter、TRuntimeProfileNode、TRuntimeProfileTree
            |- StateStoreService.thrift 定义了StateStore服务相关结构和服务RPC，结构有StateStoreServiceVersion、TTopicItem、TTopicUpdate、TTopicDelta、TTopicRegistration、TRegisterSubscriberRequest、TRegisterSubscriberResponse　和服务StateStoreSubscriber中的TUpdateStateResponse UpdateState(1: TUpdateStateRequest params)
            |- Status.thrift 定义了TStatusCode错误码和TStatus(包含错误码和错误消息)
            |- Type.thrift 定义了类型TTimestamp、TPlanNodeId、TTupleId、TSlotId结构TPrimitiveType、TStmtType,TExplainLevel,TNetworkAddress,TUniqueId(128位)
            |- beeswax.thrift hue beeswax接口
            |- ci_service.thrift 从hive上复制过来兼容hive接口的对外接口
            |- partuet.thrift parquet文件thrift接口
```
