# ts-brpc

`ts-brpc`尝试将 ts+node 场景下 web 服务的 restful 替换为 RPC 风格，专注逻辑隐藏 http 请求细节。  
`ts-brpc`可以扫描 ts 代码中的类型信息，免去 API 文档维护成本，代码即 API 文档；并且可在编码时提供类型校验。  

## 使用方法

### 服务端
```ts
// 运行 server 服务之前执行`ts-rpc`命令
// scripts: yarn ts-brpc server -c ts-rpc.json && yarn dev

import { bindKoa, RPCService, RPCMethod } from 'ts-brpc'

bindKoa(User) // 或者 bindExpress

@RPCService()
class User {

  // bindKoa 自动注入 ctx
  ctx

  // midway 通过框架注解手动注入
  // @Inject()
  // resp: IRespHandle

  @RPCMethod()
  getInfoById(id: string): { name: string, age: number, avatar: string } {
    // 从上游获取数据
    return {
      name: '22',
      age: 18,
      avatar: '<image url>'
    }
  }
}
```

### 客户端
```ts
// 运行 client 服务之前执行`ts-rpc`命令
// scripts: yarn ts-brpc client -c ts-rpc.json && yarn dev

import { createRetmoteService } from 'ts-brpc'
import RPCDemo from './rpc-definition' // rpc-definition 为 ts-brpc.json client 对应的 genRPCDefintionTarget

const rs = createRetmoteService<RPCDemo>({
  baseUrl: "127.0.0.1:3000",
  // 可选，用于拦截处理请求
  agent: (req: Request): Response => {
    // fetch('/user/getInfoById/')
  }
})

const userInfo = await rs.User.getInfoById('<user id>')
console.log(userInfo) // { name: '22', age: 18, avatar: '<imgage url>' }
```

### ts-rpc.json 示例
```json
{
  "appId": "RPCDemo",
  "client": {
    "apps": {
      "a": "localhost:3000",
      "b": "",
      "aDev": ""
    },
    "genRPCDefintionTarget": "./"
  },
  "server": {
    "scanDir": ["server/*.ts"],
    "metaOutDir": "./"
  }
}
```

## 运行 demo
1. `git clone git@github.com:hughfenghen/ts-rpc.git`  
2. `cd demo && yarn`  
3. `yarn server` 新开 shell 窗口，`yarn client`  