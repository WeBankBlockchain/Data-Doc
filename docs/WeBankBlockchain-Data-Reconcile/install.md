# 快速开始

## 1. 环境准备

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件   | 说明                                                         | 备注 |
| ---------- | ------------------------------------------------------------ | ---- |
| FISCO-BCOS | \>= 2.0， 1.x版本请参考V0.5版本 dev分支                      |      |
| Bash       | 需支持Bash（理论上来说支持所有ksh、zsh等其他unix shell，但未测试） |      |
| Java       | \>= JDK[1.8]                                                 |      |
| Git        | 下载的安装包使用Git                                          |      |
| MySQL      | \>= mysql-community-server[5.7]                              |      |
| FTP        | 需要时安装                                                     |      |

## 2. 项目准备

### 2.1 下载代码：

```
git clone https://github.com/WeBankBlockchain/WeBankBlockchain-Data-Reconcile.git
cd WeBankBlockchain-Data-Reconcile
git checkout dev
```
### 2.2 项目打包

```
./gradlew build
```

项目jar包在dist目录下，位置如下：

![](../../images/WeBankBlockchain-Data-Reconcile/jarpath.png)


### 2.3 项目配置

配置文件位于dist/config目录下，位置如下：

![](../../images/WeBankBlockchain-Data-Reconcile/configfile.png)

#### 2.3.1 数据库配置

关于数据库的连接配置在datasource.properties下，将链上导出数据所在的数据库参数配置到这里，链上数据需要借助数据导出组件WeBankBlockchain-Data-Export对链上数据进行导出，数据导出组件使用：[WeBankBlockchain-Data-Export](https://data-doc.readthedocs.io/zh_CN/dev/docs/WeBankBlockchain-Data-Reconcile/install.html)

必配项如下：

```
## data export DB config
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8
spring.datasource.username=root
spring.datasource.password=123456

##生产环境下需关闭以下自动建表配置，手动建表
spring.jpa.hibernate.ddl-auto=update
```

将下述sql，在以上配置的数据库中执行，创建对账任务表，该表记录了对账任务的生命周期和流转状态
```
CREATE TABLE `task_info` (
  `pk_id` bigint(20) NOT NULL AUTO_INCREMENT,
  `node_id` varchar(255) NOT NULL DEFAULT '""',
  `task_id` varchar(255) NOT NULL DEFAULT '""',
  `status` tinyint(4) NOT NULL,
  `triggered` tinyint(4) NOT NULL,
  `business_file_name` varchar(255) DEFAULT NULL,
  `business_file_path` varchar(255) DEFAULT NULL,
  `bc_file_path` varchar(255) DEFAULT NULL,
  `result_file_path` varchar(255) DEFAULT NULL,
  `last_execute_starttime` timestamp NULL DEFAULT NULL,
  `last_execute_endtime` timestamp NULL DEFAULT NULL,
  `data_range_begintime` timestamp NULL DEFAULT NULL,
  `data_range_endtime` timestamp NULL DEFAULT NULL,
  `retry_count` int(11) DEFAULT 0,
  `createtime` timestamp NOT NULL DEFAULT '2019-05-21 00:00:00',
  `updatetime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`pk_id`),
  UNIQUE KEY `task_id` (`task_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

#### 2.3.2 对账配置

对账的配置reconcile.properties

配置项如下，其中必配项做了标记(Must)

```
#定时对账开关
reconcile.task.timer.enable=true
#定时对账数据时间范围（单位：天）
reconcile.task.time.range.days=0
#定时对账时间规则
#Online build/parse: http://cron.qqe2.com/
#Commonly used: second, minute, hour, day, month, year
reconcile.task.time.rule=0 0 1 * * ?

#对账任务超时时间（ms）
reconcile.task.timeout=600000
#对账任务失败重试间隔时间（ms）
reconcile.task.retry.interval.time=60000
#对账失败任务重试次数
reconcile.task.retry.count=2
#对账状态补偿时间规则（执行中）
reconcile.executing.compensate.rule=0 0/1 * * * ?
#对账状态补偿时间规则（失败）
reconcile.failed.compensate.rule=0 0/1 * * * ?

#业务数据提供方机构名 (Must)
reconcile.business.name=webank

##数据导出sql配置，包含查询sql和时间参数字段配置 (Must)
#data query sql，format：select * from table where ... and 1=1（There is no need to add a data time range and paging criteria）
reconcile.bc.reconcileQuerySql=select * from asset_transfer_event_event where 1=1
#The time field name of the data export table， If multiple table operations are involved,
#please indicate which table it belongs to, that is, add the field prefix, such as table.timeField (Must)
reconcile.bc.QueryTimeField=block_timestamp

##默认对账模式配置:
#通用对账模式开关
reconcile.general.enabled=true
#业务数据文件格式, json or txt
reconcile.file.type=txt
#业务数据唯一键 (Must)
reconcile.field.business.uniqueColumn=orderId
#BC数据唯一键，与业务唯一键对应 (Must)
reconcile.field.bc.uniqueColumn=pk_id
#两方数据字段匹配规则，格式如下 (Must)
#reconcile.fieldMapping.busId=bcId
#reconcile.fieldMapping.busName=bcName
#reconcile.fieldMapping.busAccount=bcAccount
reconcile.fieldMapping._from_account=fromAccount
reconcile.fieldMapping._to_account=toAccount
reconcile.fieldMapping._amount=amount
```

开启默认对账后，对账字段映射规则字段为必配，业务对账文件中字段名和数据导出的字段名要对应。

#### 2.3.3 FTP配置

ftp配置在ftp.properties中，基本配置项如下：

```
ftp.enabled=true
ftp.host=127.0.0.1
ftp.port=21
ftp.userName=ftptest
ftp.passWord=123456
ftp.workDir=/home/upload
```

本地测试使用ftp，可在本地安装FTP环境，推荐QuickFTP Server，方便快捷开启FTP

远程可自行搭建，推荐vsftp，参考链接：[ftp安装](https://data-doc.readthedocs.io/zh_CN/latest/docs/WeBankBlockchain-Data-Reconcile/appendix.html#ftp)

#### 2.3.4 对账数据准备

##### 2.3.4.1 业务方数据准备

需要预先将业务数据文件放到ftp配置目录下

**txt文件格式要求如下：**

```
pk_id#_#block_height#_#certainty#_#
5330#_#12329#_#1#_#
5329#_#12328#_#1#_#
328#_#12327#_#1#_#
```

首行不可为空行，首行为对账数据字段，要与对账配置中字段对应，分隔符#_#。

文件命名规则为：业务数据提供方机构名_对账数据起始日期，机构名为对账配置中的机构名。

对账数据的导出提供了统一的工具方法，在utils/FileUtills工具类中，如下：

```
//数据写入新文件中
public static <T> File exportDataByTxtFormat(List<T> dataList, String filePath)

//append为true时，数据追加到文件末尾
public static <T> File exportDataByTxtFormat(List<T> dataList, String filePath,
boolean append) 
```

对应调用方式如下，定义数据对象，其属性对象应实现toString()方法

```
 	@Test
    public void exportDataUtilTest() throws Exception {
        List<ExportData> dataList = new ArrayList<>();
        ExportData data1 = new ExportData(1, "wew", 2);
        ExportData data2 = new ExportData(2, "ds", 4);
        dataList.add(data1);
        dataList.add(data2);
        FileUtils.exportDataByTxtFormat(dataList,"out/export.txt");
    }
	//例子
    static class ExportData{
        long amount;
        String name;
        int age;
        public ExportData(long amount, String name, int age) {
            this.amount = amount;
            this.name = name;
            this.age = age;
        }
    }
```



**json格式文件如下：**

```
[{"orderId":"2","amount":"10","fromAccount":"sjj","toAccount":"wy"},{"orderId":"3","amount":"10","fromAccount":"sjj","toAccount":"wy"}]
```

以上为业务方数据准备。



##### 2.3.4.2 链上数据导出

链上数据需要借助数据导出组件WeBankBlockchain-Data-Export对链上数据进行导出，数据导出组件使用：[WeBankBlockchain-Data-Export](https://data-doc.readthedocs.io/zh_CN/dev/docs/WeBankBlockchain-Data-Reconcile/install.html)



## 3.  项目启动



### 项目启动

执行dist/目录下的start脚本
```
cd dist && bash start.sh
```

 启动成功日志如下：

![](../../images/WeBankBlockchain-Data-Reconcile/runsuccess.png)



对账执行结果文件保存在dist/out/result/下，并远程推送到FTP。

执行日志如下：

![](../../images/WeBankBlockchain-Data-Reconcile/log.png)


执行结果本地保存在/dist/out/result目录中：

![](../../images/WeBankBlockchain-Data-Reconcile/resultpath.png)

执行日志保存在logs目录下



## 4. 项目扩展

支持业务根据自身需求对组件进行扩展，组件核心流程位于handler目录下，如下：

![](../../images/WeBankBlockchain-Data-Reconcile/processpath.png)

对账流程默认分为四步：

1.任务管理，为执行责任链的首端，负责对账任务调度管理，任务模块由组件负责管理。

2.对账文件获取，分为业务对账文件和链上数据文件获取，提供两个扩展接口，如下，并提供默认实现，使用者可根据自身需要对接口进行实现

```
//链上数据导出，默认提供txt，json实现
public interface BCDataExportService{
    File exportData(String filePath, Date dataRangeBeginTime, Date dataRangeEndTime);
}
//业务对账文件获取,默认提供FTP文件获取实现
public interface FileTransferService {
    String sendFile(File file) throws Exception;
    File obtainFile(String fileName, String localFilePath) throws IOException;
}
```

3.对账执行，分为文件解析和数据对账处理，提供两个扩展接口，如下，并提供默认实现

```
//文件解析，默认提供txt，json实现
public interface FileParser {
    void parseAndTransfer(File businessReconFile, File bcReconFile, ReconcileTransfer transfer) throws ReconcileException;
}
//对账处理，提供通用对账逻辑实现
public interface ReconcileExecutor{
    ReconcileResult execute(ReconcileExecuteData executeData);
}
```

4.结果处理，包括结果导出和推送，提供扩展接口如下，并提供默认实现

```
//结果导出文件，默认提供txt，json实现
public interface ResultDataExportService {
    File exportResultData(String filePath, List<Future<ReconcileResult>> reconcileResults);
}
```

以上是对账核心流程。

另外，也可对核心流程功能进行增删扩展，提供流程处理扩展接口如下：

```
public interface Handler {
    void invoke(ReconcileContext context, InvocationHandler handler) throws Exception;
}
```

流程管理在ReconcileHandlerFactory类中，可在该类中对流程步骤进行扩展管理。