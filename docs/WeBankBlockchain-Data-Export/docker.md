
### Docker快速部署

#### 前置依赖

| 依赖软件 | 说明 |备注|
| --- | --- | --- |
| FISCO-BCOS | 2.0及以上版本 | |
| Bash | 需支持Bash（理论上来说支持所有ksh、zsh等其他unix shell，但未测试）|


#### 获取启动脚本和配置文件

```
curl -#LO https://github.com/WeBankBlockchain/Data-Export/releases/download/V1.7.2/data-export-1.7.2.tar.gz
```

```eval_rst
.. note::
    - 如果因为网络问题导致长时间无法下载，请尝试：
    - curl -#LO https://gitee.com/WeBankBlockchain/Data-Export/attach_files/679385/download/data-export-1.7.2.tar.gz
```

解压文件包至当前目录
```
tar -zxvf data-export-1.7.2.tar.gz && cd data-export-docker && chmod -x build_export.sh
```

data-export-docker目录如下：
```
├── data-export-docker
│   ├── config
│   │   ├── abi
│   │   ├── bin
│   │   ├── application.properties
│   ├── log
│   ├── data
│   └── build_export.sh
```

```eval_rst
.. note::
    - **config为配置文件目录，使用channel方式连接区块链时，需将证书放至该目录。**
    - config包括了abi和bin两个文件夹，用于配置合约信息。
    - 运行生成的sql脚本data_export.sql和可视化脚本default_dashboard.json会保存在config目录下。
    - 运行日志保存在log目录下。
    - data目录为docker安装mysql和es后的数据挂载目录。
```


#### 配置证书（channel方式启动）

选择channel方式连接链节点时，需配置证书。

将链节点SDK证书拷贝到 **./data-export-docker/config**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./config/
```


#### 配置文件

配置文件application.properties位于config目录下

修改application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要配置的：

```
### 数据导出支持以下三种方式:
### 1, Channel
### 2, JsonRPC
### 3, Data-Stash
### 选择其中一种方式配置即可，默认Channel方式

# Channel方式启动，与java sdk一致，需配置证书
## GROUP_ID必须与FISCO-BCOS中配置的groupId一致, 多群组以,分隔，如1,2
system.groupId=1
# 节点的IP及通讯端口、组号。 
##IP为节点运行的IP，PORT为节点运行的channel_port，默认为20200
system.nodeStr=127.0.0.1:20200

```

数据导出除支持上述的Channel方式导出数据外，还支持[JSON-RPC方式](./expertconfig.html#json-rpc)和[数据仓库方式](./expertconfig.html#id3)

其中多群组数据导出，参照[多群组数据导出](./expertconfig.html#id11)

#### 配置合约

将合约对应的abi和binary文件分别放置到config/abi和config/bin中。

两个目录中包含了一个HelloWorld合约的abi和bin文件，使用时请按需删除。

合约bin和binary的获取参考[编译智能合约](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/tutorial/sdk_application.html#id6)


#### 启动脚本

```
bash build_export.sh -m
```
上述脚本会自动安装docker，并拉取对应镜像，进行执行。如果docker安装失败，请手动安装后重新执行脚本。

**脚本参数说明**

| 参数 | 说明 |
| --- | --- |
| -m | 自动安装mysql，数据存储在/data/mysql下 |
| -e | 自动安装elasticsearch，数据存储在/data/elasticsearch下 |
| -g | 自动安装grafana |

```eval_rst
.. note::
    - 加后缀 **-m** 启动脚本，会通过docker自动安装mysql, 并创建一个名为 **data_export** 的数据库, **application.properties** 中默认配置了该mysql的信息，无需另配置mysql连接信息。
    - 加后缀 **-e** 启动脚本，会通过docker自动安装elasticsearch，并自动修改配置文件中es相关配置。
    - 加后缀 **-g** 启动脚本， 会通过docker自动安装grafana，并自动修改配置文件中可视化相关配置，生成可视化脚本。
    - 不加后缀执行如: **bash build_export.sh**，这时需自行安装相关组件，并配置连接信息。 
    - ElasticSearch用于商用场景时需自行去ElasticSearch官网下载或采购。该行为与微众区块链无关。
```

这里采取 **bash build_export.sh -m** 方式启动，docker安装的mysql的访问信息如下： 
```
用户名：root
密码：123456
访问地址端口：127.0.0.1:3307
```

控制台可看到提示启动结果：

```
......
docker.io/fiscoorg/dataexport:1.7.2
b7c087943edbe731304c76bcc44d705d20a8362fa8f2271d0d03ca6c75ee061c
data export run success
See the logging command: docker logs -f dataexport
.....

```

如果 **bash build_export.sh -meg** 执行，则可看到提示如下：
```
7.8.0: Pulling from library/elasticsearch
... ...
docker run elasticsearch success...
latest: Pulling from grafana/grafana
... ...
grafana run success
```


通过如下命令可查看运行日志：
```
docker logs -f dataexport
```

访问docker数据库命令如下：
```
docker exec -it mysql mysql -uroot -p123456
```

#### 关闭重启

停止运行，命令如下
```
docker stop dataexport
```

可修改application.properties配置文件后，重新启动，命令如下

```
docker restart dataexport
```

停止和删除所有容器命令如下：
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

#### 可视化展示配置

参照[可视化展示配置](./view.html#id4)

#### 更多配置

除上述功能外，还支持以下功能：

- [数据表个性化](./expertconfig.html#id4)

- [ES存储](./expertconfig.html#es)

- [集群多活](./expertconfig.html#id7)

- [分库分表](./expertconfig.html#id12)

- [工程配置](./expertconfig.html#id6)

- [合约字段个性化配置](./expertconfig.html#id9)

更多配置参见[服务配置说明](./expertconfig.md)

#### 常见问题

docker与数据库或链在一台机器上，docker无法访问宿主机。参考[常见问题](./question.html#docker-docker)

centos启动脚本报yum更新失败。参考[常见问题](./question.html#centosyum)

