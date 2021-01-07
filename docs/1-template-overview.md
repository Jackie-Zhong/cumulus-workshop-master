# 平行链收集者模板

熟悉[Substrate Node Template](https://github.com/substrate-developer-hub/substrate-node-template)的开发者将会发现平行链模板很熟悉。
它们具有相同的通用结构，包括`节点`，`运行时逻辑`，以及 `托盘` 目录。它们的运行时间是相似的，并具有许多相同的托盘。
除了一些新的特性，托盘本身基本上是相同的。许多[Substrate devhub tutorials](https://substrate.dev/tutorials/)可以在平行链模板上通过少量的的修改而被使用。


这两个模板之间的相似性应该会给您带来信心，如果您已经构建一个Substrate链，那么您构建一个平行链就没有问题了！


## 节点模板的不同

然而，这两个模板之间有一些重要的区别，值得从一开始就观察。


### 平行链信息托盘

这个托盘被设计的目的是将有关平行链的注册注入到它自己的运行时逻辑中，目前它只注入链注册的平行链id。这使得运行时逻辑可以知道哪些跨链消息是针对它的。


### 消息代理托盘

这个托盘负责将通过网络接收的块链消息分发到它们要用的托盘上。如果您打算接收跨链消息，您应该可以使用这个托盘。


### 代币商托盘

这个托盘可以作为发送和接收跨链消息的示例。他是实现我们前面看到的[跨链代币转账](../4-cross-chain/1-downward.md)。
如果您想要接收DOTs或者来自国外的链的资产，您应该使用这个托盘或者使用它的代码作为灵感。


### `register_validate_block!` 宏

每一个平行链必须向中继链提供一个`validate_block`函数，表示作为一个wasm blob。节点模板没有提供这个函数，但是平行链模板有。
多亏了cumulus，为Substrate运行时逻辑创建这个函数就像在运行时底部添加一行代码一样简单：


```rust
cumulus_runtime::register_validate_block!(Block, Executive);
```

### 没有Aura托盘

许多流行的Substrate运行时逻辑，像Polkadot，包含了consensus-related托盘和runtime-apis。这个节点模板以Aura托盘及其相关运行时逻辑的`AuraApi`为特色。
两者都是在平行链模板中缺失的。


这是因为收集者节点的authors区块在一个规则的间隔，而不是使用更复杂的共识引擎，如Aura，Babe，或者PoW。
在未来，这个authoring的行为可能是可配置的，并且运行时逻辑的这些部分可能会被重新引入。关于这方面的进展，请访问：


- https://github.com/paritytech/cumulus/issues/166
- https://github.com/paritytech/cumulus/issues/36
- https://github.com/paritytech/cumulus/issues/115
- https://github.com/paritytech/cumulus/issues/116

### 没有GRANDPA托盘

许多流行的Substrate运行时逻辑，包括节点模板，都有一个与finality-related相关的`GrandpaApi`。
这两者都是平行链模板中确实的。
Many popular Substrate runtimes including the node template feature a finality-related GRANDPA
pallet and its associated `GrandpaApi`. These are both missing from the parachain template.

这是因为平行链跟随的是中继链的最终性而不是运行它们自己的最终行的工具。这是基本的Polkadot的架构，而且不会改变。


### 服务

收集者节点的服务与节点模板的服务是完全不同的。虽然您可以找到相似之处，但是入伍的架构却大不相同。这个新的服务是cumulus提供的主要变化。


> 当使用Cumulus去修改现有的Substrate链时，通常最好是从模板中复制服务代码。
> service code from the template.

## 使用模板

下面几节中的说明将使用平行链模板简单的处理一些基本的练习。它们可以按照任何顺序被遵循，而不是建立在彼此之上。要了解Substrate开发的更多的彻底的入门，搜索[Substrate Developer Hub](https://substrate.dev)。


这些指令假设您已经在本地系统[compiled the parachain template](../1-prep/1-compiling.md)上编译了。如果您没有，请返回去做。

如果您是一个github用户，可以从分支
If you're a github user, begin by[forking the parachain template](https://github.com/substrate-developer-hub/substrate-pallet-template)开始，然后在您的fork中使用。