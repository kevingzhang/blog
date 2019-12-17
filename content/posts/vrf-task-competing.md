---
title: "VRF Task Management and Consensus"
date: 2019-12-16T16:31:59-08:00
draft: false
---

I listed several principles in my previous post. Let's discuss them one by one. 

# How blockchain solve this kind of issue?

BTC and other Blockchain projects gave us a good example of using randomness. BTC uses the PoW (Proof of Work) consensus to generate the randomness. No one can predict which miner will win the next block in the hash power competition. An attacker has to own more than 51% hash power to attack the BTC network, which means the attack cost is super high. So far BTC is still one of the most secure financial systems after running for more than 10 years without any central control. However, to maintain such security also comes with a huge cost. It wastes so much electricity just doing SHA256 meaninglessly. 

# VRF is better

AlgorRand's VRF is a good alternative to PoW. We can still have randomness without consuming much power. VRF stands for Verifiable Random Function. Using VRF,  a node can generate a random hash and a proof from an open seed and his *private* key. Others can easily verify if this node is lying by verify the random proof against this node's *public* key and open seed. VRF is deterministic as well as random. For any given open seed, any node (assume has one pair of public/private key pair) can generate one unpredictable random and proof. And it is very easy to get verified by any others who know the public key and open seed. 

# How are we going to use VRF?

In our P2P network, when there is a task broadcast to the network, the hash of this task and current block hash is considered to be the known open seed. Every node can try its luck by computing random and proof using VRF.  If random is less than a particular number, this node is the winner. Other nodes can verify whether or not it is lying. There could be more than one node selected to join a committee using an additional Sortition algorithm. Inside this committee, members run a consensus to decide who is executor, others are verifiers. They work together to complete the task and share the revenue (gas fee).

# Demo video and explanation

I made a demo in the summer of 2019 showing how it works. Click to view the youtube video.[![Demo](https://img.youtube.com/vi/ZQiHnaG2sUk/0.jpg)](https://www.youtube.com/watch?v=ZQiHnaG2sUk).

In this video, I made a simulator and a test use case. Bob and Alice do not trust each other, but Alice wants to use Bob's code to process her picture. If Bob deploys his code (function per se, a lambda) to the cloud services like what most developers do today, technically Bob has the access right to his server instance and download Alice's picture. Bob doesn't want Alice to run his code on her computer either, because Bob's concerning Alice will get his source code. The solution is this: Bob encrypted and upload his code (in Python) with a description JSON (which docker image can and how to run his code) to IPFS. Alice encrypted and upload her image to IPFS too. Alice posts a task in the P2P network with a payment stub. Any node in the network can try to win this task and earn the money by running VRF. The winner executor will show to Alice and Bob VRF proof and attestation report from other verifiers (also elected using VRF). The attestation confirmed that the executor is running Bob's code in a Trusted Execution Environment (TEE). Alice and Bob trust TEE, and give their decryption keys to execution in encryption way. Executor's TEE can decrypt the keys, use these keys to decrypted Bob's code and Alice's image downloaded from IPFS. Execute the code get processed images back to Alice. When execution completed, based on Alice and Bob's requirements, all trace information is removed. After verifiers remote attestation, all of the committee members will share the gas fee. 

Sounds completed eh? Well, we will have simplified versions in my future post, but let's just follow the logic for now. 

# Threat models

Let's discuss the threat models and concerns we are trying to protect.

## The executor is malicious. 

He owns the node and wants to get the data running inside. 

TEE and TPM are designed to prevent this. I planned a lot of posts talking about them but for now, let's just briefly go through the result. 

Trusted computing and TEE technologies prevent anyone including the owner of the machine to get the information running side the system. Technically you own the machine like a black box. You know it is working but you won't know what it is running. Temper and hardware to get information is either very hard technically or very expensive economically, and also easy to be detected by other verifiers by remote attestation. Bad behavior will cost financial punishment. 

## Bob is malicious

Bob is the developer. He can write malicious code in his image processing function to steal customer's images through some kind of back doors. The backdoors are either a network or local temp storage.

Bob's code can only run inside a docker container.  If the docker engine is not compromised, the docker engine can limit the container's behavior. For example, if data leaking via the network is a concern, the executor can start a docker container with a "None" network. If save files into temp folder in hard drives is a concern, the docker engine can eliminate the disk access by "Volume" control or totally disable volume, use RAM instead. Furthermore, if restore to the clean state is a requirement, the remote attestation verifier can request to inspect the hard drive revert back to the pre-task state. In order to do this, [NixOS](https://nixos.org/) is a very good operation system we can use. We will have a separate blog post talking about NixOS and Nix package system. 

This design not only protects Alice's privacy data but also protect executor's node from damaged by Bob's potential malicious code. 

We will discuss the limitation of Docker containers and future WASM use cases in our future blog post.

## Alice receives the processed image but refused to pay her gas fee

All tasks require some kind escrow and paid upfront before any node would like to take. Alice is free to broadcast a task without any proof-of-payment, however, her transaction is likely rejected by blockchain miner at first, or no node would like to even try VRF. In this case, Alice lost at least the gas fee to post her task but did not get anything, so she is not likely doing so.

## Bob's code have infinity loop and Alice's image file is a poison bomb

This is runtime error which can be hardly detected before execution. In this case, Alice will run out of the gas limit and did not get anything. Alice is not motivating to do so, but she lost her gas fee assume it is Bob's code bug. The record will be stored in the blockchain. The bad record will give Bob a bad historical record and defeat new customers to use Bob's service. No matter what happened, the executor and verifiers can still get their salary as long as they have no fault on this task.

## Verifiers did not actually do the attestation just lie to get salary

There is a group of verifiers who are doing remote attestation individually. Each of them will get an additional big bonus if he can detect any other verifier or executor bad behavior.  Given the election is random, there is no way to collude in advance. If any verifier cannot detect executor bad behavior but others can, he will be punished after consensus. 

# Known issues of this solution

## Side-channel attacks or any other physical attack

## Consensus too slow




