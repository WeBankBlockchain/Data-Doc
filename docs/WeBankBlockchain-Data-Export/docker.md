
### Docker方式启动

#### 前置依赖

| 依赖软件 | 说明 |备注|
| --- | --- | --- |
| FISCO-BCOS | 2.0及以上版本 | |
| MySQL | >= mysql-community-server[5.7] | |
| ElasticSearch | >= elasticsearch [7.0] | 只有在需要ES存储时安装 |
| zookeeper | >= zookeeper[3.4] | 只有在进行集群部署的时候需要安装|
| Bash | 需支持Bash（理论上来说支持所有ksh、zsh等其他unix shell，但未测试）|


#### 获取启动脚本和配置文件

```
curl -#LO https://github.com/WeBankBlockchain/Data-Export/releases/download/1.7.2-beta/data-export-1.7.2-beta.tar.gz
```

解压文件包至当前目录
```
tar -zxvf data-export-1.7.2-beta.tar.gz && cd data-export-docker && chmod -x build_export.sh
```

data-export-docker目录如下：
```
├── data-export-docker
│   ├── config
│   │   ├── abi
│   │   ├── bin
│   │   ├── application.properties
│   ├── log
│   └── build_export.sh
```

```eval_rst
.. note::
    - **config为配置文件目录，使用channel方式连接区块链时，需将证书放至该目录。**
    - config包括了abi和bin两个文件夹，用于配置合约信息。
    - 运行生成的sql脚本data_export.sql和可视化脚本default_dashboard.json会保存在config目录下。
    - 运行日志保存在log目录下

```


#### 配置证书（channel方式启动）

选择channel方式连接链节点时，需配置证书。

将链节点SDK证书拷贝到 **./data-export/config**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./data-export/config/
```


#### 配置文件

配置文件application.properties位于config目录下，进入config

```
cd config
```

修改application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要配置的：

```
### 数据导出支持以下三种方式:
### 1, Channel
### 2, JsonRPC
### 3, Data-Stash
### 选择其中一种方式配置即可，默认Channel方式

# Channel方式启动，需配置证书
## GROUP_ID必须与FISCO-BCOS中配置的groupId一致, 多群组以,分隔，如1,2
system.groupId=1
# 节点的IP及通讯端口、组号。 
##IP为节点运行的IP，PORT为节点运行的channel_port，默认为20200
system.nodeStr=[IP]:[PORT]

### 数据库的信息，暂时只支持mysql； serverTimezone 用来设置时区
### 请确保在运行前创建对应的database，如果分库分表，则可配置多个数据源，如system.db1.dbUrl=\system.db1.user=\system.db0.password=
system.db0.dbUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db0.user=
system.db0.password=

```

数据导出除支持上述的Channel方式导出数据外，还支持[JSON-RPC方式](./expertconfig.md)和[数据仓库方式](./expertconfig.md)

其中多群组数据导出，参照[多群组数据导出](./expertconfig.md)

#### 配置合约

将合约对应的abi和binary文件分别放置到config/abi和config/bin中。

两个目录中包含了一个HelloWorld合约的abi和bin文件，使用时请按需删除。



#### 可视化安装配置

在application.properties中将grafana打开时，将在docker中自动部署grafana，通过[ip]:3000即可访问，配置如下:
```
system.grafanaEnable=true
```

#### 启动脚本

```
bash build_export.sh
```
上述脚本会自动安装docker，并拉取对应镜像，进行执行。如果docker安装失败，请手动安装后重新执行脚本。

控制台可看到提示启动结果：

```
......
docker.io/wangyue168git/dataexport:1.7.2
b7c087943edbe731304c76bcc44d705d20a8362fa8f2271d0d03ca6c75ee061c
data export run success
See the logging command: docker logs -f dataexport
.....

```

如果打开了grafana，则可看到执行提示如下：
```
docker.io/grafana/grafana:latest
c9cc7e8920c17d0f5421808b058a2f39e827234c0b781f393b8e60ef8d073d86
grafana run success
```


通过如下命令可查看运行日志：
```
docker logs -f dataexport
```

#### 修改配置

停止运行，命令如下
```
docker stop dataexport
```

修改application.properties配置文件后，重新启动，命令如下

```
docker restart dataexport
```

#### 可视化展示配置

参照[可视化展示配置](./view.md)

#### 更多配置

更多配置参见[服务配置说明](./expertconfig.md)

#### 常见问题

docker与数据库或链在一台机器上，docker无法访问宿主机。参考[常见问题](./question.html#docker-docker)

centos启动脚本报yum更新失败。参考[常见问题](./question.html#centosyum)

