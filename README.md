

# LDFStock Core
The LDFStock Core smart contracts provide a system for launching regulatory-compliant securities tokens on a decentralized blockchain. This particular repository is the implementation of a system that allows for the creation of ST-20-compatible tokens. This system has a modular design that promotes a variety of pluggable components for various types of issuances, legal requirements, and offering processes.

## Introduction to Security Tokens

### What is a Security token?
A Security Token shares many of the characteristics of both fungible (erc20) and non-fungible tokens (erc721). Security tokens are designed to represent complete or fractional ownership interests in assets and/or entities. While utility tokens have no limitations on who can send or receive the token, security tokens are subject to many restrictions based on identity, jurisdiction and asset category.

数字证券具有可替代token（ERC20）和不可替代token（ERC721）的许多特征。 数字证券旨在代表资产和/或实体的全部或部分所有权权益。 尽管实用程序token对谁可以发送或接收token没有限制，但是数字证券受身份，管辖权和资产类别的限制。



由于我们经常被问到“什么是ST-20数字证券？” —我们将在这里Pablo Ruiz进行介绍：

> ST-20数字证券是在ERC-20协议的基础上实现的基于以太坊的数字证券，它增加了数字证券根据特定规则控制交换的功能。ST-20数字证券依赖于交换管理器*来确定数字证券应采用的规则集，以允许或拒绝在对等交易所中在发行方与投资者之间进行的转让，或在对等交易所中进行的交易。

### Security Tokens Vs. Utility Tokens?
The concept of utility tokens is fairly well understood in the blockchain space today. Utility tokens represent access to a network, and your token purchase represents the ability to buy goods or services from that network- Think of when you purchase a arcade token to allow you to play an arcade game machine. Utility tokens give you that same type of access to a product or service. On the other hand, security tokens represent complete or fractional ownership in an asset (such as shares in a company, a real-estate asset, artwork, etc). Such as having a stake in a company, real estate, or intellectual property can all be represented by security tokens. Security tokens offer the benefit of bringing significant transparency over traditional paper shares through the use of the blockchain and its associated public ledger. Security token structure, distribution, or changes that could affect investors are now accessible to all via the blockchain.

实用通证的概念在当今的区块链领域中已得到很好的理解。 实用通证token表示对网络的访问，而购买token则表示从该网络购买商品或服务的能力-想想何时购买街机token以允许您玩街机游戏机。 实用通证为您提供了对产品或服务的相同访问类型。 另一方面，数字证券表示资产（例如公司的股份，房地产资产，艺术品等）的完全或部分所有权。 例如在公司，房地产或知识产权中拥有股份，都可以用数字证券表示。 通过使用区块链及其相关的公共分类帐，数字证券提供了相对于传统纸质股票而言显着透明的优势。 现在可以通过区块链访问数字证券结构，分布或可能影响投资者的变更。

# 开发人员和更广泛的社区如何使用它？

坚实的基础为LDFStock平台的构建奠定了基础。现在其他技术人员可以在此基础上开发新的*STO模块*，该*模块*定义了如何处理和实施数字证券发行。LDFStock已交付了一个样本*STO模块*，它将成为许多实际STO模块中的第一个。

重要的是要注意，我们现在正处于拐点。现在，我们将从一个数字证券很少的世界过渡到一个可以快速轻松地创建数字证券的世界，其中内置了必要的交易限制和合规性要求。这为开发人员，企业和LDFStock创造了巨大的机会社区成员构建有用的工具（桥梁，道路，隧道和管道），这些工具将在数字证券发行过程中反复使用。

# ST-20 Interface Overview
## Description
An ST-20 token is an Ethereum-based token implemented on top of the ERC-20 protocol that adds the ability for tokens to control transfers based on specific rules. ST-20 tokens rely on Transfer Managers to determine the ruleset the token should apply in order to allow or deny a transfer, be it between the issuer and investors, in a peer to peer exchange, or a transaction with an exchange.

ST-20 token是在ERC-20协议之上实现的基于以太坊的token，它增加了token根据特定规则控制交换的功能。 ST-20 token依赖于交换管理器来确定token应采用的规则集，以允许或拒绝在对等交易所中在发行方与投资者之间进行的转让，或在对等交易所中进行的交易。

## How it works
ST-20 tokens must implement a **verifyTransfer method** which will be called when attempting to execute a **transfer** or **transferFrom** method. The verifyTransfer method will determine whether that transaction can be completed or not. The implementation of verifyTransfer can take many forms, but the default approach is a **whitelist** controlled by the **GeneralTransferManager.**

ST-20 token必须实现一个verifyTransfer方法，该方法将在尝试执行transfer或transferFrom方法时被调用。 verifyTransfer方法将确定该事务是否可以完成。 verifyTransfer的实现可以采用多种形式，但是默认方法是由GeneralTransferManager控制的白名单。

## The ST-20 Interface
```
contract IST20 {

    // 链下Hash加密token(股份，房地产资产，艺术品)  off-chain hash
    bytes32 public tokenDetails;

    //transfer, transferFrom must respect the result of verifyTransfer
    function verifyTransfer(address _from, address _to, uint256 _amount) view public returns (bool success);

    //used to create tokens
    function mint(address _investor, uint256 _amount) public returns (bool success);
}
```
# The LDFStock Core Architecture
The diagram below depicts a high-level view of the various modules, registries, and contracts implemented within LDFStock Core 2.0.0:

![LDFStock Core architecture](https://github.com/LDFStockNetwork/LDFStock-core/blob/dev-3.0.0/docs/images/LDFStock%20Core%20v3.png)

## Components
### LDFStock Registries

**Security Token Registry (STR)** - This registry tells us which tokens and tickers have been registered to it. This allows us to prevent people from reserving the same ticker as another issuer as well checking for inputs such as making sure it is a maximum of 10 characters and what the expiry date is on the respective ticker. Right now, if you reserve a ticker it last for 60 days. After it expires someone else can go ahead and reserve it or they you can re-register it.

该注册表告诉我们已向其注册了哪些 token和数字股票代码。 这使我们能够防止人们保留与另一发行人相同的股票行情，并检查输入内容，例如确保最多10个字符以及相应股票行情的到期日。 现在，如果您保留股票报价，它将持续60天。 过期后，其他人可以继续保留它，或者他们可以重新注册。

With the **2.0.0 Core Release**, when you deploy a token you do it through the ST registry and it keeps a record of which tokens have been registered within it. 数字证券注册流程

**The Module Registry** - This registry keeps a record of all the different module factories.

​                                           token不同的工厂模块注册

**The Features Registry** - A registry of features that we may enable in the future but right now only LDFStock has control of the features. Later, LDFStock can easily turn access on and off.

我们将来可能会启用的功能注册表，但现在只有LDFStock可以控制这些功能。 之后，LDFStock可以轻松地打开和关闭访问。

To be clear, each module has its own factory which is in charge of deploying an instance of that module for the issuers token.

需要明确的是，每个模块都有其自己的工厂，该工厂负责为发行者 token部署该模块的实例。

There are General factories which every token uses (if wanted). It works by sending the token to the factory where it asks for an instance of that said module and the token will add an instance of that module to itself. This allows for each token to have its unique modules associated with it. All of this is created through the factories and the module registry keeps a records of all the different modules factories that are registered.

每个 token都有General factories通用工厂（如果需要）。 它通过将 token发送到工厂来请求该模块的实例来工作， token将向其自身添加该模块的实例。 这允许每个 token具有与其关联的唯一模块。 所有这些都是通过工厂创建的，模块注册表保留了所有已注册的不同模块工厂的记录。

As of now, LDFStock is the only one that can add or register a module factory to the module registry. LDFStock submits the modules to the registry, however, we are exploring different approaches to open up development to other parties such as potentially working with external developers to provide services to issuers through modules.

到目前为止，LDFStock是唯一可以将模块工厂添加或注册到模块注册表中的模块。 LDFStock将模块提交给注册表，但是，我们正在探索不同的方法来向其他方开放开发，例如可能与外部开发人员合作以通过模块向发行人提供服务。

**LDFStock has 3 main registries**  数字证券3个注册流程，3个注册地址-----**LDFStock管理

1. Security Token Registry
2. Features Registry
3. Module Registry

The LDFStock Registry holds the addresses of the 3 registries above.

As of the **2.0.0 release**, we have built it out so that the Module and Security Token Registry are upgradeable. This means that down the road if there is something in the logic that we need to change, we can do that without having to re-deploy the whole thing again. All we need to do is update it.

从2.0.0版本开始，我们对其进行了扩展，以便可以升级模块和数字证券注册表。 这意味着，如果在逻辑上某些事情我们需要更改，那么我们可以做到这一点，而不必再次重新部署整个事情。 我们需要做的就是更新它。

### Modules

**Security Token (ST-20)**: The SecurityToken is an implementation of the ST-20 protocol that allows the addition of different modules to control its behavior. Different modules can be attached to a SecurityToken.

数字证券 token（ST-20）：SecurityToken是ST-20协议的实现，该协议允许添加不同的模块来控制其行为。 可以将不同的模块（工厂模块）附加到SecurityToken。

We have a ST-20 token which is an Ethereum-based token implemented on top of the ERC-20 protocol that adds the ability for tokens to control transfers based on specific rules. ST-20 tokens rely on Transfer Managers to determine the ruleset the token should apply in order to allow or deny a transfer, be it between the issuer and investors, in a peer to peer exchange, or a transaction with an exchange.

我们有一个ST-20 token，它是基于Ethereum的 token，是在ERC-20协议的基础上实现的，它增加了 token的功能，可以根据特定规则控制交换。 ST-20 token依靠转移管理器来确定 token应采用的规则集，以允许或拒绝该转移，无论是在发行方与投资者之间，在点对点交换中还是在与交易所进行的交易中。

To simplify, it breaks down to having a base token that gives the issuer the ability to add functionality through modules.

为简化起见，它分解为具有基本 token，该 token使发行者能够通过模块添加功能。

###### Example

We have modules that can deal with transfer management. Restricting transfers through a whitelist  or just restricting a transfer between addresses that could make an account go over a specified limit or you can limit the amount of a token holders or you can even limit transfers to prevent dumping of tokens by having a lockup period for token holders.

我们有可以处理转移管理的模块。 通过白名单限制转移，或者仅限制地址之间的转移，这可能会使帐户超过指定的限制，或者您可以限制 token持有者的数量，或者甚至可以通过具有 token的锁定期来限制转移以防止丢弃 token 持有人。

#### The LDFStock Modules
**TransferManager modules:** These control the logic behind transfers and how they are allowed or disallowed. By default, the ST (Security Token) gets a `GeneralTransferManager` module attached in order to determine if transfers should be allowed based on a whitelist approach.

这些控制着交换背后的逻辑以及如何允许或禁止它们。 默认情况下，ST（数字证券）会附加GeneralTransferManager模块，以便根据白名单方法确定是否应允许交换。

The `GeneralTransferManager` behaves differently depending who is trying to transfer the tokens.
a) In an offering setting (investors buying tokens from the issuer) the investor's address should be present on an internal whitelist managed by the issuer within the GeneralTransferManager.

GeneralTransferManager的行为会有所不同，具体取决于谁在尝试转账token。
a）在发售设置中（投资者从发行人购买token），投资者的地址应出现在GeneralTransferManager中由发行人管理的内部白名单中。

b) In a peer to peer transfer, restrictions apply based on real-life lockups that are enforced on-chain. For example, if a particular holder has a 1-year sale restriction for the token, the transaction will fail until that year passes.

b）P2P转账中，限制基于链上强制执行的实际锁定进行。 例如，如果特定持有人对该token有1年的销售限制，则该交易将失败，直到该年过去。

**Security Token Offering (STO) modules:** A SecurityToken can be attached to one (and only one) STO module that will dictate the logic of how those tokens will be sold/distributed. An STO is the equivalent to the Crowdsale contracts often found present in traditional ICOs.

可以将SecurityToken附加到一个（只有一个）STO模块，该模块将指示如何出售/分配这些token的逻辑。 STO等同于传统ICO中经常出现的Crowdsale合同。

**Permission Manager modules:** These modules manage permissions on different aspects of the issuance process. The issuer can use this module to manage permissions and designate administrators on his token. For example, the issuer might give a KYC firm permissions to add investors to the whitelist.

这些模块管理发行过程不同方面的权限。 发行者可以使用此模块来管理权限并为其token指定管理员。 例如，发行人可能授予KYC公司将投资者添加到白名单的权限。

**Checkpoint Modules**
These modules allow the issuer to define checkpoints at which token balances and the total supply of a token can be consistently queried. This functionality is useful for dividend payment mechanisms and on-chain governance, both of which need to be able to determine token balances consistently as of a specified point in time.

这些模块允许发行者定义检查点，在该检查点可以一致地查询token余额和token总供应量。 此功能对于股息支付机制和链上治理非常有用，这两者都需要能够在指定的时间点一致地确定token余额。

**赎回模块  Burn Modules**
These modules allow issuers or investors to burn or redeem their tokens in exchange of another token which can be on chain or offchain.

这些模块允许发行人或投资者燃烧或赎回其token，以换取另一个可以在链上或在链外的token

With the Core **2.0.0 Release**, LDFStock has also introduced the `USDTieredSTO`. This new STO module allows a security token to be issued in return for investment (security token offering) in various currencies (ETH, ST-LDF & a USD stable coin). The price of tokens is denominated in USD and the STO allows multiple tiers with different price points to be defined. Discounts for investments made in ST-LDF can also be defined.

。

在Core 2.0.0发行版中，LDFStock还引入了USDTieredSTO。 这个新的STO模块允许以各种货币（ETH，ST-LDF和USD稳定币）发行数字证券以换取投资（数字证券供给）。 token的价格以美元计价，STO允许定义具有不同价格点的多个等级。 还可以定义在ST-LDF中进行投资的折扣。

## CLI and CLI Documentation Wiki:

The CLI is for users that want to easily walkthrough all the details of an STO issuance. The CLI documentation is located on our [Github Wiki](https://github.com/LDFStockNetwork/LDFStock-core/wiki).

You can easily navigate through it with the sidebar directory in order to run the CLI and set up and test the following:

1. Prerequisite Instructions / Deploy and setup the LDFStock contracts
2. Launch the CLI on Ganache
3. Use the Faucet to get ST-LDF
4. Deploy a token + Launch a USDTieredSTO
5. Whitelist investors
6. Work with the Dividends module
7. Using other CLI features


# Setting up LDFStock Core

## Mainnet
### v3.0.0

V3 Audit Report by Consensys Diligence: https://github.com/LDFStockNetwork/LDFStock-audit-report-2019-04

| Contract                                                         | Address                                                                                                                       |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| SecurityTokenRegistry (Data Store):                                | [0x240f9f86b1465bf1b8eb29bc88cbf65573dfdd97](https://etherscan.io/address/0x240f9f86b1465bf1b8eb29bc88cbf65573dfdd97)                                              |
| SecurityTokenRegistry (Logic):                                | [0x92274793a65a0de42bb4bf19b393930863877630](https://etherscan.io/address/0x92274793a65a0de42bb4bf19b393930863877630)                                              |
| ModuleRegistry (Data Store):                                       | [0x4566d68ea96fc2213f2446f0dd0f482146cee96d](https://etherscan.io/address/0x4566d68ea96fc2213f2446f0dd0f482146cee96d)                                              |
| ModuleRegistry (Logic):                                       | [0x7550fe3308ba534b44e94c83cd08b7e3c5b96db5](https://etherscan.io/address/0x7550fe3308ba534b44e94c83cd08b7e3c5b96db5)                                              |
| LDFStock Registry:                                            | [0xdfabf3e4793cd30affb47ab6fa4cf4eef26bbc27](https://etherscan.io/address/0xdfabf3e4793cd30affb47ab6fa4cf4eef26bbc27)                                              |
| Feature Registry:                                            | [0xa3eacb03622bf1513880892b7270d965f693ffb5](https://etherscan.io/address/0xa3eacb03622bf1513880892b7270d965f693ffb5)                                              |
| ETHOracle:                                                   | [0x60055e9a93aae267da5a052e95846fa9469c0e7a](https://etherscan.io/address/0x60055e9a93aae267da5a052e95846fa9469c0e7a)                                              |
| ST-LDFOracle:                                                   | [0x52cb4616E191Ff664B0bff247469ce7b74579D1B](https://etherscan.io/address/0x52cb4616E191Ff664B0bff247469ce7b74579D1B)                                              |
| General Transfer Manager Factory:                              | [0x5fafcfc0afd80d2f95133170172b045024ca8fd1](https://etherscan.io/address/0x5fafcfc0afd80d2f95133170172b045024ca8fd1)                                              |
| General Permission Manager Factory:                             | [0xeb4c8c9d71cbe60ca0e688e4e70c5ab22abb72a4](https://etherscan.io/address/0xeb4c8c9d71cbe60ca0e688e4e70c5ab22abb72a4)                                              |
| CappedSTOFactory:                                               | [0x7c64e9cfc397db2da3213a172d783f1b9c30d7ef](https://etherscan.io/address/0x7c64e9cfc397db2da3213a172d783f1b9c30d7ef)                                              |
| USDTieredSTO Factory:                                           | [0x80ae6e1b6dc661d21ee1680bd5ff919f0400f17d](https://etherscan.io/address/0x80ae6e1b6dc661d21ee1680bd5ff919f0400f17d)                                              |
| ERC20 Dividends Checkpoint Factory:                             | [0x550fc7d520f596bfdf75dca4d9f5f3c0c6020212](https://etherscan.io/address/0x550fc7d520f596bfdf75dca4d9f5f3c0c6020212)                                              |
| Count Transfer Manager Factory:                               | [0xA8e0a4E7f0cdECF43AFbA0360B6f64412Df2e6B0](https://etherscan.io/address/0xA8e0a4E7f0cdECF43AFbA0360B6f64412Df2e6B0)                                              |
| Percentage Transfer Manager Factory:                             | [0x5732ee7ef44dc5ab7b7cbac8ada5268c96895ca5](https://etherscan.io/address/0x5732ee7ef44dc5ab7b7cbac8ada5268c96895ca5)                                              |
| Manual Approval Transfer Manager Factory:                        | [0x156389b30ae9e5ca8ec9e55ff529738480e42214](https://etherscan.io/address/0x156389b30ae9e5ca8ec9e55ff529738480e42214)                                              |



### v2.0.0

| Contract                                                         | Address                                                                                                                       |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| SecurityTokenRegistry (Proxy):                                | [0x240f9f86b1465bf1b8eb29bc88cbf65573dfdd97](https://etherscan.io/address/0x240f9f86b1465bf1b8eb29bc88cbf65573dfdd97)                                              |
| ModuleRegistry (Proxy):                                       | [0x4566d68ea96fc2213f2446f0dd0f482146cee96d](https://etherscan.io/address/0x4566d68ea96fc2213f2446f0dd0f482146cee96d)                                              |
| LDFStock Registry:                                            | [0xdfabf3e4793cd30affb47ab6fa4cf4eef26bbc27](https://etherscan.io/address/0xdfabf3e4793cd30affb47ab6fa4cf4eef26bbc27)                                              |
| Feature Registry:                                            | [0xa3eacb03622bf1513880892b7270d965f693ffb5](https://etherscan.io/address/0xa3eacb03622bf1513880892b7270d965f693ffb5)                                              |
| ETHOracle:                                                   | [0x60055e9a93aae267da5a052e95846fa9469c0e7a](https://etherscan.io/address/0x60055e9a93aae267da5a052e95846fa9469c0e7a)                                              |
| ST-LDFOracle:                                                   | [0x52cb4616E191Ff664B0bff247469ce7b74579D1B](https://etherscan.io/address/0x52cb4616E191Ff664B0bff247469ce7b74579D1B)                                              |
| General Transfer Manager Factory:                              | [0xdc95598ef2bbfdb66d02d5f3eea98ea39fbc8b26](https://etherscan.io/address/0xdc95598ef2bbfdb66d02d5f3eea98ea39fbc8b26)                                              |
| General Permission Manager Factory:                             | [0xf0aa1856360277c60052d6095c5b787b01388cdd](https://etherscan.io/address/0xf0aa1856360277c60052d6095c5b787b01388cdd)                                              |
| CappedSTOFactory:                                               | [0x77d89663e8819023a87bfe2bc9baaa6922c0e57c](https://etherscan.io/address/0x77d89663e8819023a87bfe2bc9baaa6922c0e57c)                                              |
| USDTieredSTO Factory:                                           | [0x5a3a30bddae1f857a19b1aed93b5cdb3c3da809a](https://etherscan.io/address/0x5a3a30bddae1f857a19b1aed93b5cdb3c3da809a)                                              |
| EthDividendsCheckpointFactory:                                  | [0x968c74c52f15b2de323eca8c677f6c9266bfefd6](https://etherscan.io/address/0x968c74c52f15b2de323eca8c677f6c9266bfefd6)                                              |
| ERC20 Dividends Checkpoint Factory:                             | [0x82f9f1ab41bacb1433c79492e54bf13bccd7f9ae](https://etherscan.io/address/0x82f9f1ab41bacb1433c79492e54bf13bccd7f9ae)                                              |
| Count Transfer Manager Factory:                               | [0xd9fd7e34d6e2c47a69e02131cf8554d52c3445d5](https://etherscan.io/address/0xd9fd7e34d6e2c47a69e02131cf8554d52c3445d5)                                              |
| Percentage Transfer Manager Factory:                             | [0xe6267a9c0a227d21c95b782b1bd32bb41fc3b43b](https://etherscan.io/address/0xe6267a9c0a227d21c95b782b1bd32bb41fc3b43b)                                              |
| Manual Approval Transfer Manager Factory (2.0.1):                        | [0x6af2afad53cb334e62b90ddbdcf3a086f654c298](https://etherscan.io/address/0x6af2afad53cb334e62b90ddbdcf3a086f654c298)                                              |


New SecurityTokenRegistry (2.0.1): 0x538136ed73011a766bf0a126a27300c3a7a2e6a6
(fixed bug with getTickersByOwner())

New ModuleRegistry (2.0.1): 0xbc18f144ccf87f2d98e6fa0661799fcdc3170119
(fixed bug with missing transferOwnership function)

New ManualApprovalTransferManager 0x6af2afad53cb334e62b90ddbdcf3a086f654c298
(Fixed 0x0 from bug)

## KOVAN

### v3.0.0

| Contract                                                         | Address                                                                                                                       |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| SecurityTokenRegistry (Data Store):                                | [0x91110c2f67e2881a8540417be9eadf5bc9f2f248](https://kovan.etherscan.io/address/0x91110c2f67e2881a8540417be9eadf5bc9f2f248)                                              |
| SecurityTokenRegistry (Logic):                                | [0x71A4F01F6Dee751eEDc6E16FD25AC45b46e1b0d9](https://kovan.etherscan.io/address/0x71A4F01F6Dee751eEDc6E16FD25AC45b46e1b0d9)                                              |
| ModuleRegistry (Data Store):                                       | [0xde6d19d7a68d453244227b6ccc5d8e6c2314627a](https://kovan.etherscan.io/address/0xde6d19d7a68d453244227b6ccc5d8e6c2314627a)                                              |
| ModuleRegistry (Logic):                                       | [0xC5203791C9d46161B378deaDa89A1D5B67Ba23e3](https://kovan.etherscan.io/address/0xC5203791C9d46161B378deaDa89A1D5B67Ba23e3)                                              |
| LDFStock Registry:                                            | [0x5b215a7d39ee305ad28da29bf2f0425c6c2a00b3](https://kovan.etherscan.io/address/0x5b215a7d39ee305ad28da29bf2f0425c6c2a00b3)                                              |
| Feature Registry:                                            | [0x8967a7cfc4b455398be2356cd05cd43b7a39697e](https://kovan.etherscan.io/address/0x8967a7cfc4b455398be2356cd05cd43b7a39697e)                                              |
| ETHOracle:                                                   | [0xCE5551FC9d43E9D2CC255139169FC889352405C8](https://kovan.etherscan.io/address/0xCE5551FC9d43E9D2CC255139169FC889352405C8)                                              |
| ST-LDFOracle:                                                   | [0x461d98EF2A0c7Ac1416EF065840fF5d4C946206C](https://kovan.etherscan.io/address/0x461d98EF2A0c7Ac1416EF065840fF5d4C946206C)                                              |
| General Transfer Manager Factory:                              | [0x5D92B852c31C0dd3409285339051c7594eaE198e](https://kovan.etherscan.io/address/0x5D92B852c31C0dd3409285339051c7594eaE198e)                                              |
| General Permission Manager Factory:                             | [0x559a15fa038c3FB84e993BE06235E7D9A0D1cB7d](https://kovan.etherscan.io/address/0x559a15fa038c3FB84e993BE06235E7D9A0D1cB7d)                                              |
| CappedSTOFactory:                                               | [0x7CEa4A1Eced1a035A6BD5e673454f6Bc8c98b20E](https://kovan.etherscan.io/address/0x7CEa4A1Eced1a035A6BD5e673454f6Bc8c98b20E)                                              |
| USDTieredSTO Factory:                                           | [0x0C260C11B46827E9d96F9a5C7DDbb66907e2b0F3](https://kovan.etherscan.io/address/0x0C260C11B46827E9d96F9a5C7DDbb66907e2b0F3)                                              |
| ERC20 Dividends Checkpoint Factory:                             | [0xE74A013FbE7B6EF5F3b4B45Ce4745dCBA3197856](https://kovan.etherscan.io/address/0xE74A013FbE7B6EF5F3b4B45Ce4745dCBA3197856)                                              |
| Count Transfer Manager Factory:                               | [0xbA6893CfdDdEc76dB8a4d8f833a81F456fB64e2c](https://kovan.etherscan.io/address/0xbA6893CfdDdEc76dB8a4d8f833a81F456fB64e2c)                                              |
| Percentage Transfer Manager Factory:                             | [0x127dcA5040f5B943100D4c4154fA4F7744e9482D](https://kovan.etherscan.io/address/0x127dcA5040f5B943100D4c4154fA4F7744e9482D)                                              |
| Manual Approval Transfer Manager Factory:                        | [0xFcd05Ab2B494577AbE0a4549b2FBec6e1bce32C9](https://kovan.etherscan.io/address/0xFcd05Ab2B494577AbE0a4549b2FBec6e1bce32C9)                                              |



### v2.0.0
New Kovan ST-LDFTokenFaucet: 0xb347b9f5b56b431b2cf4e1d90a5995f7519ca792

| Contract                                                         | Address                                                                                                                       |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| SecurityTokenRegistry (Proxy):                                | [0x91110c2f67e2881a8540417be9eadf5bc9f2f248](https://kovan.etherscan.io/address/0x91110c2f67e2881a8540417be9eadf5bc9f2f248)                                              |
| ModuleRegistry (Proxy):                                       | [0xde6d19d7a68d453244227b6ccc5d8e6c2314627a](https://kovan.etherscan.io/address/0xde6d19d7a68d453244227b6ccc5d8e6c2314627a)                                              |
| LDFStock Registry:                                            | [0x5b215a7d39ee305ad28da29bf2f0425c6c2a00b3](https://kovan.etherscan.io/address/0x5b215a7d39ee305ad28da29bf2f0425c6c2a00b3)                                              |
| Feature Registry:                                            | [0x8967a7cfc4b455398be2356cd05cd43b7a39697e](https://kovan.etherscan.io/address/0x8967a7cfc4b455398be2356cd05cd43b7a39697e)                                              |
| ETHOracle:                                                   | [0x14542627196c7dab26eb11ffd8a407ffc476de76](https://kovan.etherscan.io/address/0x14542627196c7dab26eb11ffd8a407ffc476de76#readContract)                                              |
| ST-LDFOracle:                                                   | [0x461d98EF2A0c7Ac1416EF065840fF5d4C946206C](https://kovan.etherscan.io/address/0x461d98EF2A0c7Ac1416EF065840fF5d4C946206C)                                              |
| General Transfer Manager Factory:                              | [0x650e9507e983077d6f822472a7dcc37626d55c7f](https://kovan.etherscan.io/address/0x650e9507e983077d6f822472a7dcc37626d55c7f)                                              |
| General Permission Manager Factory:                             | [0xbf0bd6305b523ce055baa6dfaa9676d6b9e6090b](https://kovan.etherscan.io/address/0xbf0bd6305b523ce055baa6dfaa9676d6b9e6090b)                                              |
| CappedSTOFactory:                                               | [0x01510b2c03296473f883c12d0723f0a46aa67f13](https://kovan.etherscan.io/address/0x01510b2c03296473f883c12d0723f0a46aa67f13)                                              |
| USDTieredSTO Factory:                                           | [0x8b9743e6129f7b7cca04e3611b5c8cd9b1d11e90](https://kovan.etherscan.io/address/0x8b9743e6129f7b7cca04e3611b5c8cd9b1d11e90)                                              |
| ERC20 Dividends Checkpoint Factory:                             | [0x4369751df5bcb2f12f1790f525ef212a622b9c60](https://kovan.etherscan.io/address/0x4369751df5bcb2f12f1790f525ef212a622b9c60)                                              |
| Count Transfer Manager Factory:                               | [0xc7cf0c1ddc85c18672951f9bfeb7163ecc8f0e2f](https://kovan.etherscan.io/address/0xc7cf0c1ddc85c18672951f9bfeb7163ecc8f0e2f)                                              |
| Percentage Transfer Manager Factory:                             | [0xfea5fcb254bcb4ada0f86903ff822d6372325cb1](https://kovan.etherscan.io/address/0xfea5fcb254bcb4ada0f86903ff822d6372325cb1)                                              |
| Manual Approval Transfer Manager Factory:                        | [0x9faa79e2ccf0eb49aa6ebde1795ad2e951ce78f8](https://kovan.etherscan.io/address/0x9faa79e2ccf0eb49aa6ebde1795ad2e951ce78f8)                                              |

New ManualApprovalTransferManager 0x9faa79e2ccf0eb49aa6ebde1795ad2e951ce78f8
(Fixed 0x0 from bug)

## Package version requirements for your machine:

- node v10.16.0
- yarn v1.17.3
- Truffle v5.0.6 (core: 5.0.6)
- Solidity v0.5.8 (solc-js)



   过时

- node v8.x.x or v9.x.x
- npm v6.x.x or newer
- Yarn v1.3 or newer
- Homebrew v1.6.7 (for macOS)
- Truffle v4.1.11 (core: 4.1.11)
- Solidity v0.4.24 (solc-js)
- Ganache CLI v6.1.3 (ganache-core: 2.1.2) or newer

## Setup

The smart contracts are written in [Solidity](https://github.com/ethereum/solidity) and tested/deployed using [Truffle](https://github.com/trufflesuite/truffle) version 4.1.0. The new version of Truffle doesn't require testrpc to be installed separately so you can just run the following:

```bash
# Install Truffle package globally:
$ npm install --global truffle

# (Only for windows) set up build tools for node-gyp by running below command in powershell:
$ npm install --global --production windows-build-tools

# Install local node dependencies:
$ yarn
```

## Testing

To test the code simply run:

```bash
# on *nix systems
$ npm run test

# on windows systems
$
```

E:\LDFStock-core-master>npm run wintest

> LDFStock-core@3.0.0 wintest E:\LDFStock-core-master
> scripts\wintest.cmd

Using network 'development'.


Compiling your contracts...
===========================
> Compiling .\contracts\ModuleRegistry.sol
> Compiling .\contracts\Pausable.sol
> Compiling .\contracts\ReclaimTokens.sol
> Compiling .\contracts\STRGetter.sol
> Compiling .\contracts\SecurityTokenRegistry.sol
> Compiling .\contracts\datastore\DataStoreFactory.sol
> Compiling .\contracts\datastore\DataStoreProxy.sol
> Compiling .\contracts\datastore\DataStoreStorage.sol
> Compiling .\contracts\external\IMedianizer.sol
> Compiling .\contracts\external\oraclizeAPI.sol
> Compiling .\contracts\interfaces\ICheckPermission.sol
> Compiling .\contracts\interfaces\IDataStore.sol
> Compiling .\contracts\interfaces\IFeatureRegistry.sol
> Compiling .\contracts\interfaces\IModule.sol
> Compiling .\contracts\interfaces\IModuleFactory.sol
> Compiling .\contracts\interfaces\IModuleRegistry.sol
> Compiling .\contracts\interfaces\IOracle.sol
> Compiling .\contracts\interfaces\IOwnable.sol
> Compiling .\contracts\interfaces\IST-LDF.sol
> Compiling .\contracts\interfaces\ILDFStockRegistry.sol
> Compiling .\contracts\interfaces\ISTFactory.sol
> Compiling .\contracts\interfaces\ISTO.sol
> Compiling .\contracts\interfaces\ISecurityToken.sol
> Compiling .\contracts\interfaces\ISecurityTokenRegistry.sol
> Compiling .\contracts\interfaces\ITransferManager.sol
> Compiling .\contracts\interfaces\IUpgradableTokenFactory.sol
> Compiling .\contracts\interfaces\IVoting.sol
> Compiling .\contracts\interfaces\token\IERC1410.sol
> Compiling .\contracts\interfaces\token\IERC1594.sol
> Compiling .\contracts\interfaces\token\IERC1643.sol
> Compiling .\contracts\interfaces\token\IERC1644.sol
> Compiling .\contracts\libraries\BokkyPooBahsDateTimeLibrary.sol
> Compiling .\contracts\libraries\DecimalMath.sol
> Compiling .\contracts\libraries\Encoder.sol
> Compiling .\contracts\libraries\StatusCodes.sol
> Compiling .\contracts\libraries\TokenLib.sol
> Compiling .\contracts\libraries\Util.sol
> Compiling .\contracts\libraries\VersionUtils.sol
> Compiling .\contracts\libraries\VolumeRestrictionLib.sol
> Compiling .\contracts\mocks\Dummy\DummySTOFactory.sol
> Compiling .\contracts\mocks\Dummy\DummySTOProxy.sol
> Compiling .\contracts\mocks\Dummy\DummySTOStorage.sol
> Compiling .\contracts\mocks\MockBurnFactory.sol
> Compiling .\contracts\mocks\MockRedemptionManager.sol
> Compiling .\contracts\modules\Burn\IBurn.sol
> Compiling .\contracts\modules\Checkpoint\Dividend\DividendCheckpoint.sol
> Compiling .\contracts\modules\Checkpoint\Dividend\ERC20\ERC20DividendCheckpointProxy.sol
> Compiling .\contracts\modules\Checkpoint\Dividend\ERC20\ERC20DividendCheckpointStorage.sol
> Compiling .\contracts\modules\Checkpoint\Dividend\Ether\EtherDividendCheckpointProxy.sol
> Compiling .\contracts\modules\Checkpoint\ICheckpoint.sol
> Compiling .\contracts\modules\Checkpoint\Voting\PLCR\PLCRVotingCheckpointProxy.sol
> Compiling .\contracts\modules\Checkpoint\Voting\PLCR\PLCRVotingCheckpointStorage.sol
> Compiling .\contracts\modules\Checkpoint\Voting\Transparent\WeightedVoteCheckpointProxy.sol
> Compiling .\contracts\modules\Checkpoint\Voting\Transparent\WeightedVoteCheckpointStorage.sol
> Compiling .\contracts\modules\Checkpoint\Voting\VotingCheckpoint.sol
> Compiling .\contracts\modules\Experimental\Burn\TrackedRedemption.sol
> Compiling .\contracts\modules\Experimental\Burn\TrackedRedemptionFactory.sol
> Compiling .\contracts\modules\Experimental\Mixed\ScheduledCheckpoint.sol
> Compiling .\contracts\modules\Experimental\TransferManager\KYCTransferManager.sol
> Compiling .\contracts\modules\Experimental\TransferManager\SignedTransferManager.sol
> Compiling .\contracts\modules\Module.sol
> Compiling .\contracts\modules\ModuleFactory.sol
> Compiling .\contracts\modules\PermissionManager\GeneralPermissionManagerProxy.sol
> Compiling .\contracts\modules\PermissionManager\GeneralPermissionManagerStorage.sol
> Compiling .\contracts\modules\PermissionManager\IPermissionManager.sol
> Compiling .\contracts\modules\STO\Capped\CappedSTOProxy.sol
> Compiling .\contracts\modules\STO\Capped\CappedSTOStorage.sol
> Compiling .\contracts\modules\STO\PreSale\PreSaleSTOProxy.sol
> Compiling .\contracts\modules\STO\PreSale\PreSaleSTOStorage.sol
> Compiling .\contracts\modules\STO\STO.sol
> Compiling .\contracts\modules\STO\USDTiered\USDTieredSTOProxy.sol
> Compiling .\contracts\modules\STO\USDTiered\USDTieredSTOStorage.sol
> Compiling .\contracts\modules\TransferManager\BTM\BlacklistTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\BTM\BlacklistTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\CTM\CountTransferManager.sol
> Compiling .\contracts\modules\TransferManager\CTM\CountTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\CTM\CountTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\GTM\GeneralTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\GTM\GeneralTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\LTM\LockUpTransferManager.sol
> Compiling .\contracts\modules\TransferManager\LTM\LockUpTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\LTM\LockUpTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\MATM\ManualApprovalTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\MATM\ManualApprovalTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\PTM\PercentageTransferManagerProxy.sol
> Compiling .\contracts\modules\TransferManager\PTM\PercentageTransferManagerStorage.sol
> Compiling .\contracts\modules\TransferManager\TransferManager.sol
> Compiling .\contracts\modules\TransferManager\VRTM\VolumeRestrictionTMProxy.sol
> Compiling .\contracts\modules\TransferManager\VRTM\VolumeRestrictionTMStorage.sol
> Compiling .\contracts\modules\UpgradableModuleFactory.sol
> Compiling .\contracts\modules\Wallet\VestingEscrowWalletProxy.sol
> Compiling .\contracts\modules\Wallet\VestingEscrowWalletStorage.sol
> Compiling .\contracts\modules\Wallet\Wallet.sol
> Compiling .\contracts\oracles\ST-LDFOracle.sol
> Compiling .\contracts\proxy\OwnedUpgradeabilityProxy.sol
> Compiling .\contracts\proxy\Proxy.sol
> Compiling .\contracts\proxy\UpgradeabilityProxy.sol
> Compiling .\contracts\storage\EternalStorage.sol
> Compiling .\contracts\storage\modules\Checkpoint\Dividend\DividendCheckpointStorage.sol
> Compiling .\contracts\storage\modules\Checkpoint\Voting\VotingCheckpointStorage.sol
> Compiling .\contracts\storage\modules\ModuleStorage.sol
> Compiling .\contracts\storage\modules\STO\STOStorage.sol
> Compiling .\contracts\tokens\OZStorage.sol
> Compiling .\contracts\tokens\STGetter.sol
> Compiling .\contracts\tokens\SecurityToken.sol
> Compiling .\contracts\tokens\SecurityTokenProxy.sol
> Compiling .\contracts\tokens\SecurityTokenStorage.sol
> Compilation warnings encountered:

    /E/LDFStock-core-master/contracts/external/oraclizeAPI.sol:320:7: Warning: Unreachable code.
      _networkID; // silence the warning and remain backwards compatible
      ^--------^
,/E/LDFStock-core-master/contracts/external/oraclizeAPI.sol:373:7: Warning: Unreachable code.
      _myid; _result; _proof; // Silence compiler warnings
      ^--------------------^
,/E/LDFStock-core-master/contracts/external/oraclizeAPI.sol:371:5: Warning: Function state mutability can be restricted to pure
    function __callback(bytes32 _myid, string memory _result, bytes memory _proof) public {
    ^ (Relevant source part starts here and spans across multiple lines).

> Artifacts written to C:\Users\Think\AppData\Local\Temp\test--22972-NABM27CnxRqS
> Compiled successfully using:
>
>  - solc: 0.5.8+commit.23d335f2.Emscripten.clang





    ----------------------- LDFStock Network Smart Contracts: -----------------------
    LDFStockRegistry:                     0xB86429c03a49bCDb3e87d5D921e17f4dfFf3F505
    SecurityTokenRegistry (Proxy):        0x502f3D2F9E0E81453B92F43a99BE089ef59773F1
    ModuleRegistry (Proxy):               0x6a3bf6B1158Aa7C2ebf3D5D66f49Fd0730bb1EBd
    FeatureRegistry:                      0xc5Eb9C9caf9AA9Bcf643B512Bd4C3e471EDa12D2
    STRGetter:                            0x77E5dce9bC308F72F1a3C7F6217Ca17Be3A35A72

    ETHOracle:                            0x640396E4D59987Cf2a96edff78a3257C158b9617
    ST-LDFOracle:                           0xe4573B7c3C2FbE5d42e9af2fc1e8017DFC0A793E
    ST-LDFStableOracle:                     0x9dd3De5A5A0276DA03687051c8e7bf0CD6A9A60b

    STFactory:                            0x57eef002F8152a9314380CCD4F3E789abB382A77
    GeneralTransferManagerLogic:          0x49233d6a0972813951Ac4e9D4a5b7135D6D91c7f
    GeneralTransferManagerFactory:        0x0aA2b29bCb4c8602f017fADe296d695D44004963
    GeneralPermissionManagerLogic:        0x809f5155feaFA33B5fEDdF8B3385404170f2b54b
    GeneralPermissionManagerFactory:      0xb14de837935d83eF5e32FFAC68ac1fFa3f209fE0

    CappedSTOLogic:                       0x3F59dF273e701e4655110196B923Ce66769cE832
    CappedSTOFactory:                     0x74D946fe64511a119F9fE175C3b0ccEC997d16CA
    USDTieredSTOLogic:                    0x5FE18f4bF95f8DE29Ad99816CDC73f170ffC9F33
    USDTieredSTOFactory:                  0x61cB8918e236A0eE86280B2aBe8d0dbAE99082b1

    CountTransferManagerLogic:            0x35354f3A4463BA8F1D323114332D49C0448013E7
    CountTransferManagerFactory:          0x39DA5eaA5C3399BD2EB99144A68437eB0BdbC052
    PercentageTransferManagerLogic:       0x5948E962cd76b5Fe5f71d1B72F4F58f544A9f8f0
    PercentageTransferManagerFactory:     0xA8e0788CB4096d5E5327D96E897f54CdF0e3b6e3
    ManualApprovalTransferManagerLogic:   0xe46618566FD3523177A654C57fd6B7CF264A941c
    ManualApprovalTransferManagerFactory: 0x080127cf6270358652b4D053c9E5d775A065f766
    EtherDividendCheckpointLogic:         0x177CAD8655c3F4E1a4Fc74072E9fCb3FD5eca544
    ERC20DividendCheckpointLogic:         0x646ED597303519Ff1478DD098Fb118af76F70e20
    EtherDividendCheckpointFactory:       0x24B05135809a48301520F291648C04AE0D451591
    ERC20DividendCheckpointFactory:       0xA34697483c06E345Ddae050407c53ED01367ffE5
    VolumeRestrictionTMFactory:           0xF835d8f6B435ADD6e193554258F837B66bD38e18
    VolumeRestrictionTMLogic:             0xea3C2A95F4321D5944dBe6A8af423adAC1E4E98F
    VestingEscrowWalletFactory:           0xBAf596842288A0F83a2371C21cbEE0A8f93cE653
    VestingEscrowWalletLogic:             0x4406993f172e1D013d0d8a059D4282823Ea22e93
    ---------------------------------------------------------------------------------














# Extending LDFStock Core

1. Deploy `ModuleRegistry`. `ModuleRegistry` keeps track of all available modules that add new functionalities to LDFStock-based security tokens.

  部署`ModuleRegistry。ModuleRegistry`跟踪所有为基于LDFStock的数字证券添加新功能的可用模块

2. Deploy `GeneralTransferManagerFactory`. This module allows the use of a general `TransferManager` for newly issued security tokens. The General Transfer Manager gives STs the ability to have their transfers restricted by using an on-chain whitelist.

   部署此`GeneralTransferManagerFactory`模块。允许将`GeneralTransferManagerFactory`用于新发行的数字证券。 通用`TransferManager`使数字证券可以使用链上白名单来限制其交换。

3. Add the `GeneralTransferManagerFactory` module to `ModuleRegistry` by calling `ModuleRegistry.registerModule()`.

   增加`GeneralTransferManagerFactory`到`ModuleRegistry`调用`ModuleRegistry.registerModule()`

4. Deploy `TickerRegistry`. This contract handles the registration of unique token symbols. Issuers first have to claim their token symbol through the `TickerRegistry`. If it's available they will be able to deploy a ST with the same symbol for a set number of days before the registration expires.

   部署TickerRegistry`。该合同处理唯一 token 符号的注册。 发行者首先必须通过TickerRegistry声明其 token 符号。 如果可用，他们将能够在注册到期之前的指定天数内部署带有相同符号的数字证券。

5. Deploy SecurityTokenRegistry. This contract is responsible for deploying new Security Tokens. STs should always be deployed by using the SecurityTokenRegistry.

   部署SecurityTokenRegistry。该合约负责部署新的数字证券。 应始终使用SecurityTokenRegistry部署数字证券

## Deploying Security Token Offerings (Network Admin Only)  部署数字证券产品

Security Token Offerings (STOs) grant STs the ability to be distributed in an initial offering. LDFStock offers a few out-of-the-box STO models for issuers to select from and, as the platform evolves, 3rd party developers will be able to create their own offerings and make them available to the network.

数字证券产品（STO）使ST拥有在初始产品中分发的能力。 LDFStock提供了一些现成的STO模型供发行人选择，随着平台的发展，第三方开发人员将能够创建自己的产品并将其提供给网络。

As an example, we've included a `CappedSTO` and `CappedSTOFactory` contracts.

例如，我们包含了CappedSTO和CappedSTOFactory合约。

In order to create a new STO, developers first have to create an STO Factory contract which will be responsible for instantiating STOs as Issuers select them. Each STO Factory has an STO contract attached to it, which will be instantiated for each Security Token that wants to use that particular STO.

为了创建新的STO，开发人员首先必须创建一个STO Factory合约，该合约将负责在发行人选择STO时实例化STO。 每个STO Factory都附带有一个STO合约，该合同将针对要使用该特定STO的每个数字证券实例化。

To make an STO available for Issuers, first, deploy the STO Factory and take note of its address. Then, call `moduleRegistry.registerModule(STO Factory address);`

发行人要发行STO，首先，部署STO Factory并记下其地址（STO Factory address）。然后调用`moduleRegistry.registerModule(STO Factory address);`

Once the STO Factory has been registered to the Module Registry, issuers will be able to see it on the LDFStock dApp and they will be able to add it as a module of the ST.

Note that while anyone can register an STO Factory, only those "approved" by LDFStock will be enabled to be attached by the general community. An STO Factory not yet approved by LDFStock may only be used by it's author.

一旦将STO Factory注册到Module Registry表中，发行者就可以在LDFStock dApp上看到它，并且可以将其添加为ST的模块。

请注意，尽管任何人都可以注册STO Factory，但只有LDFStock“批准”的人，才能被附加general community。 未经LDFStock批准的STO Factory只能由其作者使用。


# Code Styleguide

The LDFStock-core repo follows the [Solidity style guide](http://solidity.readthedocs.io/en/develop/style-guide.html).

# Links

- [LDFStock Website](https://LDFStock.network)
- [Ethereum Project](https://www.ethereum.org/)
- [Solidity Docs](https://solidity.readthedocs.io/en/develop/)
- [Truffle Framework](http://truffleframework.com/)
- [Ganache CLI / TestRPC](https://github.com/trufflesuite/ganache-cli)
