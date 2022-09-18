---
title: "A Roller-Coaster Ride to Open-Source Contribution in Ethereum 2.0"
date: 2022-01-24 10:00
categories: [open-source]
tags: [open-source, eth 2.0, client, ethereum]

---

### Introduction
I want to share our roller-coaster ride exploring the Ethereum client called [Geth](https://xord.com/research/zooming-out-of-geth/), written in Golang. At Novon, we have a dedicated and passionate team of developers & researchers that aims towards helping & contributing to existing blockchain infrastructures. We will be focusing on the development division in Novon, which is actively looking & working on layer 0 to suggest improvements & implement exciting features for the blockchain community to use. <br>
We made contribution on [Eth 2.0](https://ethereum.org/en/history/) client [Lodestar](https://github.com/ChainSafe/lodestar). [Check this out!](https://github.com/ChainSafe/lodestar/pull/3502)

### How did we get Started?
Being in the blockchain space & building exciting DApps over it for quite some time, we realized there is more to the blockchain. Having experience in the application layer, we wanted something more challenging to throw us out of our comfort zone. So we decided to move to the layer below it, layer 0. 

We immediately aimed to understand Geth (go-ethereum), an ethereum client written in Golang. It has been written by the awesome developers for more than six years & is being maintained to date. It was a fascinating decision, but immediately we realized a challenge, learning 'Golang.' We did not have experience in that language before but keeping the principle in mind "Logics for all languages are approximately the same, the only hurdle is the syntax for that language" we quickly hopped over to golang to acquire a new language skillset. After a few articles, golang tour, & youtube videos, we were ready to dive into the Geth codebase. 

For the developers looking at code that's written over the course of six years with technical documentation, it's challenging to get anywhere, especially with a massive codebase. At first, we looked at the organized folder names explaining each component of the geth, each folder containing tons of files & links to different files. However, it was not possible to entirely go through each & every folder, so we came up with the strategy for studying geth.

We decided not to consider every file & folder while exploring. It was to keep us focused & not get overwhelmed, which worked out pretty well. We targeted the entry point of geth by having logs placed at many places within the code to run customized geth using our custom commands for our satisfaction. We considered using delve, an excellent debugger for golang to debug most parts of the geth.

Look for more articles on how geth works at the core. I would like to thank Kelvin Fichter & Optimism team for sharing their video on the internals of geth. It was a huge help.

1. Folders of important blockchain components.
2. Peer-to-peer network communication.
3. How web3 actually works under the hood.
4. Working of EVM interpreter.
5. Block propagation & consensus 
mechanism.

One challenge was to work with the peer-to-peer side of things. Go debugger was not helpful in that regard. We manually had to figure out the working of p2p by deploying a local testnet & by connecting peers (That's a story for another day). We also wrote a guide on how to connect two local nodes of geth & start your own custom blockchain—an article on that soon.

Once we had these things figured out, it became super easy to understand the flow of the application. Another resource that helped us build an understanding around geth is this GitHub repo to connect the overall picture of geth.

We documented each step & realized any new developer stepping into this realm would face the exact same set of problems as we did. So we decided to write a guide exploring geth to enable easy onboarding of a developer to understand how exactly ethereum works at the core & not waste any time by reinventing the wheel. Our team is actively working on this & we will be sharing these articles soon.

### More Geth Forks?
We had heard that there are many different EVM-based blockchains which are essentially all forks of geth. So we decided to take a look at that too. One blockchain that instantly comes to mind is the Binance smart chain (BSC); for our curiosity, we wanted to see what code has been changed & to what extent, but we simply couldn't compare the codebases & see exactly what is going on. While tackling this, a thought hit my mind about there must be some software that compares two folders & voila, we came across MeldMerge. It allows you to see the changes & compare the folders file by file. It opened up a whole lot of possibilities for exploring other codebases. We immediately thought of Optimism, Arbitrum, Polygon (Matic) & Binance smart chain. Our minds were flowing with excitement. We applied this tool to BSC and discovered a few changes & their integration with the Binance chain, their genesis block configuration & the reason why Binance is cheaper (fixed gas opcodes).

### Exciting Discovery!
One of my teammates pointed out that if we run binance smart chain's version of geth with the default parameters, it runs ethereum mainnet; interesting!. We thought this would apply to all forks but didn't check them out.

Another exciting thing was to discover the hardcoded addresses & contract addresses after the DAO attack on ethereum in late 2017.

### The Decision
Now we had understood most of the things & after a continuous learning period of 1 month, we decided to look for areas that could be improved within geth or features that could be implemented. We briefly looked at EIP-1559 implementation & the frequency of updates & patches to the geth repository. The devs were very active & there was very little room for contribution as the software is being maintained by the developers having years of experience in Golang & maintaining geth.

We had two options to proceed.

We enhance our golang skills & look for contributions in geth or other forks of geth which seems promising.

Eth 2.0 was just around the corner; since this is actively being evolved & changes are frequently being made to Eth 2.0 specs the chances of contributing to that software were much higher & the codebase is just three years old with a much better structure. And the community was very active.

We decided to move on to the Eth 2.0 client & continue releasing the articles on geth for easy onboarding of other developers trying to move into this space.

### Was it Futile to Learn Geth?
I’d argue, No. This was a huge learning experience for all of us. It developed our mindset for open source contribution produced a sense of designing system architecture with a peer-to-peer network.

It was a great opportunity for us to learn a new language & apply it to a real-world application. It was also a chance to get in touch with relevant people in the space for queries & suggestions.

### Next-Stop: Lodestar
Eth 2.0 client is written in many different languages. We are experienced in Typescript. A client named Lodestar, is written by the amazing team at ChainSafe for over three years. Their documentation was super simple; all our newly learned p2p skills significantly made sense. Also, ChainSafe is involved more in layer 0 architecture development; some of their projects include a client for Eth 2.0 (Lodestar), Polkadot (Gossamer) & Mina protocol, to name a few.

We prepared ourselves to dive into another ocean of wonderful opportunities. Going through the code a lot quicker & it immediately started to make sense. It didn't take us long to grasp the basic flow of the application. A teammate of mine pointed out an open issue on Lodestar, which was related to implementing a "remote signer" API, which calls an external API to sign an attestation without storing a private key on the validator machine, as specified in [EIP 3030](https://eips.ethereum.org/EIPS/eip-3030). What do we do next? Create a Pull Request implementing this feature and it got recently merged! And that is how we made our first contribution to Eth2.0.