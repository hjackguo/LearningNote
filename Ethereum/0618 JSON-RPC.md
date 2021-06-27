- JSON-RPC API 就是一个接口，允许我们编写的程序使用以太坊客户端作为网关，访问以太坊网络和链上数据。
- RPC接口作为一个HTTP服务，**端口设定为8545**。出于安全原因，默认情况下，它仅限于接受来自localhost的连接。
- 要访问JSON-RPC API, 可以使用编程语言编写的专用库，例如JavaScript的web3.js.
- 或者可以手动构建HTTP请求并发送/接受JSON编码的请求，如：

![image-20210618212830016](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/image-20210618212830016.png?x-oss-process=style/compress)

```java
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0", "method":"web3_clientVersion", "params":[], "id":1}' http://localhost:8545

{"jsonrpc":"2.0","id":1,"result":"Geth/v1.9.8-stable-d62e9b28/darwin-amd64/go1.16.3"}
```

web3_clientVersion  在实际geth中，是web3.clientVersion.  json中用下划线代替' . '

