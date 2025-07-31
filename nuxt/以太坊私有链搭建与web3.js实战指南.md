# 以太坊私有链搭建与web3.js实战指南

## 一、环境准备与Geth安装

### 1.1 安装以太坊客户端
使用以下命令在Ubuntu系统安装Geth：
```bash
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum
```

👉 [查看区块链开发最新工具包](https://bit.ly/okx_welcome)

### 1.2 Node环境配置
使用nvm管理Node版本：
```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
source ~/git/nvm/nvm.sh
nvm install 7
```

## 二、私有链启动方案

### 2.1 PM2进程管理方案
创建`geth.json`配置文件：
```json
{
  "name": "geth",
  "cwd": "/usr/bin/",
  "script": "geth",
  "args": "--rpcapi eth,web3 --rpc --dev --datadir /home/username/geth_private_data",
  "log_date_format": "YYYY-MM-DD HH:mm Z",
  "out_file": "/home/username/geth_private_data/log/geth_out.log",
  "error_file": "/home/username/geth_private_data/log/geth_err.log"
}
```

执行启动命令：
```bash
pm2 start geth.json
```

### 2.2 Shell脚本启动方案
创建启动脚本`geth_private.sh`：
```bash
#!/bin/bash
geth=${GETH:-geth}
$geth --datadir data --networkid 31415926 \
--rpc --rpcapi "admin,debug,eth,miner,net,personal,shh,txpool,web3" \
--rpcaddr "0.0.0.0" --rpccorsdomain "*" --nodiscover console
```

设置执行权限：
```bash
chmod +x geth_private.sh
```

启动方式选择：
```bash
nohup ./geth_private.sh 0<&- &>/dev/null & # 守护进程模式
nohup ./geth_private.sh 0<&- &> /tmp/geth.log & # 日志记录模式
./geth_private.sh # 交互控制台模式
```

## 三、智能合约部署流程

### 3.1 开发环境搭建
安装依赖包：
```bash
npm install web3
sudo apt-get install solc
```

### 3.2 部署前准备
确保满足以下条件：
| 项目        | 要求说明                |
|-------------|-------------------------|
| 账户状态    | 至少一个解锁账户        |
| 账户余额    | 0.1 ETH以上              |
| RPC配置     | 包含web3,eth等模块       |
| 网络ID      | 自定义私有网络ID(如31415926) |

### 3.3 控制台操作示例
连接节点：
```bash
geth attach ipc:/some/custom/path
geth attach http://191.168.1.1:8545
```

启动挖矿：
```javascript
miner.start(1)
```

## 四、常见问题解答

### Q1：如何确认Geth是否成功启动？
检查日志文件`/tmp/geth.log`是否有类似`Mined block (#72 / 517dcfd1)`的输出，同时使用`eth.blockNumber`查看区块高度变化。

👉 [获取区块链开发最新资源](https://bit.ly/okx_welcome)

### Q2：web3.js连接失败怎么办？
确保启动参数包含`--rpcapi "web3,eth"`，检查防火墙设置是否开放8545端口，使用`curl http://localhost:8545`进行连通性测试。

### Q3：私有链部署需要多少内存？
建议最低配置：4GB RAM + 20GB SSD，使用`--cache=1024`参数优化内存使用。

## 五、进阶开发技巧

### 5.1 智能合约调试
在部署时添加日志记录：
```javascript
var contract = web3.eth.contract(abi).at(address);
contract.Event().watch(function(error, result){
    console.log("事件触发：", result);
});
```

### 5.2 性能优化方案
| 优化方向     | 实施方案                     |
|--------------|------------------------------|
| 同步模式     | 使用`--fast`快速同步         |
| 矿工设置     | `miner.setGasPrice(20000000000)` |
| 节点监控     | 部署Prometheus监控系统        |
| 备份策略     | 定期备份`datadir`目录         |

👉 [探索区块链开发新趋势](https://bit.ly/okx_welcome)