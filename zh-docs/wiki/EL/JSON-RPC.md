# JSON-RPC

JSON-RPC 规范是基于 [OpenRPC](https://open-rpc.org/getting-started) 的 JSON 编码的远程过程调用协议。它允许调用远程服务器上的函数，并返回结果。
它是执行 API 规范的一部分，该规范提供了一组与以太坊区块链交互的方法。
更广为人知的是用户如何使用客户端与网络交互，甚至共识层（CL）和执行层（EL）如何通过引擎 API（Engine API） 进行交互。
本节介绍 JSON-RPC 方法。
## API 规范

JSON-RPC 方法按照方法前缀指定的命名空间进行分组。尽管它们的目的不同，但都共享一个公共的结构，并且在所有实现中必须具有相同的行为：

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "<prefix_methodName>",
  "params": [...]
}
```

解释:
- `id`：请求的唯一标识符。
- `jsonrpc`: JSON-RPC 协议的版本。
- `method`：被调用的方法。
- `params`：方法的参数。如果方法不需要任何参数，则可以是空数组。如果不提供其他值，则可能有默认值。
  
### 命名空间

每个方法都由命名空间前缀和方法名组成，方法名之间用下划线分隔。

以太坊客户端必须实现规范要求的最基本的 RPC 方法集合来与网络交互。除此之外，还有一些特定于客户端的方法来控制节点或实现额外的独特功能。总是参考列出可用方法和命名空间的客户端文档，例如注意 [Geth](https://geth.ethereum.org/docs/interacting-with-geth/rpc) 和 [Reth](https://paradigmxyz.github.io/reth/jsonrpc/intro.html) 文档中的不同命名空间。

下面是一些最常见的命名空间的例子。

| **命名空间** | **描述**                                                                                      | **敏感性** |
| ------------- | ---------------------------------------------------------------------------------------------------- | ------------- |
| eth           | eth API 允许您与以太坊进行交互。                                                    | Maybe         |
| web3          | web3 API 为 web3 客户端提供实用功能。                                        | No            |
| net           | net API 提供对节点网络信息的访问。                                      | No            |
| txpool        | txpool API 允许您检查交易池。                                           | No            |
| debug         | debug API 提供了几个方法来检查以太坊状态，包括 Geth 风格（Geth-style）的跟踪。| No            |
| trace         | trace API 提供了几个方法来检查以太坊状态，包括奇偶校验风格（Parity-style）的跟踪。 | No            |
| admin         | admin API 允许你配置你的节点。                                                     | Yes           |
| rpc           | rpc API 提供 rpc 服务器及其模块的信息                                | No            |

敏感意味着它们可能被用来建立节点，例如 **admin** ，或访问存储在节点中的帐户数据，就像 **eth** 。

现在，让我们看看一些方法，以了解它们是如何构建的以及它们的作用：

#### Eth

Eth 可能是最常用的命名空间，提供对以太坊网络的基本访问，例如，钱包读取余额并创建交易是必要的。
这里只提供了方法的简要列表，但完整列表可以在 [Ethereum JSON-RPC 规范](https://ethereum.github.io/execution-apis/api-documentation/)中找到。

| **方法**                           |           **参数**            | **描述**                                                                                                                             |
| ------------------------------------ |:-------------------------------:| ------------------------------------------------------------------------------------------------------------------------------------------- |
| eth_blockNumber                      |       没有必填参数       | 返回最近的块的编号                                                                                                 |
| eth_call                             |       交易对象        | 立即执行一个新的消息调用，而不在区块链上创建交易                                                   |
| eth_chainId                          |       没有必填参数       | 返回当前链                                                                                                                 |
| eth_estimateGas                      |       交易对象        | 发起一次调用或交易，该操作不会被添加到区块链上，并返回消耗的 Gas，可用于估算实际消耗的 Gas。|
| eth_gasPrice                         |       没有必填参数       | 返回当前每个 Gas 的价格（以 wei 为单位）                                                 |
| eth_getBalance                       |      地址，区块编号      | 返回给定地址的账户余额                                                   |
| eth_getBlockByHash                   |      区块哈希，完整交易      | 通过区块哈希返回区块信息                                                                                              |
| eth_getBlockByNumber                 |     区块编号，完整交易      | 通过区块编号返回区块信息                                                         |
| eth_getBlockTransactionCountByHash   |           区块哈希            | 返回与给定区块哈希匹配的区块中的交易数量                                                  |
| eth_getBlockTransactionCountByNumber |         区块编号          | 返回一个区块中与给定区块编号匹配的交易数量                                               |
| eth_getCode                          |     地址，区块编号     |           返回区块链中给定地址的代码                                       |
| eth_getLogs                          |            过滤器对象        |          返回一个匹配给定过滤器对象的所有日志的数组                    |
| eth_getStorageAt                     | 地址，位置，区块编号 |         返回给定地址的存储位置的值                                                      |

#### Debug

*debug* 命名空间提供了检查以太坊状态的方法。它是对原始数据的直接访问，对于某些用例（如区块浏览器或研究目的）可能是必要的。某些方法可能需要在节点上进行大量计算，且对于非存档节点的历史状态请求通常不可行。因此，公共 RPC 提供者通常会限制该命名空间，或者仅允许使用安全的方法。
以下是 debug 方法的一些基本示例：

| **方法**               |      **参数**       | **描述**                                                 |
|--------------------------|:---------------------:|-----------------------------------------------------------------|
| debug_getBadBlocks       |  没有必填参数  | 返回客户端最近看到的坏块数组 |
| debug_getRawBlock        |     block_number      | 返回一个 RLP 编码的区块                                    |
| debug_getRawHeader       |     block_number      | 返回一个 RLP 编码的区块头                   |
| debug_getRawReceipts     |     block_number      |    返回一个 EIP-2718 二进制编码的收据数组       |
| debug_getRawTransactions |        tx_hash        |    返回一个 EIP-2718 二进制编码的交易数组    |

#### Engine

[Engine API](https://hackmd.io/@danielrachi/engine_api) 与前述方法不同，客户端在一个独立且经过身份验证的端点上提供引擎 API(Engine API)，而不是常规的 HTTP JSON RPC，因为它并非面向用户的 API。该 API 旨在用于共识客户端和执行客户端之间的连接，基本上是一个内部节点通信过程。
跨客户端通信用于交换有关共识、分叉选择、区块验证等的信息：


| **方法**                               |               **参数**               | **描述**                                                           |
|------------------------------------------|:--------------------------------------:|---------------------------------------------------------------------------|
| engine_exchangeTransitionConfigurationV1 |        共识客户端配置（Consensus client config）        |         更换客户端配置                                   |
| engine_forkchoiceUpdatedV1*              |  forkchoice_state,载荷属性（payload attributes）   |        更新分叉选择状态                                      |
| engine_getPayloadBodiesByHashV1*         |           block_hash (array)           |    给定区块哈希，返回相应执行载荷的主体  |
| engine_getPayloadV1*                     |  forkchoice_state, 载荷属性（payload attributes）  |    从载荷构建过程获取执行载荷            |
| debug_newPayloadV1*                      |                tx_hash                 |              返回执行载荷验证                        |

带有星号（*）的方法有多个版本。[Ethereum JSON-RPC specification](https://ethereum.github.io/execution-apis/api-documentation/) 提供了详细的描述。

## 编码


JSON-RPC 方法的参数编码有一个约定，就是十六进制编码。
*数量以使用 “0x” 前缀的十六进制值表示。
  *例如，数字65表示为 “0x41”。
  *数字0表示为 “0x0”。
  *一些无效的用法是 “0x” 和 “ff” 。因为第一种情况下没有数字，第二种情况下没有 “0x” 前缀。
*未格式化的数据，如哈希、账户地址或字节数组，也使用 “0x” 前缀进行十六进制编码。
  *例如：0x400（十进制的 1014）
  *无效的大小写是 0x400，因为不允许前导零

  //译者注：在某些情况下，特别是当参数需要符合特定大小或格式时，JSON-RPC 可能会将数字“填充”到特定的字节长度。例如，可能会将 1014 填充为 0x400（十六进制），以符合特定的格式要求。

## 与传输方式无关

值得一提的是，JSON-RPC 是与传输方式无关的，这意味着它可以通过任何传输协议使用，例如 HTTP、WebSockets（WSS）甚至进程间通信（IPC）。

它们的区别可以总结如下：

* **HTTP** 传输提供了单向的请求-响应模型，连接会在响应发送后关闭。
* **WSS** 是一种双向协议，这意味着连接会一直保持开启，直到节点或用户显式关闭它。它支持基于订阅的通信模型，例如事件驱动的交互。
* **IPC** 传输协议用于同一台机器上运行的进程之间的通信。它比 HTTP 和 WSS 更快，但不适合远程通信，例如可以通过本地 JS 控制台使用。
## 工具
有几种方式可以使用 JSON-RPC 方法，其中之一是使用 curl 命令。例如，要获取最新的区块号，可以使用以下命令：

```bash
curl <node-endpoint> \
-X POST \
-H "Content-Type: application/json" \
-d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

请注意，*params* 字段是空的，因为该方法将 “latest” 作为默认值。

另一种方法是使用 Javascript/TypeScript 中的`axios`库。例如，要获取地址余额，可以使用以下代码：

```typescript
import axios from 'axios';

const node = '<node-endpoint>';
const address = '<address>';

const response = await axios.post(node, {
  jsonrpc: '2.0',
  method: 'eth_getBalance',
  params: [address, 'latest'],
  id: 1,
  headers: {
    'Content-Type': 'application/json',
  },
});
```
你可能注意到了，JSON-RPC 方法包装在一个 POST 请求中，参数在请求主体中传递。
这是使用 OSI 的应用层（ HTTP 协议）在客户端和服务器之间交换数据的另一种方式。

无论哪种方式，与以太坊网络交互的最常见用途是使用 web3 库，例如用于 python 的 web3py 或用于 JS/TS 的 web3. JS/ ether . JS：
#### web3py

```python
from web3 import Web3

# Set up HTTPProvider
w3 = Web3(Web3.HTTPProvider('http://localhost:8545'))

# API
w3.eth.get_balance('0xaddress')
```

#### ethers.js

```typescript
import { ethers } from "ethers";

const provider = new ethers.providers.JsonRpcProvider('http://localhost:8545');

await provider.getBlockNumber();
```

通常，所有 web3 库都会封装 JSON-RPC 方法，以提供一种更友好的方式与执行层交互。请参阅您首选的编程语言，因为语法可能有所不同。

### 进一步的阅读
* [Ethereum JSON-RPC Specification](https://ethereum.github.io/execution-apis/api-documentation/)
* [Execution API Specification](https://github.com/ethereum/execution-apis/tree/main)
* [JSON-RPC | Infura docs](https://docs.infura.io/api/networks/ethereum/json-rpc-methods)
* [reth book | JSON-RPC](https://paradigmxyz.github.io/reth/jsonrpc/intro.html)
* [OpenRPC](https://open-rpc.org/getting-started)
