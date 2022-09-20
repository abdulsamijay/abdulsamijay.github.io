---
title: "Major DeFi hack analysis & POCs"
date: 2022-07-16 10:00
categories: [hack-analysis]
tags: [exploit, defi-securiry, poc]

---

### Introduction
Defi hacks have become a major target for hackers to extract the value from the protocol with very little effort & cost. In the web3 space it is very economically feasiblt to attack larger platforms & disappear into the abyss of mixers.

Here I discuss some the major defi hacks that took place in the past. We will analyze them to figure out what exactly went wrong. What strategies attackers normally take after exploiting a protocol.

In this blog post, I have compiled 10 defi hack analysis & developed theri POCs for the purpose of understnding the mistakes that protocols made & learn from it to avoid this the future. While developing the POCs I cam to realize that many of the exploits included `flashloan` + `re-entrancy` combination along with some incorrectly handled `access control mechanism`. I have also seen a few recent hacks that took place but haven't created their POCs as they are pretty much the same to the exploits that I have written in this post. Going through the exploit repository you should be able to re-create those exploits as majority of them follows the same pattern. 

I would aslo like to cateforize some hacks:
1. `Compromised` or leaked private keys {Keep the damn thing safe!}
2. Incorrect implementation of `Access control mechanism` in smart contracts which leads to ownership takeover.
3. Incorrect handling of `checks-effect-pattern` which causes `Re-entrancy attacks`.
4. Lack of `signature verification`, this applies to contracts that store the signature hash & `cross-chain bridges`.
5. Unsafe usage of `Delegate` calls & `Proxy` mechanism.
6. `Oracle Manipulation` attacks.

[`Here is the repository for exploit re-creation`](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC). You can follow the insturctions for running the exploits on forked enviroment.

### Defi Exploit POCs
### The infamous DAOHack - The Classic Re-entrancy (2016)
This was a classic re-entrancy attack which led to the fork of `Ethereum` (ETH) & `Ethereum-classic` (ETC) chain split. There was a debate on whether to fork the chain or not back in the day. This was I beleive the first attack in which the attacker managed to withdraw `3,800,000` (3.8 Million) Ether!

The core of the attak was:
1.  The contract sent Eth to the person buying & deducted the balance after the transfer of Eth.
2. When the Eth is transfered to a function, it invokes the fallback function of the contract. Here the attcker used the opportunity to re-renter the function after depositing the amount.
3. The attcker repeatedly called the function in fallback & withdrew `3.8 Million Ether`.
4. Therefore, Use the check-effect-pattern to migitage risks as such!

See the [POC here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/The-Dao-hack).

### Beanstalk Finanace Hack (April, 2022)
Beanstalk protocol got hacked for around $74M through exploiting the governance mechanism & stealing all the BEANS & Curve LP tokens stored in the Beanstalk protocol. It is a bit complex hack, lets break it down step by step.

- [Hack Transaction](https://etherscan.io/tx/0xcd314668aaa9bbfebaf1a0bd2b6553d01dd58899c508d4729fa7311dc5d33ad7)

- [Hackers Address](https://etherscan.io/address/0x1c5dcdd006ea78a7e4783f9e6021c32935a10fb4)

- [Hacker Exploit Contract](https://etherscan.io/address/0x79224bc0bf70ec34f0ef56ed8251619499a59def)

- [Beanstalk Proposal Transaction](https://etherscan.io/address/0xe5ecf73603d98a0128f05ed30506ac7a663dbb69)

1. The attacker starts by taking a flashloan of $1 Billion from AAVE v2 containing the following assets.
    - `350,000,000` DAI
    - `500,000,000` USDC
    - `150,000,000` USDT
2. The attacker takes yet another Flashloan from uniswap v2 for `32,100,950` BEAN & Sushiswap for `11,643,065` LUSD.
3. Then the attacker deposits DAI, USDC & USDT to Curves 3Pool (DAI/USDC/USDT) to get `979,691,328` 3Crv tokens.
4. Exchange `15,000,000` CRV tokens to `15,251,318` LUSD on BEANLUSD-f pool.
5. Add single asset liquidity 964,691,328 CRV to get `795,425,740` BEAN3CRV-f.
6. The attacker deposits `32,100,950` BEAN & 26,894,383 LUSD to get `58,924,887` BEANLUSD-f
7. The user then deposits BEANLUSD-f & BEAN3CRV-f to beanstalk contract to get enough voting power.
8. The attacker calls Diamond.vote(18) At this point user has control over 66% of voting power.
9. The proposal gets executed by calling the Diamond.emergencyCommit(18) function on beanstalk protocol which send back the following tokens back to the exploit contract.
    - `36,084,584` BEAN
    - `0.540716100968756904` UNI-V2 ETH/BEAN.
    - `874,663,982` BEAN3CRV-f.
    - `60,562,844` BEANLUSD-f.
    - `100` BEAN minted to the exploit contract.
10. Removes `874,663,982` CRV single liquidity to get `1,007,734,729` CRV tokens.
11. Removes `60,562,844` BEANLUSD-f single liquidity to get `28,149,504` LUSD.
12. Returns flashloan of `11,678,100` LUSD to Sushiswap.
13. Returns flashloan of `32,197,543` BEAN to Uniswap V2.
14. Exchanges `16,471,404` LUSD to get `16,184,690` CRV on LUSDCRV-f.
15. Removes liquidity from `511,959,710` 3CRV Pool to get `522,487,380` USDC, `358,371,797` DAI, `156,732,232` USDT.
16. Returns flashloan on aave for 350,315,000 DAI, 500,450,000 USDC & 150,135,000 USDT.
17. Removes liquidity on `0.540716100968756904` Uniswap V2 to get `10,883` Eth & `32,511,085` BEAN.
18. Donated `250,000` USDC to Ukraine Donation Wallet.
19. Swap `15,443,059` DAI to `15,441,256` USDC on Uniswap V3.
20. Swap `37,228,637` USDC for `11,822` Eth on Uniswap V3.
21. Swap `6,597,232` USDT for `2,124` Eth on Uniswap V3.
22. Leaving the attacker with over `24k Eth` ~ `$72M` in profit.

#### THE EXIT STRATEGY
The hacker used tornado cash & split the ~24k Eth into chunks of 1, 10 & 100 Eth to disappear in thin air. One thing to note is that this hack was a result of a bad governance design and not the economic design.

See the [POC here](https://github.com/abdulsamijay/Beanstalk-Exploit-POC)

### Rari-Capital Fuse Hack (April, 2022)
Rari capital got hacked for around $79M through a classic re-entrancy attack. Rari is a fork of compound finance which had this bug fixed earlier. It is not the first time Rari has been a victim of a hack.

#### Pre-requisite
1. Rari is a fork of compound finance & compound had a known issue of re-entrancy attack whenever CTokens were borrowed through `borrow()` function.
2. This was patched by the Rari team by introducing a pool-wide re-entrancy guard on CTokens.
3. There also exists a component called “comptroller” which is responsible for functions such as providing & withdrawing collateral by calling `enterMarkets()` & exitMarket respectively.
4. The comptroller contract did not have re-entrancy checks in place. The attacker exploited through the `exitMarket()` function which makes the deposited asset no longer a collateral meaning it can be withdrawn at any time.

#### The Exploit
The attacker created 2 contracts.

1. [For Exploiting Rari Fuse Pools](https://etherscan.io/address/0x32075bad9050d4767018084f0cb87b3182d36c45)
2. [For Receiving Profits after exploits](https://etherscan.io/address/0xe39f3c40966df56c69aa508d8ad459e77b8a2bc1)

There were 7 pools that were affected due to this exploit `(8，18，27，127，144，146，156)`

1. Attacker took flashloan of `50,000` WETH & `80,000` WSTETH from Balancer vault
2. Attacker deposited `80,000` WSTETH collateral into fWSTETH-146 pool.
3. After depositing, the attacker borrowed `2397` ETH from fWSTETH-146 pool without updating the borrowers record.
4. The pool triggers the fallback function of the exploiter contract while sending ether to the exploit contract where the attacker makes a re-entrant call to `exitMarket()` & withdraws his collateral of `80,000` WSTETH.
5. The attacker receives 2397 ETH for free & transfers it to another contract for later claiming.
6. The attacker repeats steps 1–4 until all borrowed amount is collected.
7. The attacker applies the same strategy on 7 different pools & runs away with `~$79M` of profit.

See the [Rari-Capital POC here](https://github.com/abdulsamijay/Rari-Capital-Exploit-POC)

### Wintermute Multisig Hack (June, 2022)
This is attack that took place on `OPTIMISM` network. This POC continously creates proxy contracts to create wintermute multi-sig that had not been deployed to `optimism network` & only existed on ethereum. The attacker took advantage of this as the `OPTIMISM` team gad already sent around `20M OP` tokens to this contract which hacker took custody of.

The attacker recursively generated addresses through the gnosis multisig until the address was generated allowing him to deploy contract at that particular address & retrieved the tokens.

See the [Wintermute POC here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/Optimism-Wintermute).

### XCarnival NFT Lending Protocol Hack (June, 2022)
A hack that took place on Ethereum Block `15028861`, where the hacker walked away with around `$3.8M` in ETH exploiting the XCarnival NFT Lending protocol.

1. The attacker uses the `pledgeAndBorrow` function in the XNFT contract to use NFTs as collateral and borrow xToken.
2. Then, a call is made using the `withdrawNFT` function in order to extract the pledged NFT. This function first checks to see if the order has been closed. If it hasn’t, it checks to see if the NFT hasn’t been withdrawn and the loan amount is 0. If none of these conditions are met, it then determines that the NFT has not been withdrawn and the order is still active (no debt). NFTs that are used for collateral can be withdrawn.
3. The attack itself starts when the attacker uses the order to directly call the `borrow` function in the xToken contract.
4. Since the contract assumes the NFT has been already returned allow the attcker to withdraw more funds.
5. The essence of this vulnerability is that there is no check while borrowing to see whether the NFT in the order has been withdrawn, allowing an attacker to borrow without repayment after removing the NFT for their own benefit. 

See the [XCarnival POC here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/XCarnival).

### Pickle Finance Hack (Nov, 2020)
On 21st November 2021, Pickle finance was hacked, where an attacker was able to drain $19M DAI from the pDai jar. The attack exploited multiple inconsistencies & flaws in the logic of the pickle jar smart contract.

#### Pre-Requisite:
1. Pickle Jar contract had a function `swapExactJarForJar()` which was meant to be generalized to bring more flexibility to the protocol. However, the attack could have been prevented if the function checked for whitelisted ones. 
2. The attacker’s jars contain minimalist functions to function as a jar and since the user controls Jars most of the checks can be easily bypassed.

#### The Exploit
The user-created two Jar contracts
1. [Attackers Address](https://etherscan.io/address/0x2b0b02ce19c322b4dd55a3949b4fb6e9377f7913)
2. [Attack Transaction](https://etherscan.io/tx/0xe72d4e7ba9b5af0cf2a8cfb1e30fd9f388df0ab3da79790be842bfbed11087b0)

The attacker deploys two new fake Jars.
- [First Jar](https://etherscan.io/address/0x75aa95508f019997aeee7b721180c80085abe0f9)
- [Second Jar](https://etherscan.io/address/0x02c8364546ec849e1726fb6cae5228702b111ee6) <br>

1. The attacker calls strategyCmpdDaiV2.`getSuppliedUnleveraged()` which returns the amount of DAI available i.e `19728769153362174946836922` ~ `19M.728` DAI.
2. The attacker calls swapExactJarForJar the first time, supplying fake Jar addresses created earlier which withdraws deleveraged invested DAI from the compound back to pDAI Jar. 
3. The Attacker calls `earn()` function 3 three times on pDAI (Pickling Dai) minting cDAI to StrategyCmpdDaiV2 contract.
4. The attacker deploys another two fake Jars & a fake underlying.
    - [Third Jar](https://etherscan.io/address/0xa2da08093a083c78c21aeca77d6fc89f3d545aed)
    - [Fourth Jar](https://etherscan.io/address/0xa445e12d69e8bd60290f6935d49ff39ba31c6115)
    - [Fake Underlying contract](https://etherscan.io/address/0x8739c55df8ca529dce060ed43279ea2f2e122122)

5. Then the attacker calls `swapExactJarForJar`, this time passes in the third & fourth Jar with crafted data that makes a function call to curve proxy in the context of the controller-v4. Since the attacker has crafted the Jar to work with the contract it bypasses checks to the point where arbitrary code is executed in the context of the controller-v4 contract. Then `withdraw()` is called to withdraw `950,818,864` cDAI to controller-v4.
6. The withdrawn cDAI are deposited to the fake Jar through `deposit()` and all cDAI is transferred to the attacker.
7. The attacker calls `redeemUnderlying` on the compound to convert all cDAI to DAI & walks away with `~19M DAI`.

Here is the [Pickle Finance Hack POC](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/blob/master/src/pickle-finance)

### Harvest Finance Hack (October, 2020)
Harvest finance got hacked for around `$34M` due to a flashloan attack which `manipulated the price` in the Curve pool to retrieve more USDT tokens than originally deposited USDT amount in fUSDT pool. This attack was also possible on other f-pools using the same set of steps described below. But the attacker chose not to continue. If the attack had continued, the attacker would have walked away with `~$400M` worth of assets.

1. The attacker deploys a contract & pre-funds it with `10.69M` USDT & `11.435M` USDC 
2. The attacker took flashloan of `50M` USDT from the Uniswap v2 USDT-WETH pair.
3. The attacker then swaps `11.425M` USDC for `11.407M` USDT. Now the contract has `60.66M` USDT.
4. A total of `60.66M` USDT are then deposited to the fUSDT pool to get `71668595794204` fUSDT tokens.
5. The attacker then swaps `11.437M` USDT back for USDC.
6. The attacker withdraws the deposited fUSDT to claim `61.1M` USDT which is more than what was originally deposited i.e `60.6M` USDT.
7. Gaining profit of approximately `0.5M`.
The attacker repeatedly called steps 3-6 4 times to gain profit.

See the [Harvest Finance Hack here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/harvest-finance)


### Inverse finance Hack (June 2022)
Inver finance was hit by the attcker in mid June 2022, and is the classic example of oracle manipulation. The attackers managed to steal ~6M worth of tokens.

I have omitted the detailed steps for the eploit which I belive are easily understood by taking look at the code. As hervest finance attack was similar to it.

Check out the [Inverse Hack POC here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/Inverse-finanace)


### Honorable Mentions
### The infamous 'Accidently killed it' (July, 2017)
A person was experimenting with the newly deployed wallet for parity. And this person accidently killed it be becomming the owner & calling `self-destruct` on the contract. Whether it was intentional or a mistake remains a mystery!
Though this was not exactly a hack but it qualified in my honorable mentions list.

Here is the github [issue](https://github.com/openethereum/parity-ethereum/issues/6995). 

See the [TheParityKill POC here](https://github.com/abdulsamijay/Defi-Hack-Analysis-POC/tree/master/src/The-Praity-Kill)