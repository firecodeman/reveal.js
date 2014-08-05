##HUE(hue-3.5.0-cdh5.0.3)源代码分析

```
  /hue
     |- apps  所有的扩展应用
        |- about 关于HUE应用
        |- beeswax Hive查询工具应用
        |- filebrowser 文件浏览器查询工作应用
        |- hbase HBase查询工具应用
        |- help 帮助文档应用
        |- impala Impala查询工具应用
        |- jobbrowser JobTracker浏览器
        |- jobsub
        |- metastore Hive MetaStore查看器
        |- oozie OOZIE编辑器
        |- pig  Pig脚本编辑器
        |- proxy 代理工具
        |- rdbms 数据库查询工具
        |- search Solar查询工具
        |- spark Spark查询工具
        |- sqoop Sqoop管理工具
        |- useradmin 用户管理工具
        |- zookeeper Zookeeper查看工具
        |- Makefile
     |- cloudera  Cloudera提供的Patch
     |- desktop 
        |- conf 配置文件
           |- hue.ini  整个HUE所有app的应用
           |- log.conf Python logger配置
           |- log4j.properties  Java logger配置
        |- core 核心代码
           |- ext-py　Python依赖的包
           |- src 
              |- app_template [应用模板]
              |- app_template_proxy [代理类型的应用模板] 其中index.mako负责根据子系统地址渲染内容
              |- auth  登录验证功能模块
              |- lib  项目内抽离出的包
              |- locale 国际化配置
              |- log  日志输出组件,包含日志buffer
              |- management  管理工具
                 |- commands 系统命令用户创建一个新窗口应用或代理应用等
              |- migrations django db 合并工具
              |- templates 框架公共页面mako模板,包含404,500,login,common_header,comm_footer等模板
              |- api.py 从urls.py中映射的desktop/api/下的url请求实现
              |- app_manager.py 负责维护DESKTOP_LIBS、DESKTOP_APPS、DESKTOP_MODULES全局变量
              |- conf.py  HUE系统在hue.ini中的配置工具，包括key和说明
              |- context_processors.py 子系统上下文替换工具
              |- decorators.py 用户是否拥有某app的某action的修饰类
              |- kt_renewer.py keytab renewer
              |- manage_entry.py
              |- middleware.py
              |- models.py
              |- setting.py
              |- supervisor.py  整个HUE系统入口
              |- urls.py  整个系统url与view映射关系
              |- views.py 
           |- static  公共页面静态内容
           |- bable.cfg
           |- hueversion.py
           |- regenerate_thrift.sh
           |- setup.py
        |- 
     |- dist
     |- docs
     |- ext
     |- maven
     |- tools
     |- Makefile
     |- Makefile.sdk
     |- Makefile.tarball
     |- Makefile.vars
     |- Makefile.vars.priv
     |- NOTICE.txt
     |- README.rst
     |- VERSION
     |- data
```
