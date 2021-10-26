# TS-RPC

## 使用方法

### ts-rpc.json
```json
{
  // 客户端配置
  "client": {
    // 远程服务地址前缀
    "baseUrl": "127.0.0.1:3000"
  },
  // 服务端配置
  "server": {
    // RPCService 所在文件
    "scanDir": ["server/*.ts"],
    // 扫描信息输入地址
    "metaOutDir": "./"
  }
}
```

### 服务端
```ts
// 运行 server 服务之前执行`ts-rpc`命令
// scripts: yarn ts-rpc server -c ts-rpc.json && yarn dev

import { bindKoa, RPCService, RPCMethod } from 'ts-brpc/server'

bindKoa(User) // 或者 bindExpress

@RPCService()
class User {

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

// TODO: ctx 信息怎么获取，错误处理 ？
```

### 客户端
```ts
// 运行 client 服务之前执行`ts-rpc`命令
// scripts: yarn ts-rpc client -c ts-rpc.json && yarn dev

import { createRetmoteService } from 'ts-brpc/client'

const rs = createRetmoteService({
  baseUrl: "127.0.0.1:3000",
  // 可选，用于拦截处理请求
  agent: (req: Request): Response => {
    // fetch('/user/getInfoById/')
  }
})

const userInfo = await rs.User.getInfoById('<user id>')
console.log(userInfo) // { name: '22', age: 18, avatar: '<imgage url>' }
```

## 运行 demo
1. `git clone git@github.com:hughfenghen/ts-rpc.git`  
2. `cd demo && yarn`  
3. `yarn server` 新开 shell 窗口，`yarn client`  