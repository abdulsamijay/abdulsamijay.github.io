---
title: "Solutions to Ethernaut Challenges"
date: 2022-05-23 10:00
categories: [challenge-write-ups]
tags: [solidity, EVM, security, smart contracts, challenge]

---
### Introduction
[Ethernaut](https://ethernaut.openzeppelin.com/) is a wonderful website based game developed by [Openzeppelin](https://openzeppelin.com/) and played in Ethereum virtual machine. Each level is a smart contract that needs to be 'hacked'. <br>
This game provides opportunity to learn more about the never ending knowledge of the EVM based smart contracts & use this knowledge to secure or rescue actual contracts in live enviroment i.e Ethereum mainnet.

In this blog post, I'll share hints & descriptions of the challenges for which I have posted the solutions on github that you can find [here](https://github.com/abdulsamijay/ethernaut).

### Pre-requisite
I have solved these challenges in [foundry](https://book.getfoundry.sh/), a smart contract development framework. You can use also [hardhat](https://hardhat.org/). Before you start, you will need basic understanding of <br>
1. [Solidity](https://docs.soliditylang.org/en/v0.8.17/) language
2. Basics of EVM execution flow
3. Familiarity with [web3.js](https://web3js.readthedocs.io/en/v1.8.0/) or [ethers.js](https://docs.ethers.io/v5/)

### Challenge Spoilers!
### 1. Fallback
#### Description
In this challenge the user is provided with a contract called `Fallback.sol`. The goal of this challenge is to become the onwer of the contract & drain all the funds (Eth).
#### Hint
Call the `contribute()` function by sending it some eth & then call `withdraw()` method to drain all funds!
This challenge requires user to be familiar with the following concepts.

1. Fallback functions & when they are triggered.
2. Ownable & access control functionality.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/Challenge-1-Fallback)!

### 2. Fallout
#### Description
In this challenge the user is provided with a contract called `Fallout`. The goal of this challenge is to become the onwer of the contract & drain all the funds (Eth).

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Fallback functions & when they are triggered.
2. Ownable & access control functionality.
3. Attention to detail!

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-2-Fallout)!

### 3. Coin Flip 
#### Description
In this challenge the user is provided with a contract called `Coinflip.sol`. The goal of this challenge is win 10 times consecutively by calling the `flip()` function of the contract.
#### Hint
This challenge requires user to be familiar with the following concepts.
1. Pseudo-Randomness in computer systems.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-3-CoinFlip)!

### 4. Telephone 
#### Description
In this challenge the user is provided with a contract called `Telephone.sol`. The goal of this challenge claim the ownership of the contract.

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Difference between `tx.origin` & `msg.sender`.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-4-Telephone)!

### 5. Token
#### Description
In this challenge the user is provided with a contract called `Token.sol`. The goal of this challenge is to hack the the token contract.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-5-Token)!

### 6. Token 
#### Description
In this challenge the user is provided with a contract called `Delegate.sol`. The goal of this challenge claim the ownership of the contract.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. Solidity delegtecall function & storage layout.
2. Fallback functions.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-6-Delegation)!

### 7. Force
#### Description
In this challenge the user is provided with a contract called `Force.sol`. The goal of this challenge to send ether to the contract that has no ability to receive ether.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. Solidity `selfdestruct()` function from the docs!

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-7-Force)!

### 8. Vault 
#### Description
In this challenge the user is provided with a contract called `Vault.sol`. The goal of this challenge to unlock the vault.

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Storage or slot packing technique while contract creation.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-8-Vault)!


### 9. King
#### Description
In this challenge the user is provided with a contract called `King.sol`. The goal of this challenge to become the king in such a way that no one should be able to overpay & become the king.


#### Hint
This challenge requires user to be familiar with the following concepts.
1. Solidity fallback() funtion.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-9-King)!


### 10. Reentrance
#### Description
In this challenge the user is provided with a contract called `Reentrance.sol`. The goal of this challenge to drain the contract.


#### Hint
This challenge requires user to be familiar with the following concepts.

1. Checks-effects-interaction pattern
2. Re-entrancy attacks.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-10-Reentrance)!

### 11. Elevator 
#### Description
In this challenge the user is provided with a contract called `Elevator.sol`. The goal of this challenge is to set the variable top to true.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-11-Elevator)!

### 12. Privacy
#### Description
In this challenge the user is provided with a contract called `Privacy.sol`. The goal of this challenge is to set the variable locked to false.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. Storage or slot packing technique while contract creation.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-12-Privacy)!


### 13. GateKeeperOne 
#### Description
In this challenge the user is provided with a contract called `GatekeeperOne.sol`. The goal of this challenge is to set the entrant variable to `tx.origin`.


#### Hint
This challenge requires user to be familiar with the following concepts.

1. Difference between `tx.origin` & `msg.sender`.
2. How `gasleft()` is used in solidity.
3. How solidity type casting works.

Check out solution & walkthrough [here]()!

### 14. GateKeeperTwo 
#### Description
In this challenge the user is provided with a contract called `GatekeeperTwo.sol`. The goal of this challenge is to set the entrant variable to `tx.origin`.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. Difference between `tx.origin` & `msg.sender`.
2. Properties of `XOR` operations.
3. Contract creation mechanism & `extcodesize()` function.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-14-GateKeeperTwo)!


### 15. Naught 
#### Description
In this challenge the user is provided with a contract called `Naught.sol`. The goal of this challenge is to transfer the tokens before the timelock period.

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Basic working of ERC20 tokens.
2. Understanding of `approve()` & `transferFrom()` functions.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-15-Naught)!



### 16. preservation 
#### Description
In this challenge the user is provided with a contract called `Preservation.sol`. The goal of this challenge is to claim the ownership of the contract.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. How `delegatecall` & `storage layout` works in solidity smart contract.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-16-Preservation)!


### 17. Recovery 
#### Description
In this challenge the user is provided with a contract called `Recovery.sol`. The goal of this challenge is to claim the ownership of the contract.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. How a smart contract address is predicted or computed with `create()` & `create2()`.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-17-Recovery)!



### 18. Magic Number
#### Description
In this challenge the user is provided with a contract called `Magicnumber.sol`. The goal of this challenge is deploy a contract that returns '42' but only 10 opcodes are allowed no more!.

#### Hint
This challenge requires user to be familiar with the following concepts.

1. Compiler bytecode & opcodes. For reference visit, https://evm.codes
2. `Create2` & `Create` opcodes for contract creating.
3. Solidity assembly.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-18-MagicNumber)!



### 19. Alien Codex 
#### Description
In this challenge the user is provided with a contract called `AlienCodex.sol`. The goal of this challenge is to claim the ownership of the contract.

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Solidity Integer overflow/underflow.
2. Solidity storage layout.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-19-AlienCodex)!


### 20. Denial
#### Description
In this challenge the user is provided with a contract called `Denial.sol`. The goal of this challenge is to place a mechnism such that the owner cannot withdraw ether from contract even though it has it.

#### Hint
This challenge requires user to be familiar with the following concepts.
1. Solidity fallback() funtion.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-20-Denial)!


### 21. 
#### Description
In this challenge the user is provided with a contract called `Shop.sol`. The goal of this challenge is to set the variable price to be less than 100.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-21-Shop)!


### 22. Dex 
#### Description
In this challenge the user is provided with a contract called `Dex.sol`. The goal of this challenge is to drain `token0`.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-22-Dex)!


### 23. DexTwo
#### Description
In this challenge the user is provided with a contract called DexTwo.sol. The goal of this challenge is to drain token0 & token1 from the dex.

Check out solution & walkthrough [here](https://github.com/abdulsamijay/ethernaut/tree/master/src/challenge-23-DexTwo)!

### Conslusion
That's all folks. I hope this write-up helped you. See you next time.