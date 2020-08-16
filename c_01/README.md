
## 作业要求

题目描述：

本地下载 TiDB，TiKV，PD 源代码，改写源码并编译部署以下环境：
* 1 TiDB
* 1 PD
* 3 TiKV
改写后：使得 TiDB 启动事务时，能打印出一个 “hello transaction” 的 日志

输出：一篇文章介绍以上过程

截止时间：本周日 24:00:00（逾期不给分）

作业提交方式：提交至个人 github ，将链接发送给 talent-plan@tidb.io

## 解题

### 修改源码

通过阅读 [TiDB 源码](https://github.com/pingcap/tidb)，通过以下文件找到开启事务的代码：
```
make file 编译 server 找到入口：tidb-server/main.go
处理请求的逻辑：server/server.go:365 server.onConn
server/conn.go:663 clientConn.Run
server/conn.go:857 clientConn.dispatch
server/conn.go:1304 clientConn.handleQuery
server/driver_tidb.go:197 TiDBContext.ExecuteStmt
session/session.go:1118 session.ExecuteStmt
session/session.go:1176 runStmt
sessionctx/context.go:34 Context.NewTxn
session/session.go:1494 session.NewTxn
```

所以在 `session/session.go:1507` 加入一行 `logutil.Logger(ctx).Info("hello transaction")` 即可满足需求。

### 编译

* [TiDB](https://github.com/pingcap/tidb): `make`
* [TiKV](https://github.com/pingcap/tikv): `make`
* [PD](https://github.com/pingcap/pd): `make`

### 部署

* 将上述三个编译结果加入 `PATH` 里，运行 `tiup `
* 下载 `tiup`，[参考资料](https://docs.pingcap.com/zh/tidb/stable/quick-start-with-tidb#%E7%AC%AC%E4%BA%8C%E7%A7%8D%E4%BD%BF%E7%94%A8-tiup-cluster-%E5%9C%A8%E5%8D%95%E6%9C%BA%E4%B8%8A%E6%A8%A1%E6%8B%9F%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2%E6%AD%A5%E9%AA%A4)
* 启动：` tiup playground --db=1 --pd=1 --kv=3 --tiflash=0`

至此，已满足作业要求。恳请批评指正。
