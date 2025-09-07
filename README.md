[English](https://github.com/touyi/solana-onchain-arbitrage-bot/blob/main/assets/en_readme.md)

# solana-onchain-arbitrage-bot

支持智能路由，计算最优套利路线
solana onchain arbitrage bot, supports intelligent routing and calculates optimal arbitrage routes

# 📝基本介绍

本仓库是基于合约智能路由的套利机器人，分为客户端和合约端，本仓库是客户端，主要功能：

* 获取需要套利的交易池
* 自动配对交易池，并且组建交易提交

对于合约端（合约地址：DxeQQ7PQ94j26ism5ivTqNHAkteFNmgRpqYx7XQFqs9Z）

* 接收客户端提交的交易池
* 自动计算最优套利路径以及最优输入（如有），并发起套利操作，否则交易返回失败

目前已经支持的交易池，后续会持续新增

* PumpSwap
* Raydium AMM
* Raydium CPMM
* Raydium CLMM
* Meteora DLMM
* Meteora Dynamic Pools

> **使用本套利机器人对于成功套利的交易，收取利润的10%作为手续费，如果套利失败，或者无利润，本合约不收取任何手续费。**
> 例如套利 输入0.5 sol，输出0.6 sol，利润就是0.1sol，手续费为0.1 * 10% = 0.01 sol

> 合约端：https://github.com/touyi/solana-onchain-arbitrage-bot-contract

# 🚀快速开始

## 安装环境

> 目前只支持一键linux系统安装

根目录执行：

```
bash install.sh
```

## 基本配置

solana-onchain-arbitrage-bot/src/config.js

config.js 中的以下配置必须填写，screctKey和screctKeyBase58填写一个即可
### 密钥
```
"base": {
        // rpc 请求url
        "rpcUrl": "https://api.mainnet-beta.solana.com", 
        // 钱包私钥,32个数字 screctKey和screctKeyBase58 2个填一个即可
        "screctKey": [], 
        // 钱包私钥base58编码字符串 screctKey和screctKeyBase58 2个填一个即可
        "screctKeyBase58": "", 
    },
```
### 设置监听的mint
在配置中添加需要监听的mint，会自动拉取mint相关的pump raydium Meteora的pool，用于套利
```json
// 需要监听套利的mint列表，自己任意修改，
// 可以直接去爬gmgn的热榜数据：https://gmgn.ai/?chain=sol&1ren=0&1fr=1&1bu=0&1di=0&0fr=0
"mintList": [
"B5boCskXEFb1RJJ9EqJNV3gt5fjhk85DeD7BgbLcpump",
...
],
```

## 转换wsol

dex一般都是直接使用wsol来交易，所以在启动交易的时候需要先在账户中转换好等量的wsol，使用仓库中提供的脚本进行转换：
在src/tools目录下执行：`node warp_sol.js --amount=1000000000`
其中amount是lamport单位的sol数量
当需要关闭wsol退回sol是执行`node warp_sol.js --amount=1000000000 --close`
> 需要保证转换的数量需要大于config.js 配置中的maxInputAmount 配置字段，默认配置`1000000000`即1WSOL




## 启动运行

在src目录下执行：
```bash
node main.js
```
启动后看到如下日志输出：
```js
🚀 ~ swap::e: SendTransactionError: Simulation failed. 
Catch the `SendTransactionError` and call `getLogs()` on it for full details.
    at Connection.sendEncodedTransaction (/home/touyi/sol/solana-onchain-arbitrage-bot/src/node_modules/@solana/web3.js/lib/index.cjs.js:8206:13)
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
    at async Connection.sendRawTransaction (/home/touyi/sol/solana-onchain-arbitrage-bot/src/node_modules/@solana/web3.js/lib/index.cjs.js:8171:20)
    at async file:///home/touyi/sol/solana-onchain-arbitrage-bot/src/bot/arb_bot.js:237:43 {
  signature: '',
  transactionMessage: 'Transaction simulation failed: Error processing Instruction 2: custom program error: 0x1772',
  transactionLogs: [
    'Program ComputeBudget111111111111111111111111111111 invoke [1]',
    'Program ComputeBudget111111111111111111111111111111 success',
    'Program ComputeBudget111111111111111111111111111111 invoke [1]',
    'Program ComputeBudget111111111111111111111111111111 success',
    'Program 3SmBMUQe5QUpLc7wMrm97CRs3kXBSFtMZvPw8CDwZvUi invoke [1]',
    'Program log: Instruction: ArbProcess32Account',
    'Program DxeQQ7PQ94j26ism5ivTqNHAkteFNmgRpqYx7XQFqs9Z invoke [2]',
    'Program log: Instruction: ArbProcess32Account',
    'Program log: max_in 1000000000, min_profit 100， market_type [0, 0], market_flag [0, 0]',
    'Program log: sol_log_compute_units programs/arb_touyi/src/processor.rs:550',
    'Program consumption: 257040 units remaining',
    'Program log: sol_log_compute_units programs/arb_touyi/src/processor.rs:20',
    'Program consumption: 250388 units remaining',
    'Program log: iy:0 dx:0 oy:0',
    'Program log: iy:0 dx:0 oy:0',
    'Program log: AnchorError thrown in programs/arb_touyi/src/processor.rs:48. Error Code: NoProfit. Error Number: 6002. Error Message: Not Profit.',
    'Program DxeQQ7PQ94j26ism5ivTqNHAkteFNmgRpqYx7XQFqs9Z consumed 25565 of 268555 compute units',
    'Program DxeQQ7PQ94j26ism5ivTqNHAkteFNmgRpqYx7XQFqs9Z failed: custom program error: 0x1772',
    'Program 3SmBMUQe5QUpLc7wMrm97CRs3kXBSFtMZvPw8CDwZvUi consumed 56710 of 299700 compute units',
    'Program 3SmBMUQe5QUpLc7wMrm97CRs3kXBSFtMZvPw8CDwZvUi failed: custom program error: 0x1772'
  ]
}
```
其中`Error Message: Not Profit.`，表示已经开始正常运行，这里之所以有`Not Profit`报错是因为在仿真交易的时候就已经发现没有利润可套，于是不会实际发送这个交易，只有仿真成功的时候才会实际发送交易。
如果期望所有交易都上链，可以配置bot: `"skipPreflight": false,`，配置后所有交易都会上链，但是注意，上链后即使交易失败，也会损失gas

> **注意**：套利的用户中间代币账户（例如trump）如果不存在，机器人会自动创建，无需手动创建；如果存在，请确保该账户余额为空

# 🌟套利原理

对于任意两个交易池，如果存在价格差，则可能通过套利来获取利润，假设：
  * 在交易池1中使用a个SOL买入b个代币X
  * 在交易池2中使用b个代币X卖出得到c个SOL
  * 可能的利润：p = c - a


如果p大于0，则可以进行套利，否则无法套利

对于多个交易池的套利，需要在交易池中提前计算出最优套利路线，然后按照最优路线进行套利。

限制于合约算力，目前只支持一跳最优路径计算，即：SOL->X->SOL, 合约会对输入的所以交易池两两配对，计算出最优套利的配对路径，然后按照最优路径进行套利。如果所有路径都无法套利，则不会进行套利。并且合约会报错，使得交易失败


# 📅如何信任？

因为合约代码并不开源，所以让人担心的问题就是：
* 我的私钥会不会被上传？
* 你会不会在合约里面把我的代币全部转走?

为了取得信任，我们从以下几个方面来保证不会出现上述情况：

**对于第一个问题**：客户端代码完全开源，你可以看到客户端代码中，私钥只用于本地签名交易，不会上传发送给任何第三方，也不会被合约读取到。

**对于第二个问题**：套利合约代码并不开源，所以在不加防护的情况下合约拥有者确实可以做到这一点，因此我们开源了一份[防损失合约](https://github.com/touyi/guard_contract)代码仓库，其原理如下：

在套利合约当中，通过客户端代码我们可以看到输入套利合约的用户token账户只包含
* sol账户（signer默认携带）
* wsol账户（userTokenBase）
* 套利的中间代币账户（tokenPair0UserTokenAccountX）

在套利合约当中，只可能做到转移这三个账户的余额。

**“套利的中间代币账户”** 因为需要保证余额为空，所以是没有的。

对于SOL和WSOL，在[防损失合约](https://github.com/touyi/guard_contract)中，会确保套利合约调用后WSOL+SOL的数量一定大于调用前的数量，否则合约失败，整个交易回滚。以此保证不会在套利合约中出现损失。具体实现参考[防损失合约](https://github.com/touyi/guard_contract)代码

## 防损失合约使用
### 使用公开已部署的防损失合约
合约地址：`3SmBMUQe5QUpLc7wMrm97CRs3kXBSFtMZvPw8CDwZvUi`

在配中配置合约idl路径，目前在本代码库中idl下的`guard_contract.json`有一份已部署的防损失合约的idl，可以直接将其配置到config.js中使用：
```json
"base": {
        // rpc 请求url
        "rpcUrl": "", 
        // 钱包私钥,32个数字 screctKey和screctKeyBase58 2个填一个即可
        "screctKey": [], 
        // 钱包私钥base58编码字符串 screctKey和screctKeyBase58 2个填一个即可
        "screctKeyBase58": "", 
        // 如果使用防损失合约提交，请填写防损失合约IDL文件路径
        "guardContractIDL": "../idl/guard.json",
    },
```
### 部署自己的防损失合约
部署参考：[防损失合约](https://github.com/touyi/guard_contract)

部署成功后配置和上面一样，idl路径需要是你自己部署的防损失合约的idl路径。


# 💰高级用法（自定义合约调用）

面向具有一定编码能力人群，可以自己实现客户端，实现自己的交易组装和发送币策略并提交套利交易。

## 套利合约的输入定义：
### Account输入
```rust

#[derive(Accounts)]
pub struct CommonAccountsInfo32<'info> {
    /// CHECK: NONE
    pub user: Signer<'info>, // 钱包地址
    #[account(mut)]
    /// CHECK: NONE
    pub user_token_base: UncheckedAccount<'info>, // 用户的wsol账户
    #[account(mut)]
    /// CHECK: NONE
    pub token_base_mint: UncheckedAccount<'info>, // wsol的mint地址: SOL111111111111111111111111111111111111111112
    
    #[account(mut)]
    /// CHECK: NONE
    pub token_program: UncheckedAccount<'info>, // 固定：TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA

    #[account(address = anchor_lang::solana_program::system_program::ID)]
    /// CHECK: NONE
    pub sys_program: UncheckedAccount<'info>, // 固定：11111111111111111111111111111111

    #[account(mut)]
    /// CHECK: NONE
    pub token_pair_0_user_token_account_x: Option<UncheckedAccount<'info>>, // 用法套利中间代币账户
    #[account(mut)]
    /// CHECK: NONE
    pub token_pair_0_mint_x: Option<UncheckedAccount<'info>>, // 中间代币mint地址

    #[account(mut, address = RECIPIENT_PUBKEY)]
    /// CHECK: NONE
    pub recipient: AccountInfo<'info>, // 合约手续费接收账户，固定：B2kcKQCZUWvK59w9V9n7oDiFwqrh5FowymgpsKZV5NHu

    // 以下是交易池账户，最多支持29个账户
    #[account(mut)]
    /// CHECK: NONE
    pub account_0: Option<UncheckedAccount<'info>>,
    #[account(mut)]
    /// CHECK: NONE
    pub account_1: Option<UncheckedAccount<'info>>,
    #[account(mut)]
    /// CHECK: NONE
    pub account_2: Option<UncheckedAccount<'info>>,
    ......
    ......
    #[account(mut)]
    /// CHECK: NONE
    pub account_27: Option<UncheckedAccount<'info>>,
    #[account(mut)]
    /// CHECK: NONE
    pub account_28: Option<UncheckedAccount<'info>>,
}

```
包含两部分：
 
* 基本账户（accounts）

这部分账户按照上述代码填写即可

* 交易池账户

交易池账户是按照一定的规则填写，下面以pumpswap举例说明如何填写交易池账户：

![image](https://github.com/touyi/solana-onchain-arbitrage-bot/blob/main/assets/pump.png)

对于每个交易池，如果要调用cpi并完成swap操作，交易池是需要一些输入账户的，如上图可以考到sell指令需要19 + 1=20个账户（+1是pumpswap Program的账户），那么在套利合约的输入中，就需要需要预留20个账户（account_0 - account_19）用于合约填充交易池的输入账户。

实际在实现的时候其实可以发现这里很多账户都是一些通用账户，例如user这些，所以这些实际都是填充在基本账户里面，交易池账户只填充一些特殊于当前交易的池子账户，例如Pool等，具体不同的交易池可以参考`solana-onchain-arbitrage-bot/src/pool_fetcher/` 目录下的实现不同池子fetcher实现，例如pumpswap的fetcher实现：
```js
    async getFillaccounts() {
        let input_accounts = []
        input_accounts.push(this.pool_program);
        input_accounts.push(this.pool_key);
        input_accounts.push(this.global_config);
        input_accounts.push(this.base_mint_pool);
        input_accounts.push(this.quote_mint_pool);
        input_accounts.push(this.protocol_receiver);
        input_accounts.push(this.protocol_receiver_account);
        input_accounts.push(ASSOCIATED_TOKEN_PROGRAM_ID);
        input_accounts.push(this.authorith);
        input_accounts.push(this.coin_creator_vault_ata);
        input_accounts.push(this.coin_creator_auth);
        
        return input_accounts;
    }
```
对于每个池子，只需要按照这个顺序填充对应的accounts即可，多个交易池子按照顺序填充套利合约的`account_xxx` 即可。

### 参数输入

上部分说明了如何填充交易池账户，但是一个交易中多个池子都填充在`account_xxx`中，套利合约如何区分这些不同池子，以及某一段account账户是什么类型池子呢？这里就需要额外的参数信息输入来辅助了，目前指令入口定义的参数见下面：

```rust
pub fn arb_process_32_account(
        ctx: Context<CommonAccountsInfo32>,
        max_in: u64,
        min_profit: u64,
        market_type: Vec<u8>,
        market_flag: Vec<u8>
    );
```
包含4个：
* max_in：最大用于购买的wsol数量, 单位lamports, 限制最大输入，实际输入通过套利路径最优计算得到
* min_profit：最小利润, 单位lamports,如果交易最终利润小于该值，交易会报错
* market_type：交易池类型的数组，用于描述交易池账户部分的`account_xx'按照顺序都有哪些交易池类型，目前不同值代表：
  * 0：meteora DLMM
  * 1：raydium AMM
  * 2：pumpswap
  * 3：raydium CLMM
  * 4：raydium CPMM
  * 5：meteora Dynamic Pools
* market_flag：交易池处理信息：这个字段是一个u8数组，每个u8代表一个交易池的处理信息，目前只使用了最高位，即`(1 << 7) & flag`,这个为1，表示对应位置的交易池是**翻转池**，**翻转池**的定义见下面。

### 翻转池

翻转池的使用是为了方便统一抽象，对于所有的交易池，池中都是一个交易对，即2个交易token，例如对于pump的这个交易对5wNu5QhdpRGrL37ffcd6TMMqZugQgxwafgz477rShtHy，在交易池的pool信息中一般都会记录这两个token的mint，我们按照记录的先后顺序，对第一个token叫做Token_X，第二个叫做Token_Y，如下：

![image](https://github.com/touyi/solana-onchain-arbitrage-bot/blob/main/assets/Snipaste_2025-05-18_21-37-06.png)

Token_X是`Ce2gx9KGXJ6C9Mp5b5x1sn9Mg87JwEbrQby4Zqo3pump`

Token_Y是`So11111111111111111111111111111111111111112`

因为交易中我们目前只支持WSOL的套利，所以需要保证每个交易池的Token_Y是WSOL，如果不是，我们就需要对这个交易池进行翻转（客户端构造accounts的时候不翻转，只是额外传入market_flag表示这个池子的Token_Y不是WSOL，会在合约中处理）

如果有更多疑问或bug反馈欢迎加入交流群讨论：https://t.me/+t3Gexbnw0rs5NWQ1

# 📌后续计划
* rust重构（doing）
* jito 发送
* Kamino
* 高热币监控


# 📜代码分析

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/touyi/solana-onchain-arbitrage-bot)



