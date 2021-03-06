## FastX 特点介绍

​	FastX 基于 omisego 的 plasma-mvp 进行了一些改进。使得其更适合用于去中心化交易所。下面我们对 FastX 的一些特点进行介绍。

### 1、支持 ERC20 和 ERC721

​	身为一个交易所，只支持 ETH 显然是不够的。FastX 增加了对 ERC20 和 ERC721 的支持。将 transaction 中的 amount 字段扩充为 contractAddress、amount 和 tokanId，并在主链合约 deposit 函数中支持 ERC20 和 ERC721 的转账。但是在 token 执行退出操作的时候可能因为 token 合约不规范导致出错无法完成，由于我们的退出方式是以有序队列的形式退出的，所以一旦出现这种问题将会导致退出队列阻塞。为了解决这个问题我们需要把退出操作分解成两个操作，第一个操作将相应退出请求从等待退出队列中取出，并将它加入一个可退出池，而这个可退出池是无序的。第二个操作再从可退出池中获取相应的退出操作进行退出，那么此时再出现由于 token 合约问题导致的错误就不会影响到 FastX 主链合约中其他正常的退出请求了。

​	如果我们扩展这个退出方案，在请求退出时发放给用户一个可交易的凭证，而在最终完成退出时用凭证换取 token 而不是直接将 token 发放至申请退出的地址上，那么用户可以交易这个凭证从而实现快速退出，而不需要等待较长的挑战期。

### 2、Transaction 支持超时

​	假设 A 有一个 ERC721 的 token 出售，B 用 Eth 购买。那么完成这个交易的 transaction 需要两个输入，一个是 token，另一个是一定数量的 Eth。因此我们需要者两个输入的拥有者也就是 A 和 B，分别对这个 transaction 签名。然而在实际操作中 A 和 B 不可能同时完成签名，一定会有一个先后关系，如果 A 先做了签名并交给 B，那么 B 就可以将这个交易一直压后，而不将它发布至网络中。为了解决这个问题，我们为 transaction 增加了超时时间以指明最晚将该 transaction 所在 block 提交至主网合约的时间，并增加挑战机制以保证提交到主网合约的超时交易为无效交易。在有了超时时间后，A 就可以放心的把 transaction 交给 B 签名，如果 B 不在规定时间内完成签名并提交至网络的话，这个交易就会被视为无效。

​	我们进一步扩展了这个想法，A 可以直接对 transaction 中除了 B 的输入以外的部分进行签名，这个签名过程完全不包含 B 的任何信息，因此这个签名是可以由 A 独自完成的。然后 A 可以将这个缺少一个输入项包含 A 签名的 transaction 发布至任意信息交流平台。在超时时间内，任何人都可以给予这个 transaction 满足条件的输入项并签名发布至 FastX 网络中，从而完成交易。此时我们称 A 的这个交易为部分签名交易，它可以被视为一个求购单或者售卖单。

### 3、部分签名交易池

​	在有了部分签名交易之后，很自然的就需要一个地方来发布和交流这些信息。因此部分签名交易池就可以作为这样一个信息交流的地方。FastX 网络的节点可以很自然的运行这样一个交易池，因为它可以提供交易深度从而提高交易量。交易池可以同时存在多个，实际上任何可以用于交流部分签名交易信息的地方都可以被视为一个部分签名交易池。

### 4、SDK

​	FastX 为开发者提供了一个全功能的 SDK。SDK 对 FastX 的主网合约交互以及子网的交互进行了封装，使用 SDK 就可以屏蔽这些底层细节，让开发者能专注于自己的业务逻辑。即使是对区块链并不是很了解的传统开发者也可以方便的进行开发。

​	我们举一个集换式卡牌游戏作为例子。使用 SDK 游戏开发者可以像使用数据库一样对卡牌资源进行创建、读取以及售卖的操作。创建卡牌时，SDK 会自动完成在主网创建合约、存入 FastX 网络的操作。读取卡牌信息时，SDK会综合相应卡牌的主网信息以及 FastX 网络信息给予反馈。售卖卡牌则会自动向交易池发布售卖单。发售新卡牌时，游戏开发者可以调用 SDK 进行创建和售卖。而在玩家进行卡牌对战时，游戏开发者可以通过 SDK 确认玩家签名并读取他拥有的卡牌。