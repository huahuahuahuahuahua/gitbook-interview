# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis# EOS

* [一种 EOSIO 合约随机数的实现](https://bbs.chainon.io/d/3083)

## Attack

* [EOS 新型攻击手法之 hard_fail 状态攻击](https://bbs.chainon.io/d/3081)
* [EOS 回滚攻击手法分析之黑名单篇](https://paper.seebug.org/773/)
* [EOS 回滚攻击手法分析之重放篇](https://paper.seebug.org/773/)

https://github.com/slowmist/eos-smart-contract-security-best-practices
https://github.com/slowmist/eos-bp-nodes-security-checklist
https://github.com/Lianantech/EOS-Security# Attack
+ https://github.com/Lianantech/ETH-Security
+ https://github.com/slowmist/Knowledge-Base

## rpc
+ https://paper.seebug.org/547/
+ https://paper.seebug.org/673/
+ https://paper.seebug.org/656/

## P2P 协议的异形攻击漏洞
+ https://paper.seebug.org/895/

## 以太坊漏洞可导致“重入攻击”风险？
+ https://paper.seebug.org/801/

## Smart contracts
+ https://github.com/Lianantech/Game-Contract-Security
+ https://ethfans.org/posts/comprehensive-list-of-common-attacks-and-defense-part-8
+ https://paper.seebug.org/category/blockchain/
    * __TBD__

<!-- 
+ https://paper.seebug.org/790/#51
+ https://paper.seebug.org/745/
+ https://paper.seebug.org/754/
+ https://paper.seebug.org/741/
+ https://paper.seebug.org/732/
+ https://paper.seebug.org/707/
+ https://paper.seebug.org/696/
+ https://paper.seebug.org/679/
+ https://paper.seebug.org/663/
+ https://paper.seebug.org/661/

+ https://paper.seebug.org/685/
+ https://paper.seebug.org/632/

+ https://paper.seebug.org/633/
+ https://paper.seebug.org/627/
+ https://paper.seebug.org/626/
+ https://paper.seebug.org/630/
+ https://paper.seebug.org/624/

+ https://paper.seebug.org/603/
+ https://paper.seebug.org/601/
+ https://paper.seebug.org/607/

+ https://paper.seebug.org/615/
+ https://paper.seebug.org/621/
+ https://paper.seebug.org/612/

+ https://paper.seebug.org/592/
 -->

## short address
+ https://paper.seebug.org/545/

## honey pots
+ https://paper.seebug.org/631/

## analysis
+ https://paper.seebug.org/717/
+ https://paper.seebug.org/684/
+ https://paper.seebug.org/672/# Curriculum

+ https://github.com/chaincodelabs/bitcoin-curriculum
+ https://github.com/bitcoinbook/bitcoinbook
+ https://github.com/jimmysong/programmingbitcoin
+ https://github.com/ProgrammingBlockchain/ProgrammingBlockchain
+ https://github.com/teachbitcoin/
+ https://github.com/jonatack/bitcoin-development
+ https://github.com/jashmenn/bitcoin-reading-list
+ https://github.com/ScottWorks/ConsenSys-Academy-Notes
+ https://github.com/rustielin/Blockchain-Notes# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

检查 policy 之前会先检查 consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）
+ 比特币单位
    * bitcoin，BTC，1比特币
    * centi-bitcoin，cBTC，1分比特币，百分之一比特币（之前最常用）
    * milli-bitcoin，mBTC，1毫比特币，千分之一比特币（偶尔用）
    * micro-bitcoin，μBTC，1微比特币，1^(-6)比特币（最常用）
    * satoshi，sat，1聪比特币，一亿分之一比特币（最小单位）
+ 每4年发行新比特币的比例降低一半
+ 2140年将达到2100w


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto,[“Bitcoin：A Peer-to-Peer  Electronic  Cash    System”](https://bitcoin.org/bitcoin.pdf)
    * 2009, 比特币网络运行
        - 北京时间2009年1月4日02:15:05 Genesis block 创世区块
            + 《泰晤士报》当天头版文章标题
            ```
            The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
            ```

## nodes
+ https://bitnodes.earn.com/
    * https://bitnodes.earn.com/nodes/live-map/
    * https://bitnodes.earn.com/nodes/?q=China

## Network status
+ [Bitcoin Optech Dashboards](https://dashboard.bitcoinops.org/)
+ [Johoe’s mempool statistics](https://jochen-hoenicke.de/queue/#0,1w)
+ [P2SH.info](https://p2sh.info/)
+ [BitcoinStats](http://bitcoinstats.com/network/propagation/)


## 设计思想
+ 为什么 block 设计为 1M？
    * 在比特币诞生之初，比特币的发明者中本聪并没有特意限制区块的大小，区块大小在其自身数据结构的控制下最大可以达到 32MB
    * 在比特币早期，有人恶意制造的大量小额转账使网络中有大量的待确认交易，导致正常的比特币转账不能被确认，确认时间被延迟，影响网络正常运转(__DDoS__)。于是中本聪将比特币的区块大小暂定为1M
    * _在比特币白皮书的第7章，中本聪就明确提出了在比特币容量不够用的时候应该怎样进行扩容。白皮书发布之后，他自己在社区留言的第一个问题，就指出了比特币未来的扩容隐患。_
    * [比特币扩容之争](#%E6%AF%94%E7%89%B9%E5%B8%81%E6%89%A9%E5%AE%B9%E4%B9%8B%E4%BA%89)
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么交易需要6次确认，coinbase 却需要 100 次才能成熟？
    * https://bitcoin.stackexchange.com/questions/1991/what-is-the-block-maturation-time
    * https://bitcoin.stackexchange.com/questions/40655/coinbase-transactions-100-block-cooldown-period
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？
+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?
+ 为什么需要 merkle tree？
+ 为什么用 ECDSA 而不是 RSA？
+ 为什么 double hash？ 
    * 长度不够可能多0导致碰撞
+ [为什么tx&block hashes用 little endian，而不是 big endian（network order）？ ](https://bitcoin.stackexchange.com/questions/2063/why-does-the-bitcoin-protocol-use-the-little-endian-notation)
    * 比特币的区块链要靠HASH值来标识区块，这就要求所有系统（不管是什么架构），存储的数据必须每个字节（包括顺序）都完全一致，因此必须规定出一种统一的字节顺序才行。哪一种都可以，但必须选一个。现代计算机 __几乎都是使用little endian__ ，所以内置的数据类型首选little endian。
        - Bitcoin transaction and block hashes are considered little-endian when treating them as integers.
    * Hashes are __defined by the standards as being big-endian__, and crypto libraries deal with them in that form, so hashes are transmitted in big-endian. 
        - On the network they are just 32-byte sequences in the order they are generated by the hash function.
        - blocks show up in big-endian form, and itz the way we humans write numbers down.
        - block hashes in Bitcoin are treated as numbers for the purposes of difficulty calculations, meaning the hash must be below certain threshold in order to pass. It is natural for a human to expect such a number to be displayed on screen with leading zeroes. 

## 图灵完备
图灵完备指的是可以描述一系列操作来描述所有可计算问题。

比特币不是图灵完备的，因为它没有条件判断语句，不能执行循环，也不会产生递归。

## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## fast sync
Gregory Maxwell, who [implemented the assumed valid feature](https://github.com/bitcoin/bitcoin/issues/9484), 跳过 the assumed valid block 之前的  script evaluation，可以使新用户减少 80% 的同步时间。用户仍然要计算 utxo 集来验证币的所有权。

James O’Beirne 在 Bitcoin-Dev mailing list 发了个 [帖子](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2019-April/016825.html) 讨论要不要连区块链信息包括 uxto 集也直接下载了。好处是 可以把同步时间减少 95% 甚至更多，坏处是随着区块越来越大，新用户没法 trustlessly verify Bitcoin’s UTXO state，只能信任已有用户。


## Verification
> 比特币节点如何验证一个区块 (适用于 BTC/BCH-ABC/BCH-SV)

当一个节点通过p2p网络获得一个新区块时，都会验证这个区块是不是有效 (CheckBlock 函数) :

+ 验证工作量证明，即验证区块头的哈希值小于当前目标值。
+ 验证 Merkle Root 是否是由区块体中的交易得到
    * 重构区块 Merkle 树得到的树根，看是否和区块头中的 hashMerkleRoot 值相等
+ 验证区块大小是否在设定范围之内
    * BTC 数据区块体不能大于 1M，隔离验证区块不能大于 3M
    * BCH-ABC 区块不能大于 32M
    * BCH-SV 现在是不能大于 128M
+ 验证是否只有一个 Coinbase 交易(一个区块，矿工只能给自己奖励一次)
+ 验证所有的交易，即遍历区块内所有的交易，检查是否是合法的交易。


## Transaction
+ 比特币有哪几种[交易类型](https://en.bitcoin.it/wiki/Transaction#Types_of_Transaction)？
    * Generation（挖矿）
        - While regular transactions use the 'inputs' section to refer to their parent transaction outputs, a generation transaction has no parent, and creates new coins from nothing.
    * Pay to Pubkey Hash
    * Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？

## Script
+ script IDE/Debugger
    * https://github.com/siminchen/bitcoinIDE
    * https://webbtc.com/script
    * http://www.crmarsh.com/script-playground/
    * https://ide.bitauth.com/

## 地址
+ see https://en.bitcoin.it/wiki/Address
    * ![Address_map.jpg](/img/Address_map.jpg)
+ [比特币地址 version 1 (P2PKH)的生成过程是？ ](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses)
    * ![PubKeyToAddr](/img/PubKeyToAddr.png)
        - 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
        - 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
        - 第三步，计算公钥的 SHA-256 哈希值。
        - 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
        - 第五步，取上一步结果，前面加入地址版本号。
        - 第六步，取上一步结果，计算 SHA-256 哈希值。
        - 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
        - 第八步，取上一步结果的前 4 个字节。
        - 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
        - 第十步，用 base58 表示法变换一下地址。
+ bech32
    * Bech32 is a [segwit](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) address format specified by [BIP 0173](https://en.bitcoin.it/wiki/BIP_0173).
    * P2SH
        - https://bitcoin.stackexchange.com/questions/65856/how-do-bech32-addresses-compare-to-p2sh-addresses-in-transaction-size
+ Bech32_address --> scriptPubKey in python
    ```
    >>> import segwit_addr
    >>> HRP='bc'
    >>> good_address='bc1qd6h6vp99qwstk3z668md42q0zc44vpwkk824zh'
    >>> typo_address='bc1qd6h6vp99qwstk3z669md42q0zc44vpwkk824zh'
    >>> wrong_network_address='tb1q3wrc5yq9c300jxlfeg7ae76tk9gsx044ucyg7a'
    >>> segwit_addr.decode(HRP, good_address)
    (0, [110, 175, 166, 4, 165, 3, 160, 187, 68, 90, 209, 246, 218, 168, 15, 22, 43, 86, 5, 214])

    >>> segwit_addr.decode(HRP, typo_address)
    (None, None)

    >>> segwit_addr.decode(HRP, wrong_network_address)
    (None, None)

    >>> witver, witprog = segwit_addr.decode(HRP, good_address)
    >>> bytes([witver + 0x50 if witver else 0, len(witprog)] + witprog).hex()
    '00146eafa604a503a0bb445ad1f6daa80f162b5605d6'
    ```
    + Bech32 addresses have a Human-Readable Part (HRP) that indicates what network the address is for. 
        * 数字 1 隔断 HRP 与地址的数据部分
        * HRP `bc`: mainnet
        * HRP `tb`: testnet
+ Sending to a P2PKH address vs Sending to a equivalent bech32 P2WPKH address
    * P2PKH
        - for `1B6FkNg199ZbPJWG5zjEiDekrCc2P7MVyC`, base58check library will decode that to a 20-byte commitment:
            ```
            6eafa604a503a0bb445ad1f6daa80f162b5605d6
            ```
        - This commitment is inserted into a scriptPubKey template:
            ```
            OP_DUP OP_HASH160 OP_PUSH20 6eafa604a503a0bb445ad1f6daa80f162b5605d6 OP_EQUALVERIFY OP_CHECKSIG
            ```
        - Converting the opcodes to hex, this looks like:
            ```
            76a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
            ```
        - This is inserted into the scriptPubKey part of an output that also includes the length of the script (25 bytes) and the amount being paid:
            ```
                 amount                           scriptPubKey
            |--------------|  |------------------------------------------------|
            00e1f505000000001976a9146eafa604a503a0bb445ad1f6daa80f162b5605d688ac
                            |
                size: 0x19 -> 25 bytes            
            ```
+ `scriptpubkey` vs `scriptsigs` 
    * 在两笔交易中, 按顺序组合并执行, 分别锁定和解锁
    * `scriptpubkey` 的原因是 地址只不过是公钥的哈希值, 发送方不知道接收方的公钥
    * ![LockingUnlocking](/img/LockingUnlocking.png)

## getaddressinfo
An current example of the descriptor format with key origin information and an error-detecting checksum:
```
$ bitcoin-cli getaddressinfo bc1qsksdpqqmsyk9654puz259y0r84afzkyqdfspvc | jq .desc
"wpkh([f6bb4c63/0'/0'/21']034ed70f273611a3f21b205c9151836d6fa9051f74f6e6bbff67238c9ebc7d04f6)#mtdep7g7"
```

+ The address is a Witness Public Key Hash `wpkh()`, otherwise known as P2WPKH. Descriptors can succinctly describe all common uses of P2PKH, P2SH, P2WPKH, P2WSH, and nested segwit.
+ The [key origin](https://github.com/bitcoin/bitcoin/blob/master/doc/descriptors.md#key-origin-identification) is described between the square brackets `[]`.
    * `f6bb4c63` is a fingerprint that identifies the key at the root of the path provided. The fingerprint is the first 32 bits of its `ripemd(sha256())` hash as [defined by BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#key-identifiers). This makes it easy for tools, such as those used with PSBTs, to work with multisig scripts and other cases where you have multiple signing devices using different keys.
    * `/0'/0'/21'` is the HD key path, corresponding to `m/0'/0'/21'` in standard [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) notation. This allows a wallet that doesn’t have all of its public keys precomputed to know which private key it needs to generate in order to produce the signature. (Bitcoin Core precomputes its public keys and so usually doesn’t need this information when used as a cold wallet—but hardware wallets with minimal storage and computational speed need HD path information in order to work efficiently.)
+ The actual public key used to generate the P2WPKH key hash is `034ed7...04f6`
+ A checksum following a # protects the descriptor string against typos on import, `mtdep7g7`

## mul-sig 多重签名

多重签名用于比如 公司董事会发起转账，对于一个 m-n 地址来说，参与创建者共有 n 人，发出的交易只要有 m 人正确签名就视为有效

比特币中地址其实就是解锁脚本, 多签地址也对应了一个多签脚本，与签名参与者的顺序有关(与参与者的pubkey以及顺序有关)

__创建多签脚本__:(并不是所有的链都是这个顺序，具体还是看 相应 opcode 在虚拟机中的执行)
```
m {pubkey1}...{pubkeyn} n OP_CHECKMULTISIG
```

比如
```
OP_2 [A's pubkey] [B's pubkey] [C's pubkey] OP_3 OP_CHECKMULTISIG
```

__解锁时__:
```
OP_0 ...signatures...
```

(OP_0 is required because of __a bug in OP_CHECKMULTISIG__, a workaround for an off-by-one error in the original implementation; it pops one too many items off the execution stack, so a dummy value must be placed on the stack).

比如
```
OP_0 [A's signature] [B's or C's signature] [serialized redeem script]
```

__注意__:
签名顺序要和多签脚本中pubkey的顺序相同, 验签时会检查签名数量有否满足，然后虚拟机 逐个 按顺序 Verify(pubkey, msg, sig)


## Segregated Witness (SegWit) 隔离见证

1. 把脚本签名(scriptSig)信息从基本结构 (base block) 里拿出来，放在一个新的数据结构当中。做验证工作的节点和矿工也会验证这个新的数据结构里的脚本签名，以确保交易是有效的。

![segwit](/img/segwit.jpg)


当审核统计区块大小的时候。脚本签名大小不会被计算在内。

对于 __某些交易__ ，签名散列增长是平方增长的, 而不是线性增长的。Segwit 通过改变交易哈希签名的计算方式可以解决此问题，使得交易的每个字节只需要至多两次哈希。

__为什么不直接用大区块？__

其实这两个扩容方法都是可以采取，但是有一个先后问题。先后次序会很大程度影响到比特币的整个网络。那么为什么技术上要先上隔离见证，简单说来直接增大区块容量让下载全节点不适合于普通PC电脑，网络中越少的全节点其实不利于比特币安全。

2.

2.1. 防止了延展性攻击（malleability）。TxID 的计算方法使得任何人可以对交易做微小的改动，不会改变交易的内容，但是会改变TxID，这就是所谓的第三方延展性。之前那个原始交易记录会被认为是无效的交易而失败。不会造成双花，也不会对区块链造成破坏，但是对原始交易记录的发起者会造成困扰，因为如果拿着原始交易记录的哈希值找不到交易的成功记录。尤其是对于一些交易所，如果没有完整的内部日志，可能无法追溯交易记录，导致攻击者利用拼凑的交易记录先成功提币，再申诉说没有提到币，要求再次提币。

2.2 更好的实现 闪电网络 ：闪电网络的具体实现需要创建一系列相互依赖的父子交易记录，需要先对子交易记录签名，然后将子交易记录交换后，再对父交易记录签名并广播。所以，有了隔离见证后，才能更完美的支持闪电网络。

2.3 花费未确认的交易： 如果 Alice 在交易1支付 Bob一些币，Bob 在交易2 使用收到的付款支付给Charlie，然后Alice的付款发生延展性修改,并用不同的txid确认， 那么交易2现在是无效的，而Charlie就不会被支付。如果Bob是值得信赖的，他会重新发出一笔交易给查理；但如果他不是，他可以简单地把这些比特币留给自己。


### 节省费用
bech32 发送支持允许付款的接收者在重新花费时节省费用。

对于在第一版比特币中实现的传统 P2PKH 地址格式，授权花费的 scriptSig 通常为 107 vbytes。 对于 P2SH 封装的 segwit P2WPKH，这个信息被移动到 witness 见证数据字段，该字段仅消耗 1/4 的 vbytes（27 vbytes），但其 P2SH 开销增加 23 vbytes，总共 50 vbytes。 对于原生 segwit P2WPKH，没有 P2SH 开销，所以共使用 27 个 vbytes。

这意味着，P2SH-P2WPKH 比 P2PKH 节省了 50% 以上的空间，而 P2WPKH 又比 P2SH-P2WPKH 节省了近 50%，或直接比 P2PKH 节省了75%。但是，支出交易不仅包含 scriptSigs 和见证数据，因此我们通常比较节省了多少空间的方式是通过看原型交易。 例如，我们想象一个典型的事务包含一个输入和两个输出（一个到接收者;一个作为找零回到消费者）。 在这种情况下：

+ 支出 P2PKH 的总交易大小为 220 vbytes
+ 支出 P2SH-P2WPKH 的大小为 167 vbytes（节省24％）
+ 支出 P2WPKH 输出的大小为 141 vbytes（与 P2SH-P2WPKH 相比节省16％，与 P2PKH 相比节省35％）

在比较简单的 multisig 交易（那些只使用单个 OP_CHECKMULTSIG 操作码的事务）时会变得复杂，因为 k-of-n multisig 输入的大小取决于签名的数量（k）和公钥的数量（n）。 因此，为了简单起见，我们将仅绘制传统 P2SH-multisig 与封装的 P2SH-P2WSH multisig 大小对比（上到传统 P2SH 支持的15-of-15）。 我们可以看到，切换到 P2SH-P2WSH 可以节省大约 40％（1-of-2 multisig）到大约 70％（15-of-15）。

![segwit-multisig-size-p2sh-to-p2sh-p2wsh](/img/optech/2019-04-segwit-multisig-size-p2sh-to-p2sh-p2wsh.png)


然后，我们可以将 P2SH-P2WSH 与原生 P2WSH 进行比较，可以看到每个交易节省约 35 个字节的额外恒定大小，即约 5％ 到 15％。

![segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png](/img/optech/2019-04-segwit-multisig-size-p2sh-p2wsh-to-p2wsh.png)

上面描述的脚本占了几乎所有与非原生 segwit 地址一起使用的脚本。（更复杂脚本的用户，例如在 LN 中使用的脚本，现在大多使用原生 segwit。）那些效率较低的脚本类型目前占用区块容量的大部分（总区块权重）。切换到原生 segwit 以减少交易的权重可以将费用减少相同的百分比，而无需改变确认所需的时间 - 其他什么都不用改。

但也不是什么改变也没有。由于交易使用较少的块权重，因此可用于其他交易的权重更多。如果可用区块权重的供应增加且需求量保持不变，手续费应该会下降（除非它们已经是默认的最低转发费用）。这意味着更多的人花费原生 segwit 输入不仅降低了那些花费者的费用，而且降低了每个创建交易的人的费用 - 包括支持发送到 bech32 地址的钱包和服务。


别的一些好处:

+ https://bitcoincore.org/en/2016/01/26/segwit-benefits/
+ https://bbs.huaweicloud.com/blogs/710256bf476611e89fc57ca23e93a89f

## Lightning Network

浏览器: 
+ https://1ml.com/
+ https://explorer.acinq.co/
+ https://github.com/altangent/lnd-explorer


Readings:

+ [闪电网络三部曲](https://bitcoinmagazine.com/articles/understanding-the-lightning-network-part-building-a-bidirectional-payment-channel-1464710791/)
+ https://yeasy.gitbooks.io/blockchain_guide/content/bitcoin/lightning_network.html
+ https://github.com/ElementsProject/lightning/blob/master/doc/deployable-lightning.pdf
+ https://blog.bitmex.com/zh_cn-the-lightning-network/
+ https://lightning.engineering/technology.html
+ https://en.bitcoin.it/wiki/Hash_Time_Locked_Contracts
+ https://en.bitcoin.it/wiki/Payment_channels
+ https://gist.github.com/bretton/0b22a0503a9eba09df86a23f3d625c13
+ https://github.com/lightningnetwork/lnd/blob/master/sample-lnd.conf
+ https://ln-zap.github.io/zap-tutorials/iOS-remote-node-setup.html

<!-- 
Relative Lock Time
Allows a transaction to be time-locked, preventing its use in a new transaction until a relative time change is confirmed.


Breach Remedy Transaction:[1] the transaction Alice creates when Mallory attempts to steal her money by having an old version of the channel state committed to the blockchain. Alice's breach remedy transaction spends all the money that Mallory received but which Mallory can't spend yet because his unilateral spend is still locked by a relative locktime using OP_CSV. This is the third of the maximum of three on-chain transactions needed to maintain a Lightning channel; it only needs to be used in the case of attempted fraud (contract breach).


Relative locktime:[7] the ability to specify when a transaction output may be spent relative to the block that included that transaction output. Enabled by BIP68 and made scriptable by BIP112. Lightning uses relative locktime to ensure breach remedy transactions may be broadcast within a time period starting from when an old commitment transaction is added to the blockchain; by making this a relative locktime (instead of an absolute date or block height), Lightning channels don't have a hard deadline for when they need to close and so can stay open indefinitely as long as the participants continue to cooperate.

Revocable Sequence Maturity Contract (RSMC):[1] a contract used in Lightning to revoke the previous commitment transaction. This is allowed through mutual consent in Lightning by both parties signing a new commitment transaction and releasing the data necessary to create breach remedy transactions for the previous commitment transaction. This property allows Lightning to support bi-directional payment channels, recover from failed HTLC routing attempts without needing to commit to the blockchain, as well as provide advanced features such as PILPPs.

 -->

 微支付通道 -> 双向支付通道 -> 闪电网络

[比特币白皮书](https://bitcoin.org/bitcoin.pdf) 发表于 2009 年，[闪电网络白皮书](https://lightning.network/lightning-network-paper.pdf) 发表于 2016 年。闪电网络起源于比特币的扩容问题。闪电网络是基于微支付通道演进而来，创造性的设计出了两种类型的交易合约：序列到期可撤销合约 RSMC（Revocable Sequence Maturity Contract，哈希时间锁定合约 HTLC（Hashed Timelock Contract）。RSMC 用来完成两个节点之间的交易确认，解决了通道中币单向流动问题，HTLC ，用来将多个节点连接成一个网络，解决了币跨节点传递的问题。这两个类型的交易组合构成了闪电网络。


### 优点
+ 小微支付成为可能
    * 交易金额低至一聪
+ 小额甚至无需手续费
+ 付款实时结算
+ 更好的隐私性：并非每笔交易都会被存在链上
    * 默认使用洋葱路由器进行分享
        - Oninion Routing (with the help of the SPHINX paper) in BOLT 04 is that you as the payer of a network hide who is receiving the money. Also you hide that you are the sender (though every node can send back error messages to you). 
            + If you pay a person with these oninion payments and this person is NOT using TOR for their lightning node you will know who the payee is (at least you know the IP address and to some degree where the computer stands) Others on the way do not know this (only the channel partner knows that the payee is involved in the payment process but it is not clear that it is the receipient to the channel partner).
    * 可以配置 tor 代理
        - Tor network is to hide your IP address
+ Securely cross blockchains: payments can be routed across more than one blockchain (including altcoins and sidechains) as long as all the chains support the same hash function to use for the hash lock, as well as the ability the ability to create time locks.
    * 实现原子交换，在通道内能将比特币交换为 Litecoin，Groestl 或 Dogecoin
+ 由于 P2P 网络的特性，所以闪电网络上的交易是不可阻止的。

### 缺点
+ 节点故障：如果其中一个节点没有响应，用户可能需要等待数小时才能关闭支付通道并再次通过另一条路径重新发送资金
+ 不可离线支付：用户无法向不在线的人进行支付
+ 不适用于大额支付：即使通过不同支付通道的路径可能存在，不同节点的多重签名钱包中的资金也可能不足以转移大额资金
+ 可能会造成支付中继站的中心化
    * 解决办法是多建闪电网络节点
+ 可能存在的攻击
    * 女巫攻击把中继站的资金池掳走，耗时很久才返还

### rebalancing 
__TODO__


## ECDSA Failures
+ ECDSA Failures
+ https://github.com/coinexchain/docs/blob/master/190722-secp256k1-ecdsa-dangers.pdf
+ https://safecurves.cr.yp.to/

## Colored Coin

## 比特币分叉
规则改变时, 网络中新旧节点遵守的规则不同
### 硬分叉
旧节点不接受新节点产生的区块，导致网络分裂为新链和旧链
### 软分叉
旧节点接受新节点产生的区块（虽然可能潜在风险）

## 比特币扩容之争

### BCH vs BTC

#### 扩大区块 VS 闪电网络
##### 扩大区块
* 主要支持者:矿工(比特大陆...)
    - 不过，大区块派并不都反对闪电网络，他们中的一部分并不抵触部署闪电网络，但是坚持在建设闪电网络的同时仍然需要扩大区块。
* 理由:
    - 闪电网络做的隔离见证对原有比特币系统有 __巨大改动__ ,万一失败
    - 闪电网络毕竟 __不是比特币区块链__ ，闪电网络可能会被 __中心化的机构__ 控制，导致比特币的中心化。
* 利益:
    - 希望赚交易费用，而闪电网络把大量小额交易都隔离开了

##### 闪电网络
see [Lightning Network](#lightning-network)

+ 主要支持者:Bitcoin Core
+ 建立在 [隔离见证](#segregated-witness-segwit-%E9%9A%94%E7%A6%BB%E8%A7%81%E8%AF%81) 的基础上
* 理由:
    - 扩大区块需要进行 [__硬分叉__](#%E7%A1%AC%E5%88%86%E5%8F%89) ，万一有的用户不升级钱包，就会产生对比特币的分裂，造成混乱；
    - 如果区块变大，以后交易越来越多，普通人的电脑上根本就运行不起这么 __大的全节点钱包__ ，只有机构的电脑可以运行，就会导致比特币的中心化。
* 利益:
    + Core团队中的好几名成员都在研发闪电网络的公司Block Stream工作,推广闪电网络可以赚取专利费

#### 香港共识
各退一步 部署隔离见证的同时把区块大小扩大到2M，由Core来主导开发。

#### 纽约共识
Core的几个开发者在共识上签完字后，团队里的其他成员却不认同这个共识，不愿意开发，于是香港共识后来连代码都没写，就这样跳票. 矿主对Core失去了信任, 纽约重新召开一个无core团队参与的大会

纽约共识达成的协议跟香港共识很像，也是隔离见证+2M扩容，SegWit2x。区别是纽约共识中，隔离见证和扩容分成了两步进行：

+ 2017年8月1日 激活隔离见证（SegWit）
+ 2017年11月，区块大小扩容到原来的两倍（2x）。

#### BCC分叉
Core一看被排挤, 在纽约共识约定的隔离见证部署前，提出UASF（用户激活的软分叉），并声称不会对UASF进行任何的 [重放保护](/blockchain/attack.html#%E9%87%8D%E6%94%BE%E6%94%BB%E5%87%BB) 。

但UASF最终因算力小且被 SegWit 兼容，没有真产生分叉，反推动比特大陆投资的矿池 ViaBTC（微比特）团队实施了针对 UASF 的硬分叉 UAHF（用户激活的硬分叉）。

+ 8月1日，ViaBTC 挖出了第一个区块，对比特币区块链进行了硬分叉
+ 产生了比特币的克隆竞争币 比特现金（Bitcoin Cash，BCC，国外又称BCH）
    * 比特币现金的区块大小可以上升到8M
    * 可容纳交易笔数是原来比特币8倍左右
    * 去掉了隔离见证
    * 区块容量大，交易速度的确更快，手续费更低
    * 克隆了比特币原链上的余额, 原比特币用户获得等额比特现金

### BCH-ABC vs BCH-SV

#### BCH-ABC
更偏向智能合约, 利用二层网络方案

+ 短出块时间到2分钟，同时区块奖励也相应减少
    * 不造成增发
    * 2分钟说长不长说短不短
        - 作为线下交易使用是过长了，“买咖啡”类的应用必须依赖零确认
        - 对于扩容前景来说，2分钟又太短了。出块平均时间设置在两分钟，而出块的时间间隔的分布是泊松分布，会有大量的相隔几秒的出块，在 GB ， TB 级的区块的时候，很容易造成来不及传输和验证。
+ 引入虫洞方案，利用二层网络方案使得BCH上可以新发token
    * 通过“摧毁”BCH 来获得，是单向的，有去无回。
    * 不能用零确认的BCH，至少要一确认才能让虫洞上的操作接续下去。
+ 引入DSV操作码
    * DSV 操作码的全称为 OP_CHECKDATASIGVERIFY 
    * 允许 BCH 对链外结果和数据进行校验
        - 这个方案非常激进，哪怕是在以智能合约为主要特性的ETH或者EOS都没有直接提供对ORACLE或者说外部数据的连接，更多的是结果上链，判定在链下完成。
        - 这个操作码的加入，对bitcoin的原始设定，矿工按打包字节收费这一基础设定有所更改，因为DSV可能是一个复杂操作，但是在字节数上只显示为一个操作码。另外一点，DSV 更改了矿工的定义，矿工从只提供算力校验链上交易的角色码，转变为校验交易和校验数据的双重角色。

#### BCH-SV
更偏向原始的比特币方案, 不用二层网络方案

+ 扩容，恢复曾经有但被 core 删掉了的操作码，去掉各种限制（比如一个交易内可以使用操作码的数量限制等）
+ tokenized方案，完全利用 OP_RETURN , 在原有网络上增加 token 协议


## 量子计算对 bitcoin 的威胁

量子计算中已经比较成型的算法：Shor’s algorithm 和 Grover’s algorithm

Shor’s algorithm 解决因式分解的问题 —— 给定一个整数 N，找到其质因数。把指数级的时间复杂度降维成了 polynomial time，也就是多项式时间。

RSA 的基石就是两个大质数 p 和 q 的合数很难被因式分解出 p 和 q。

Shor 对 RSA 体系的破坏性是显而易见的(整个非对称加密体系下的算法都会受到巨大的冲击)，而且，它的变种，对基于椭圆双曲线的 ECDSA 也有类似的降维杀伤力。

然而你还是能信任你的 bitcoin 钱包。虽然 bitcoin 钱包的私钥和钱包地址都来源于 ECDSA 的私钥和公钥，然而钱包地址并非直接是公钥，而是公钥的 hash。因而，你给一个钱包打钱，并不会需要钱包的公钥；只有这个钱包使用里面的钱（给别人打钱）时，才需要把自己的公钥放在 transaction 里。__如果一个钱包只是收钱，那么它是安全的__ —— 即便 Shor 算法也需要公钥去逆向私钥。因为公钥没有暴露出来，Shor 算法无法使用。因而即便量子计算破解了非对称加密算法，对于那些没有使用过的冷钱包（code wallet），也无法破解。对于那些需要 multisig 的钱包，也是类似。

如果非得破解冷钱包，那么需要先把钱包地址逆向出来其公钥，而这个操作 Shor 无法完成，只能借助 Grover 算法。

基本上，Grover 对于函数 f(x) = y，只要给定 y，以及 x 取值的一个列表，它可以以 O(sqrt N) 的时间复杂度，找到这个 x。换句话说，随便一个算法，正常情况下暴力破解（在算法的定义域里一个个试），是 O(N)，Grover 将其降低成 O(sqrt N)，对于时间复杂度来说，这算法虽然看上去不错，但大多数情况下只是聊胜于无。

一个 256bit 的公钥, 256bit 数字的取值范围 n_max: 5.78960446186581e+76

sqrt(n_max) = 2.4061596916800453e+38

而 log(n_max) = 176.75253104278605

虽然 sqrt 后量级已经大大减少，但还是 trillion trillion trillion 级别，在一个可以预见的时间内无法破解。所以，即便使用了 Grover 算法，也无法有效地通过钱包地址破解出公钥，进而进一步使用 Shor 算法从公钥破解出私钥。

### NewHope 密钥交换协议
[该协议](https://newhopecrypto.org/) 被认为可抵抗量子计算机的攻击，因此今天记录两个对等体之间通信的窃听者将无法在将来解密该数据他们拥有一台快速量子计算机。

## Schnorr签名

__TODO:__
+ https://bitcointechtalk.com/scaling-bitcoin-schnorr-signatures-abe3b5c275d1
+ https://bitcointechtalk.com/what-is-a-bitcoin-merklized-abstract-syntax-tree-mast-33fdf2da5e2f
+ eltoo

可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

任何多重签名协议，如BitGo或闪电网络使用的协议，都将与普通点对点交易一样小。由此节省的空间总量很难估计，但如果每个人都采用这种方法，预计比特币区块链的容量将增加10-20%。

Schnorr签名可以很容易地扩展到支持固定大小的多签名和阈值签名，以及“无脚本脚本”，这些脚本允许在签名中编码闪电支付通道的语义。而对于ECDSA，这要困难得多。Schnorr签名的批量验证也是可能的，这使得它们的验证速度比比特币的ECDSA签名快得多。

Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

无脚本脚本（Scriptless scripts）是扩展两方Schnorr多签名协议的一种方法。该协议允许两个用户联合生成一个签名，使联合签名具有与普通签名相同的大小和使用相同的验证方程。使用无脚本脚本，可以扩展此协议，当最后一方完成签名时，还会向另一方泄露额外的秘密。这个额外的秘密可以像在Lightning HTLC中使用的“哈希原像（hash preimages）”一样使用，而且还有一个额外的好处，那就是它不会出现在区块链上。它还具有更多的代数结构，这使得它在连接多个支付通道时可以“重新盲化”，从而修正了闪电网络的隐私限制，即路径中的每个通道都需要使用相同的路径。

无脚本脚本可以极大地改善隐私，它允许用户创建长路径的支付通道，而不用使用相同的哈希像原将它们链接起来，还可以防止这些哈希显示给区块链。eltoo可以提高可扩展性，它使用的是SIGHASH_NOINPUT，这是比特币的另一个提议，允许闪电用户在一定的空间内无限期地维护支付通道。

Taproot 是为比特币提出的一个提议，所有的输出都用一个签名密钥，可以用一个签名消费。使用多重签名和无脚本脚本，可以使用这些单签名对多方交易、闪电支付通道等进行编码。Taproot还允许这个密钥提交到一个额外的脚本，以防无脚本脚本不够用。但是在合作的情况下，从来没有显示过这个额外的脚本。Taproot 不能隐藏资金流向和具体金额的，只能隐藏合约内容，而且如果双方不愿意合作，可能还是会暴露合约的内容

Graftroot 是另一个被提出的扩展方案，它不太可能很快被包含在比特币中，它进一步允许Taproot签署者签署替代的消费路径，而不是使用Taproot输出中提交的脚本。由于没有对可以签署多少消费策略的限制，在用户有1000条消费路径的情况下，这将大大提高效率。然而，在实践中还不清楚这是否是用户所希望的。

大大提高了多签名的可伸缩性，允许创建具有非常多参与者的多重签名，同时比特币区块链不需要任何空间成本。它们还可以用来在比特币和Liquid之间创建原子交换交易，而且除了普通交易之外，不需要额外的比特币空间成本。

Schnorr签名方案会遇到重放攻击（Replay Attack）的问题，如果不去研究新的机制，目前的MuSig签名方案就无法保护在虚拟机中进行签名的用户。可以被解决： https://zkproof.org/workshop2/main.html

### bip-taproot & bip-tapscript
总结 from: https://bitcoinops.org/en/newsletters/2019/05/14/

+ [bip-taproot](https://github.com/sipa/bips/blob/bip-schnorr/bip-taproot.mediawiki) 允许通过 Schnorr-style 签名  或 通过 merklized script 默克尔化脚本 进行花费
+ [bip-tapscript](https://github.com/sipa/bips/blob/bip-schnorr/bip-tapscript.mediawiki) 定义了 用于 bip-taproot 中 merkle spend 默克尔花费的脚本语言（与 bitcoin 中现有脚本相近但稍有不同）。
+ 单签 P2PKH 和 P2WPKH 中，生成私钥，派生出公钥，对公钥进行哈希然后生成地址的 witness program。Taproot 中 哈希这一步被省略，所以 地址中会直接包含 公钥。
    * P2WPKH
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>read 32 bytes from <a href="https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator">CSPRNG</a>, or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD derivation</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>point(0x807d[…]0101), or using <a href="https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki">BIP32</a> HD public derivation</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Hash</td>
      <td>ripemd(sha256(0x0202e5[…]3c23))</td>
      <td><code class="highlighter-rouge">0x006e[...]05d6</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 0, 0x006e[…]05d6)</td>
      <td><code class="highlighter-rouge">bc1qd6[...]24zh</code></td>
    </tr>
  </tbody>
</table>
    * Taproot
        - Taproot 中 哈希这一步被省略，所以 bech32 地址中会直接包含公钥, 伴随着一点小改变。 33-byte 比特币公钥 以 0x02 或 0x03 开头，以便验证者在secp256k1椭圆曲线上重建密钥的 Y-坐标; bip-taproot 中该值会被减2，于是 0x02 变成 0x00， 0x03 变成 0x01 但含义不变。这样（换成使用低位）的好处是，把剩下的位省下用来将来软分叉。此外， witness version 从P2WPKH/P2WSH 中的 0 变成了 1.
<table>
  <thead>
    <tr>
      <th>Object</th>
      <th>Operation</th>
      <th>Example result</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Private key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x807d[...]0101</code></td>
    </tr>
    <tr>
      <td>Public key</td>
      <td>(Same as above)</td>
      <td><code class="highlighter-rouge">0x02e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Alter key prefix</td>
      <td>(key[0] - 2),key[1:33])</td>
      <td><code class="highlighter-rouge">0x00e5[...]3c23</code></td>
    </tr>
    <tr>
      <td>Address</td>
      <td>bech32.encode(‘bc’, 1, 0x00e5[…]3c23)</td>
      <td><code class="highlighter-rouge">bc1pqr[...]xg73</code></td>
    </tr>
  </tbody>
</table>
+ 花费 P2PKH 或 P2WPKH 需要在 inpu 中包含 公钥。Taproot 中公钥会在花费的 UTXO 中提供，所以可以省略好几个 vbytes。
+ 创建 Taproot output 和 创建一个 P2WSH output 占用基本差不多空间；但花费一个单签 Taproot 比起 P2WPKH 节省 40% 的空间。而节省交易体积就有助节省交易费。
+ 交易消息摘要中的 double-SHA256 hashing 其实不能提供额外的安全帮助。所以 Taproot 中换成了只进行一次。
+ 对 多签来说，Taproot 无论有多少密钥和签名，占用大小都一样。而 P2WSH 中每多一个公钥就要增加 8.5 vbytes，每多一个签名就要增加 18.25 vbytes。
    * ![2019-05-taproot-multisig-size](/img/optech/2019-05-taproot-multisig-size.png)
+ Tapscript 中不再接受 ECDSA 签名。
+ Tapscript 中废除了 OP_CHECKMULTISIG 和 OP_CHECKMULTISIGVERIFY。 可以使用 OP_CHECKSIGADD (OP_CSADD) 来替代，并可以显著加速批量验证签名。
+ 通过 taproot 和 tapscript，single-sig, multisig, and MAST-based spends 都可以只用同一个 public key 和签名,那么追踪用户在使用 bitcoin 哪一个特性就更难，这算是对抗追踪、对抗审查的一个好事。


## RSK
rsk，以前被称为Rootstock，是一个比特币的侧链, layer 2，RSK克隆了以太坊的虚拟机(EVM)，这意味着该平台支持EDCCs（智能合约）。# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### ProgPow

### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。

## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。# Cryptography

## Math
+ Binary Quadratic Forms, An Algorithmic Approach - Algorithms and Computation in Mathematics • Volume 20
+ https://crypto.stackexchange.com/questions/10467/what-is-the-lowest-level-of-mathematics-required-in-order-to-understand-how-encr
+ https://intensecrypto.org/public/index.html
+ [Quadralay's Cryptography Archive](https://www.austinlinks.com/Crypto/)
+ https://crypto.stackexchange.com/questions/53321/what-are-the-coordinates-of-a-generator-point
+ https://www.michaelstraka.com/posts/classgroups/
+ https://docs.google.com/document/d/1sdZqVdXOY9h85oVP2E47SFDMlsAbiPZY1LcaPAZ5LV8/edit
+ http://safecurves.cr.yp.to/
+ https://blog.cr.yp.to/index.html
+ https://github.com/sobolevn/awesome-cryptography
+ https://github.com/pFarb/awesome-crypto-papers
+ https://github.com/SebastianElvis/crypto-lecture-notes/
    * https://www.garykessler.net/library/crypto.html
    * https://cseweb.ucsd.edu/~mihir/papers/gb.pdf
    * http://crypto.stanford.edu/~dabo/cryptobook/
        - https://toc.cryptobook.us/
    * http://www.wisdom.weizmann.ac.il/~oded/foc-book.html
        - http://www.wisdom.weizmann.ac.il/~oded/foc.html
    * http://cacr.uwaterloo.ca/hac/
    * https://crypto.stanford.edu/~blynn/
        - https://crypto.stanford.edu/pbc/notes/
    * http://math.tut.fi/~ruohonen/MC.pdf
    * [Practical Cryptographic Systems](http://spar.isi.jhu.edu/~mgreen/650.445/650.445__Main.html)
        * https://github.com/matthewdgreen/practicalcrypto
    + https://cse.iitkgp.ac.in/~debdeep/courses_iitkgp/FCrypto/index.htm
    + https://courses.cs.washington.edu/courses/cse599b/
    + [Graduate Cryptography (Topics in Theory)](http://www.ccs.neu.edu/home/wichs/class/crypto-fall15/index.html) from Northeastern
    + [Foundations of Cryptography](http://www.ccs.neu.edu/home/wichs/class/crypto-fall17/index.html) from Northeastern
    + [Introduction to Cryptography](https://cims.nyu.edu/~regev/teaching/crypto_fall_2014/) from NYU
    + [Randomness in Cryptography](https://cs.nyu.edu/courses/spring14/CSCI-GA.3220-001/index.html) from NYU
    + [Introduction to Cryptography](https://cs.nyu.edu/courses/spring12/CSCI-GA.3210-001/)
    + [Cryptography](https://www.cs.princeton.edu/courses/archive/spring10/cos433/) from Princeton
    + [Cryptography](https://courses.engr.illinois.edu/cs598man/sp2016/) from UIUC
    + [Applied Cryptography](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/) from UIUC
    + [Classics of Cryptography](https://crypto.stanford.edu/cs359c/17sp/index.html) from Stanford
    + [Cryptography](https://moodle.cs.huji.ac.il/cs14/course/view.php?id=67531) from Hebrew
    + [Cryptography](https://www.boazbarak.org/cs127spring16/) from Harvard
    + [Advanced Topics in Cryptography](http://www.cs.umd.edu/~jkatz/gradcrypto2/scribes.html) from UMD
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/gradcrypto2/links.html)
    + [Introduction to Cryptography](http://www.cs.umd.edu/~jkatz/crypto/s19/lectures.html) from UMD
        + [Lecture notes for Fall '02](http://www.cs.umd.edu/~jkatz/crypto/f02/lectures.html)
        + [Suggested Readings](http://www.cs.umd.edu/~jkatz/crypto/f02/readings.html)
    - [Introduction to Modern Cryptography (2nd edition)](http://www.cs.umd.edu/~jkatz/imc.html) by Jonathan Katz and Yehuda Lindell
    + [Cryptography](https://people.eecs.berkeley.edu/~alexch/classes/CS276-F2017.html) from Berkeley
    + [Advanced Cryptography](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring18/) from Berkeley
    + [A Theorist's Toolkit](https://www.cs.cmu.edu/~odonnell/toolkit13/) from CMU
    - [A Course in Cryptography](http://www.cs.cornell.edu/courses/cs4830/2010fa/lecnotes.pdf) by Rafael Pass and Abhi Shelat
    + [Handbook of Applied Cryptography](https://notendur.hi.is/pgg/Handbook%20of%20Applied%20Cryptography.pdf)
    + [Foundations of Cryptography 89-856](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf) by Yehuda Lindell
    + [An intensive introduction to cryptography](https://github.com/boazbk/crypto) by Boaz Barak
    - [Information-Theoretic Lower Bounds](https://theinformaticists.com/2019/09/16/online-lecture-information-theoretic-lower-bounds/)
    + [Probabilistically Checkable and Interactive Proof Systems](https://people.eecs.berkeley.edu/~alexch/classes/CS294-S2019.html) from Berkeley
    + http://www.ccs.neu.edu/home/viola/classes/gems-08/
    + https://www.cs.princeton.edu/courses/archive/spr10/cos433/#NOTES
    + https://homes.esat.kuleuven.be/~nsmart/FHE-MPC/
    + http://www.cs.bu.edu/~reyzin/teaching/s11cs937/
    + http://cs.brown.edu/~seny/2950v/
        * https://web.engr.oregonstate.edu/~glencora/cs175/index.php/CourseScheduleSpring2018#w5H
    + [Applied Cryptography](https://www.cs.virginia.edu/~evans/courses/crypto-notes.pdf) by David Evans
    + [The Joy of Cryptography](https://web.engr.oregonstate.edu/~rosulekm/crypto/)
+ https://tlu.tarilabs.com/cryptography/cryptography.html
+ https://github.com/isislovecruft/library--/tree/master/cryptography%20%26%20mathematics
+ [Elliptic Curve Handbook](http://webs.ucm.es/BUCM/mat/doc8354.pdf)
- [Pseudorandomness](https://people.seas.harvard.edu/~salil/pseudorandomness/) by Salil Vadhan
- http://www.cs.columbia.edu/~tal/6261/SP20/reading/rand-survey.pdf
- https://mnin.org/write/2006_sshcrypto.html
+ https://github.com/claucece/useful-crypto-resources
+ https://people.csail.mit.edu/rivest/crypto-security.html
- https://cseweb.ucsd.edu/~mihir/crypto-links.html
+ 图解密码学
+ Applied Cryptography: Protocols, Algorithms, and Source Code in C 应用密码学
+ [Mathematics of Isogeny Based Cryptography](https://arxiv.org/abs/1711.04062)
+ **tools**
    * https://kconrad.math.uconn.edu/blurbs/
    * http://people.csail.mit.edu/vinodv/FHE/FHE-refs.html

## EdDSA
+ https://sefiks.com/2018/12/24/a-gentle-introduction-to-edwards-curve-digital-signature-algorithm-eddsa/
+ https://ed25519.cr.yp.to
    + https://research.kudelskisecurity.com/2017/10/04/defeating-eddsa-with-faults/
    * https://crypto.stackexchange.com/questions/72035/difference-between-pure-eddsa-ed25519-and-hasheddsa-ed25519ph
    * https://crypto.stackexchange.com/questions/52611/eddsa-ed25519-is-any-random-number-sufficient-for-a-good-private-key
- EdDSA vs ECDSA
    + https://crypto.stackexchange.com/questions/58380/ecdsa-eddsa-and-ed25519-relationship-compatibility
    * https://crypto.stackexchange.com/questions/60383/what-is-the-difference-between-ecdsa-and-eddsa

## ZKP academa
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/AdamISZ/from0k2bp
+ conference
    * Zk proof workshop
    * ZK #lux
    * Zcash 的 #Zcon1
+ https://docs.zkproof.org/reference
+ https://zkp.science/
+ https://github.com/zcash/zips/blob/master/protocol/protocol.pdf
+ https://github.com/zcash-hackworks/design-of-sapling-book
+ https://www.zfnd.org/blog/a-new-network-stack-for-zcash/
+ https://github.com/zcash/zips/blob/master/protocol/sapling.pdf
+ https://github.com/zcash/zips/blob/master/zip-0243.rst
+ https://docs.google.com/presentation/d/1qsOtMLiBVhVMbeB_R0heTSMRsKnhuOKfhACFiXKM-J0/edit#slide=id.p
+ https://github.com/arielgabizon/sapling-security-analysis
+ https://github.com/zcash/sapling-security-analysis/blob/master/MaryMallerUpdated.pdf

## MPC
+ [A Pragmatic Introduction to Secure Multi-Party Computation](https://securecomputation.org/)
+ https://www.mpcalliance.org/learn
+ https://github.com/rdragos/awesome-mpc
+ [From Keys to Databases – Real-World Applications of Secure Multi-Party Computation](https://eprint.iacr.org/2018/450.pdf)
+ http://www.multipartycomputation.com/mpc-software
+ lindell's
    + http://u.cs.biu.ac.il/~lindell/MPC-resources.html
    * [Foundations of Cryptography](http://u.cs.biu.ac.il/~lindell/89-856/complete-89-856.pdf)
    * [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)
    * ...
- [Secure Computation](http://www.cs.umd.edu/~jkatz/gradcrypto2/f13/scribes.html) from UMD
+ [Special Topic in Cryptography: Secure Computation](https://people.eecs.berkeley.edu/~sanjamg/classes/cs294-spring16/) from Berkeley
+ [Distributing any Elliptic Curve Based Protocol](https://eprint.iacr.org/2019/768.pdf)
    * This (work) allows us to transform essentially any one-party protocol over an elliptic curve, into an n-party one
+ [Reliable and Secure Distributed Programming](https://github.com/ChrisLinn/chrislinn.ink/tree/master/assets/IntroductionToReliableAndSecur_Book_2011.pdf) (book)
+ Coding Theory, Cryptography and Related Areas? (book)
+ https://www.binance.vision/security/threshold-signatures-explained
+ https://guutboy.github.io
+ incentives
    * [Insured MPC: Efficient Secure Multiparty Computation with Punishable Abort](https://eprint.iacr.org/2018/942.pdf)
    * [Catching MPC Cheaters: Identification and Openability∗](https://eprint.iacr.org/2016/611.pdf)
    * ~~[An End-to-End System for Large Scale P2P MPC-as-a-Service and Low-Bandwidth MPC for Weak Participants](https://eprint.iacr.org/2018/751.pdf)~~
- [Distributed Key Generation in the Wild](https://eprint.iacr.org/2012/377.pdf)
- [Bootstrapping Consensus Without Trusted Setup: Fully Asynchronous Distributed Key Generation](https://eprint.iacr.org/2019/1015.pdf)
- [Revisiting the Distributed Key Generation for Discrete-Log Based Cryptosystems](https://pdfs.semanticscholar.org/642b/d1bbc86c7750cef9fa770e9e4ba86bd49eb9.pdf)
+ https://marsella.github.io/static/mpcsok.pdf
+ https://www.eng.biu.ac.il/hazay/publications/
+ https://sharemind.cyber.ee/research/

# VDF
+ https://vdfresearch.org/

# Prover
+ [verifpal](https://verifpal.com/res/pdf/manual.pdf)
    * easy to learn
        - flawn in earlier versions
        + it now has ProVerif and Coq translators
    * has a more intuitive language than tamarin or easycrypt
        - [Tamarin](https://tamarin-prover.github.io/)
            + hard to learn but can do more
                * examples
                    - https://www.wireguard.com/papers/wireguard-formal-verification.pdf
        - easycrypt# DApps

See [awesome-dapps](https://github.com/HAOYUatHZ/awesome-dapps)# Exchange
+ https://github.com/Lianantech/Exchange-security-issues
+ https://github.com/slowmist/Knowledge-Base

## 中心化交易所
+ [viabtc_exchange_server](https://github.com/viabtc/viabtc_exchange_server)


## 去中心化交易所
+ https://github.com/etherex/etherex
+ https://github.com/eostoken/ET-exchange/tree/master/etbexchange

<!-- 
+ https://openledger.io/
+ kijiji
 --># Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#%E6%95%B0%E7%BB%84%E6%98%AF%E5%80%BC%E4%BC%A0%E9%80%92)
    ```
    func main() {
        x := [3]int{1, 2, 3}

        func(arr [3]int) {
            arr[0] = 7
            fmt.Println(arr)
        }(x)

        fmt.Println(x)
    }
    ```
+ [输出什么](https://github.com/chai2010/advanced-go-programming-book/blob/master/appendix/appendix-a-trap.md#map%E9%81%8D%E5%8E%86%E6%98%AF%E9%A1%BA%E5%BA%8F%E4%B8%8D%E5%9B%BA%E5%AE%9A)
    ```
    func main() {
        m := map[string]string{
            "1": "1",
            "2": "2",
            "3": "3",
        }

        for k, v := range m {
            println(k, v)
        }
    }
    ```# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

# Sharding

## 1% Attack

+ 状态分片（智能合约状态同步&数据可用性相关）、交易分片（需要防止双花） 涉及 跨分片通信 和  状态交换，这里不进行讨论
+ 网络分片
    * 节点越多，速度越快吗？不是的
        - 每个节点都要验证所有的交易
        - 不可能三角: security, scalability, decentralization
            + 牺牲一定的 安全性，换取一定的 scalability?
                * 分片! 每个节点只需要验证部分交易
                    - 超级节点 vs 校对节点(collator)
+ PoW
    * security & reliability 好，但 scalability 差
    * 如果进行分片，比如分 100 片，那么分片上的算力只占 全网 1%，也就是说只需要 1% 的算力，就能完全控制该分片(1% 攻击)
        - 其实也有方案解决这个问题，monoxide 连弩挖矿，此处略过
+ PoS
    * 为什么 PoS 不会遇到这个问题
        - 将节点随机分配到分片，防止作恶者将算力汇集到某一分片
            + 无法选择被分配到哪个分片
            + 无法提前知道会被分配到哪一个分片
    * 这算是 ETH 为了分片想转 PoS 的一个原因# State Channel

+ https://github.com/ledgerlabs/state-channels/wiki
+ https://github.com/machinomy/awesome-state-channels
+ https://github.com/jtremback/universal-state-channels
+ https://github.com/SpankChain/general-state-channels

# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
# Miscellenous

+ 混币
+ 判断题
    1. 区块链上的数据默认加密的（F）
        - 解答：可使用 hex 工具查看
    1. 区块链上的记录都是真实的（F）
    1. 区块链记录的所有数据是不可篡改的（F） 
    1. 区块链不存在 DDOS 攻击（F）
    1. 加密数字货币都是使用区块链技术（F） 
    1. 比特币每个节点同步的账本都是全账本（F）
    1. 当最后一个比特币挖出来后，比特币矿工不需要继续挖矿了（F）
    1. 区块链的签名和加密技术一般采用对称加密技术（F）
    1. 所有区块链技术的每一个节点都是没有差别、都是平等的（F）
        - 解答：全节点 vs SPV轻节点
    1. 区块链等同于分布式账本（F）# 区块链工程师面试宝典
区块链工程师、区块链研究员、区块链架构师面试宝典。这个 repo 总结了一些区块链的基础知识，为各位求职者增加求职信心。推荐使用 [Gitbook 阅读](https://www.gitbook.com/read/book/chrislinn/blockchain-cheatsheet)。

---

DApps 开发参考: [awesome-dapps-source-code](https://github.com/HAOYUatHZ/awesome-dapps-source-code)
# Resources

+ [区块链行业词典-甲子光年 维京资本 2018.02.PDF](https://github.com/ChrisLinn/blockchain-cheatsheet/blob/master/docs/blockchain_dict-JiaZiGuangNian-Viking-2018.02.pdf)# Summary

* [README](README.md)
* [Bitcoin](bitcoin/readme.md)
    - [PR 学习](bitcoin/pr-review.md)
* [Blockchain](blockchain/readme.md)
    * [Attack](blockchain/attack.md)
+ [Explorer](explorer.md)
+ [随机性](randomness.md)
+ [DApps](dapps.md)
* [AltCoin](altcoin/readme.md)
    * [Ethereum](altcoin/ethereum/readme.md)
        * [Solidity](altcoin/ethereum/solidity.md)
        - [Attack](altcoin/ethereum/attack.md)
    * [Scrypt家族](altcoin/scrypt.md)
    * Ripple
    * [Filecoin](altcoin/filecoin.md)
    * Zcash
    * [EOS](altcoin/eos.md)
    * [Stable Coin](altcoin/stablecoin.md)
* [Wallet](wallet.md)
* [Mining](mining.md)
* [网络](network.md)
+ Layer 2
    * [Sharding](layer2/sharding.md)
    * [State Channel](layer2/state-channel.md)
* [GoLang](golang.md)
* [涉及密码学的编程](crypto-coding.md)
* [Exchange](exchange.md)
* [博弈论](game-theory.md)
* [Miscs](misc.md)
* [Resources](res.md)

# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
# Solidity

https://github.com/crytic/not-so-smart-contracts

+ 以太坊外部账户和合约账户的差别？ 
+ Solidity 中哪些变量是存放在 memory 中哪些是存放在 storage 中？
+ Solidity 中如何定义修改器和回退函数？ 

## Ethereum Security Tools
+ Slither
+ [tintinweb](https://github.com/tintinweb/) Visual Auditors
    * [Solidity](https://marketplace.visualstudio.com/items?itemName=tintinweb.solidity-visual-auditor)
    * [Vyper](https://marketplace.visualstudio.com/items?itemName=tintinweb.vscode-vyper)
+ [Surya](https://github.com/ConsenSys/surya)
    * provides information about the contracts’ structure and generates call graphs and inheritance graphs.
+ [Mythril Classic](https://github.com/ConsenSys/mythril-classic)
    * a disassembler, hacking tool and security analyzer for Solidity code, raw EVM bytecode and eWASM
+ [Scrooge McEtherface](https://github.com/b-mueller/scrooge-mcetherface/)
    * automatically exploiting the detected issues
+ [Karl](https://github.com/cleanunicorn/karl)
    * monitor for smart contracts that checks for security vulnerabilities using the Mythril Classic detection engine
+ [MythX](https://mythx.io/)
    * a cloud-based service that makes powerful smart contract security analysis to anyone
        - https://github.com/ConsenSys/truffle-security
+ [Panvala](http://www.panvala.com/)
+ https://github.com/melonproject/oyente
+ https://github.com/quoscient/octopus
+ https://github.com/ConsenSys/mythril
+ https://github.com/CryptoExperts/wyverse
+ https://github.com/Imanfeng/Blockchain-Public-Chain-and-Smart-Contract-Vulnerability
+ https://github.com/nvonpentz/solidity-exploits
+ https://github.com/ConsenSys/smart-contract-best-practices

## Testing
+ https://github.com/MichalZalecki/tdd-solidity-intro
+ https://github.com/sc-forks/solidity-coverage
# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
# Altcoin

所有的 Bitcoin 衍生币都属于 AltCoin, alternatives to bitcoin. 其中最著名的恐怕是 Ethereum.# Ripple

# Scrypt 家族 

__莱特币狗币撕逼史__: [转：盛世危言：scrypt系统崩溃已进入倒计时](https://bitcointalk.org/index.php?topic=763157.msg8598872#msg8598872)

## Scrypt 算法
比特币采用 SHA-256作nonce碰撞以挖矿。SHA-256 性能与内存空间没有关系，只与CPU处理速度有关，导致矿机泛滥。

Scrypt算法 __需要大量内存，内存硬件成本较高__ ，避免矿厂矿池低成本挖矿。

Scrypt算法会产生一个p个块元素的数组，p的值大概比2^31（42亿）小几个数量级，实际使用中可能是十万~百万级别. 对于每个块元素，都是进行一系列运算生成哈希值，最后对整个数组再进行PBKDF2-HMAC-SHA256运算得到最终结果。

Scrypt算法将每个元素都存放在内存中，最后才能算出正确的结果，从算法层面保证了对大量内存空间的硬需求，从而提高了运算成本。

2014年 crypt 算法ASIC矿机出现终结了显卡与Scrypt虚拟币的蜜月。

## Litecoin
+ 每2.5分钟而不是10分钟处理一个块，提供更快的交易确认。
+ 总币数是84000000是Bitcoin的4倍。
+ 调整难度的周期为3.5天而不是2周。

## Dogecoin
一开始基本上就是莱特币克隆，但特点在于小费文化、娱乐文化、慈善文化。

# Stablecoin

## Banchor
+ [深入浅出 Bancor 协议](https://learnblockchain.cn/2019/04/15/understand-bancor/)
+ [How Bancor Relays Work](https://medium.com/@natehindman/how-bancor-relays-work-c712a374374f)# tendermint

+ https://github.com/wupeaking/tendermint_code_analysis