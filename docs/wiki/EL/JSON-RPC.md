# JSON-RPC

JSON-RPC规范是基于[OpenRPC](https://www.open-rpc.org/docs/getting-started)以JSON编码的远程过程调用协议。它允许调用远程服务器上的函数，并返回结果。
它是执行 API 规范的一部分，该规范提供了一组与以太坊区块链交互的方法。
更为人所知的是用户如何使用客户端与网络交互的方式，甚至共识层 (CL) 和执行层 (EL) 如何通过引擎 API 交互。
本节提供 JSON-RPC 方法的描述。

## API 规范

JSON-RPC 方法按指定为方法前缀的命名空间进行分组。尽管它们都有不同的目的，但它们都共享一个共同的结构，并且在所有实现中必须表现相同：

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "<prefix_methodName>",
  "params": [...]
}
```

其中：
- `id`：请求的唯一标识符。
- `jsonrpc`：JSON-RPC 协议的版本。
- `method`：要调用的方法。
- `params`：方法的参数。如果该方法不需要任何参数，它可以是一个空数组。如果没有提供，其他的可能有默认值。

### 命名空间

每个方法都由命名空间前缀和方法名称组成，并用下划线分隔。

以太坊客户端必须实现规范要求的与网络交互的 RPC 方法的最小基本集。除此之外，还有客户端特定方法用于控制节点或实现额外的独特功能。请始终参考列出可用方法和命名空间的客户端文档，例如注意 [Geth](https://geth.ethereum.org/docs/interacting-with-geth/rpc) 和 [Reth](https://reth.rs/jsonrpc/intro/) 文档中的不同命名空间。 

以下是最常见命名空间的示例： 

| **命名空间** | **描述** | **敏感** |
| ------------- | ---------------------------------------------------------------------------------------------------- | ------------- |
|以太坊 | eth API 允许您与以太坊交互。                                                    |也许|
|网络3 | web3 API 为 web3 客户端提供实用函数。                                         |没有 |
|网 | net API 提供对节点网络信息的访问。                                      |没有 |
|交易池 | txpool API 允许您检查交易池。                                           |没有 |
|调试|调试 API 提供了多种方法来检查以太坊状态，包括 Geth 样式跟踪。   |没有 |
|追踪|跟踪 API 提供了多种方法来检查以太坊状态，包括奇偶校验样式跟踪。 |没有 |
|管理员 |管理员 API 允许您配置您的节点。                                                     |是的 |
|远程进程调用 | rpc API 提供有关 RPC 服务器及其模块的信息 |没有 |

敏感意味着它们可用于设置节点，例如 *admin*，或访问存储在节点中的帐户数据，就像 *eth* 一样。

现在，让我们看一些方法来了解它们是如何构建的以及它们的作用：

#### 以太坊

Eth 可能是最常用的命名空间，提供对以太坊网络的基本访问，例如钱包需要读取余额并创建交易。 
这里仅提供了方法的简要列表，但完整列表可以在 [以太坊 JSON-RPC 规范](https://ethereum.github.io/execution-apis/api-documentation/) 中找到。

| **方法** |           **参数** | **描述** |
| ------------------------------------ |:-------------------------------:| ------------------------------------------------------------------------------------------------------------------------------------------- |
| eth_blockNumber |       没有强制参数 |返回最近的区块 | 的编号
| eth_call |       交易对象 |立即执行新的消息调用，而不在区块链上创建交易 |
| eth_chainId |       没有强制参数 |返回当前链 ID |
| eth_estimateGas |       交易对象 |调用 or 交易，不会添加到区块链中，并返回已使用的 gas，可用于估计已使用的 gas |
| eth_gasPrice |       没有强制参数 |返回每个 gas 的当前价格(以 wei 为单位) |
| eth_getBalance |      地址，区块号码 |返回给定地址的账户余额 |
| eth_getBlockByHash |      区块哈希，完整交易 |通过哈希返回有关区块的信息 |
| eth_getBlockByNumber |     区块号码，完整交易 |按区块编号返回有关区块的信息 |
| eth_getBlockTransactionCountByHash |           区块哈希 |从与给定的区块匹配的区块返回区块中的交易的数量 |
| eth_getBlockTransactionCountByNumber |          区块号 |从与给定区块编号匹配的区块返回区块中的交易编号 |
| eth_getCode |      地址，区块号码 |返回区块链中给定地址处的代码 |
| eth_getLogs |          过滤对象|返回与给定过滤器对象匹配的所有日志的数组 |
| eth_getStorageAt |地址、位置、区块号码 |返回给定地址的存储位置的值 |

#### 调试

*debug* 命名空间提供了检查以太坊状态的方法。它可以直接访问原始数据，这对于某些用例(例如区块浏览器或研究目的)可能是必需的。其中一些方法可能需要在节点上进行大量计算，并且对非存档节点上的历史状态的请求大多是不可行的。因此，公共 RPC 的提供者通常会限制此命名空间或仅允许安全方法。 
以下是调试方法的基本示例： 

| **方法** |      **参数** | **描述** |
|--------------------------|:---------------------:|-----------------------------------------------------------------|
| debug_getBadBlocks |  没有强制参数 |返回客户端见过的最近不良区块的数组 |
| debug_getRawBlock |     block_number |返回 RLP 编码的区块 |
| debug_getRawHeader |     block_number |返回 RLP 编码的标头 |
| debug_getRawReceipts |     block_number |返回 EIP-2718 二进制编码收据数组 |
| debug_getRawTransactions |        tx_hash |返回 EIP-2718 二进制编码的数组交易 |

#### 引擎

[Engine API](https://hackmd.io/@danielrachi/engine_api) 与前面提到的通用以太坊 JSON‑RPC 方法不同。执行客户端在单独的经过身份验证的端点上为引擎 API 提供服务，而不是在正常的 HTTP JSON‑RPC 端口上，因为它不是面向用户的 API。其唯一目的是促进客户端之间的通信，在共识和执行客户端之间交换有关共识、分叉选择和 区块验证的信息。

客户端间通信通过 HTTP 上的 JSON‑RPC 接口进行操作，并使用 JSON Web 令牌 (JWT) 进行保护。 JWT 将发件人验证为有效的共识层客户端，尽管它不提供流量加密。此外，引擎 JSON-RPC 端点只能由共识层访问，确保恶意外部方无法与其交互。

下表列出了引擎 API 的核心方法，并简要说明了它们的用途和它们接受的参数：
| **方法** |               **参数** | **描述** |
|------------------------------------------|:--------------------------------------:|---------------------------------------------------------------------------|
| engine_exchangeTransitionConfigurationV1 |        共识客户端配置 | | 在 CL 和 EL 之间交换配置详细信息 |
| engine_forkchoiceUpdatedV1* |  forkchoice_state、载荷属性 |更新 forkchoice 状态并可选择启动载荷构建 |
| engine_getPayloadBodiesByHashV1* |           block_hash(数组)|给定区块哈希，返回相应的执行载荷实体 |
| engine_getPayloadV1* |  forkchoice_state、载荷属性 |获取由 EL 构建的执行载荷 |
| debug_newPayloadV1* |                tx_hash |返回执行载荷验证详细信息用于调试目的 |

那些标有星号(*)的方法有多个版本来支持网络升级和不断发展的协议功能。 [以太坊 JSON-RPC 规范](https://ethereum.github.io/execution-apis/api-documentation/) 提供了有关这些方法的详细文档。

## 编码

JSON-RPC 方法的参数编码有一个约定，即十六进制编码。
* 数量使用“0x”前缀表示为十六进制值。
  * 例如，数字 65 表示为“0x41”。
  * 数字 0 表示为“0x0”。
  * 一些无效的用法是“0x”和“ff”。由于第一种情况没有后续数字，并且第二种情况没有前缀“0x”。 
* 未格式化的数据(例如哈希、帐户地址或字节数组)也使用“0x”前缀进行十六进制编码。
  * 例如：0x400(十进制为 1014)
  * 无效的情况是 0x0400，因为不允许使用前导零

## 与传输无关

值得一提的是，JSON-RPC 与传输无关，这意味着它可以在任何传输协议上使用，例如 HTTP、WebSockets (WSS)，甚至进程间通信 (IPC)。
它们的区别可以总结如下：
* **HTTP** 传输提供单向响应请求模型，该模型在发送响应后关闭连接。
* **WSS** 是双向协议，这意味着连接保持打开状态，直到节点或用户明确关闭它。它允许基于订阅的模型通信，例如事件驱动的交互。
* **IPC** 传输协议用于同一机器上运行的进程之间的通信。它比 HTTP 和 WSS 更快，但不适合远程通信，例如它可以通过本地 JS 控制台使用。
  
## 工装

有多种方式可以使用 JSON-RPC 方法。其中之一是使用 `curl` 命令。例如，要获取最新的区块号码，可以使用以下命令：

```bash
curl <node-endpoint> \
-X POST \
-H "Content-Type: application/json" \
-d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```
请注意 *params* 字段为何为空，因为该方法传递“latest”作为其默认值。

另一种方法是在 Javascript/TypeScript 中使用 `axios` 库。例如，要获取地址余额，可以使用以下代码：

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
您可能会注意到，JSON-RPC 方法包装在 POST 请求中，并且参数在请求正文中传递。
这是使用 OSI 的应用层在客户端和服务器之间交换数据的不同方式：HTTP 协议。

无论哪种方式，与以太坊网络交互的最常见用途是使用 web3 库，例如用于 python 的 web3py 或用于 JS/TS 的 web3.js/ethers.js：

#### 网络3py

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

通常，所有 web3 库都包装 JSON-RPC 方法，提供更友好的方式与执行层交互。请以您喜欢的编程语言进行查找，因为语法可能会有所不同。

### 进一步阅读
* [以太坊 JSON-RPC 规范](https://ethereum.github.io/execution-apis/api-documentation/)
* [执行API规范](https://github.com/ethereum/execution-apis/tree/main)
* [JSON-RPC | Infura 文档](https://docs.metamask.io/services/reference/ethereum/json-rpc-methods/)
* [Reth书| JSON-RPC](https://reth.rs/jsonrpc/intro/)
* [OpenRPC](https://www.open-rpc.org/docs/getting-started)
* [Engine API |米哈伊尔 |第21讲](https://youtu.be/fR7LBXAMH7g)
