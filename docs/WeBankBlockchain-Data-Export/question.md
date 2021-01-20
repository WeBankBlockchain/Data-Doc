## 常见问题

### 为啥我的数据里自动生成的表里，只有block_detail_info、block_raw_data、block_task_pool、contract_info表有数据？

> A： block_task_pool、block_raw_data、block_detail_info表是链的基本数据，只要服务正常运行，这些表肯定会有数据。首先，请检查连接的区块链的地址、端口是否正确。其次，你需要检查合约的版本。如果你升级了合约，但链上执行的合约都是老版本的合约，这个时候就无法获得数据。最后，需要检查合约中是否定义了Event、显式定义了构造函数；如果没有定义，是不会有Event和构造函数的表的。

> B： Java文件中的binary数据必须保证和上链文件的binary一致，否则识别不出来

### 我在链上部署了多个项目的合约，其中的包名并不同，能在同一个工程里导出数据吗？

> A：可以。只需要手动将编译生成的合约代码的包名改为同一个，然后在配置文件中将monitor.contractPackName配置为该包名，并按照之前的方式配置、重启，即可导出所有合约的数据。


### 假如我的合约升级了怎么办，能否导出历史和更新后的合约数据？

> A：可以。但是会被作为两个数据库表来进行存储，因为合约的数据结构等可能会改变。
操作方法：你也猜到了，我们建议建立版本号，将升级的合约与旧版本的合约Java文件，使用不同的命名，保存到配置文件下面。

### 我已成功启动和部署服务，也看到Mysql里生成了各个函数的表，但是只有event表里有数据，函数表里没有？

> A：这个问题是因为发送交易的文件和数据导出里放入的Java文件不同造成的。例如，交易是通过nodejs或webase-front来发送的，但是，Java文件是通过控制台编译生成的。解决方案：将发送交易处的java文件复制到数据导出工程中。如果没有Java文件，例如nodejs环境，可将生成的binary和abi值，手工拷贝并替换Java文件里的BINARY和ABI字段的值，并重新使用monkey工程来重新生成bee工程即可。

### 是否支持合约函数和事件的重载？

> A：暂不支持，建议修改命名。

### 是否支持多群组的数据导出？

> A：支持
> 操作方法： [多群组数据导出](https://data-doc.readthedocs.io/zh_CN/latest/docs/WeBankBlockchain-Data-Export/install.html#id19)


### 脚本没权限，执行shell脚本报错误"permission denied"或格式错误？

```
赋权限：chmod + *.sh
转格式：dos2unix *.sh
```


