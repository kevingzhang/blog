---
title: "Trustless Serverless Dapp"
date: 2019-12-17T10:11:41-08:00
draft: true
---
![](https://algorithmia.com/blog/wp-content/uploads/2018/03/Serverless-architecture-backend-web.png)
# Do not expect dApp is ready today, there are missing pieces

TCP/IP was invented around the 1970s, Database 1980, but web app and mobile internet become popular around 2000. It takes about 30 years. Bitcoin was invented in 2009 which is so far the most popular blockchain application, There is no way for us to expect decentralized apps to get popular within 10 years?  It will take some time to get all the pieces in the tech stack to get invented. 

# Existing 3 layers cloud app tech stack

Before we get into those missing pieces in dApp tech stack, let's review what we have in existing internet tech stack.

A typical internet app has 3 layers. Client side (browser or mobile app client), server side and database layer. In the cloud era, most of servers and database are deployed to the cloud service provider such as Amazon AWS. In most cases, static resources, such as html/css/js code and mages are loaded from CDN to speed up the loading process. But the dynamic content still need to be calculated at server side. In many cases the server logic is close connected to database layer. That's why the best practice is to deploy server instances close to database instances. 

# Serverless and Function-as-a-Service

A recent trend is the Serverless or Function as a Service. One of the major reasons of moving to serverless is that server instance utilize. Depends on how busy your servers are, in many cases your server is just idle waiting for client's requests. Although we can reduce the number of instances of server cluster to reduce the cost, we still need to maintain a minimized running instances to deal with possible short burst of client requests. Provisioning a new instance is kind of slow, you cannot just increase a few instances in few million seconds when new client requests come in. Wait a minute? what if we can? Right, that is how serverless come in.

Serverless is not actually "server" "less", it means you do not "occupy" a server when idle. You share a server with thousands of other applications and your function is loaded and run only if your client has a request comes in. I know serverless has other meaning, but I just make it as simple as possible. That's why serverless is sometime called "Function as a Service", because as a developer you do not actually rent and deploy your code to this server instance, instead you upload your code to a function as a service provider, let them handle the request. Of course, you will need to design your application logic to be able to "pure" functional. Pure functional means stateless, all the states have to be stored in some kind of fast database, not in the memory of your instance, because you do not even own an instance, you just own your code and few million seconds of the CPU running time. That is it.

Serverless technology allows the code to be cold started much faster than provisioning a cloud server instances. However if your function is really "cold" it still need a relatively long time to cold start. Really "Cold" means either the first time deloyment or idle for a very long period of time. Here is a article about [Cold start time](https://mikhail.io/serverless/coldstarts/big3/). Usually when your code is still "warm" you won't worry too much about the start time. It is usually between 1 and few seconds. 

If you are very picky on fast response, we can still move one step futuer to reduce the few seconds latency. 

# CDN Serverless Worker

CDN is widely used because it is fast. It is fast become it is close to the end user. CDN providers usually owns more data center across over the world. When you request a static resource, CDN will find your nearest (network distance, not geolocation) CDN server to feed the resource back to you. 

If CDN is fast, why do not we deploy my function code to nearest CDN server, so that I can get fast response. You are right, and someone is doing so, for example [CloudFlare Worker and Serverless Framework](https://www.cloudflare.com/learning/serverless/glossary/serverless-and-cloudflare-workers/). But wait a minute, did I say the server would be better to be deployed somewhere close to your data storage? It is easier to deploy your function code to CloudFlare's CDN server to handle those function do not require database access (one example is verify signature or hash function). How about those functions designed to process data? Can you also move data to those CDN instance near me? Good suggestion, this is the first main topic for today - Decentralzied database

# Decentralized database

Under the hood, blockchain is mainly an append-only distributed database without a central management. Miners can append new state (called block) to the database via some kind of consensuses in between. Because there is a consensus, all other miners agree on the new state and update their own database to up to date. Well, there are more to talk about blockchain but we just focus on our main topic today, do not get side track. 

So if we deploy block nodes to major CDN providers. There is always a node near you and provide the function and data together as long as the following conditions are met:

- You can trust the node, or there is some kind trustless that you do not need to trust the node. The node will run your code in a trusted environment and behavior as you expected without compromise or data leaking. 
- You are tolerance the slightly delay caused by the network syncronization on database. because it will take some time for your nearest node to get latest block update. Well, all distributed system has delay, it is just a matter of your tolerance.
- You and your app's client understand the whole system is running decentralzied. That means no one, including you and the miners have no responsibility and cannot revert anything wrote to the database. It is append only. For example if your clients lost their private keys, they are gone, period.
 
# the dApp tech stack overview

Well, this seems to be one of the tech stack of decentralzied application, so called dApp. Let's recap what we discussed:

- You write your code in pure functional way. Basically your code do two kind of tasks: a) Based on client query, check the distributed database (the blockchain) and response to your client. b)Based on client's command creating a new transaction send to the miner. The miners are responsible to broadcast ovedr the network and run consensus to update global state in new block.

- You store you code (if need to compile, ideally compile to Web Aseembly. We will discuss why we go Web Assembly in detail later) to the blockchain. If the code is too big, it is OK to store the hash which other miners can download your code from IPFS. You do not deploy it anywhere. In other word, you have no control at rum time of your code. No one have.

- The node need to get some kind of certificate or so-called proof-of-trust so that clients will know this node is safe to get connected. This kind of certificate must be issued decentralizedly. That means no one can fake a certificate by compromse the issueing center.

Let's go through them one by one see what we have by the end of 2019.

## Using Tendermint / ABCI to separate your dApp logic from blockchain infrastructure

You are supposed to build a dApp not a whole new blockchain, right? So why bother those complicated blockchain infrastucture? Let those experts handle it. Before 2019, we actually have no solution for this but now it is much better. One of the solution to be considered is ABCI from Tendermint or Cosmos. You can either build your full dApp stack ground up with only suppport to ABCI, or, build on top of Cosmos SDK by leverage Cosmos' existing modules to save your time. With the help from Cosmos, you will focus on those 2 things I mentioned earlier - just in case you forgot - Query and Command. This remind me a programming pattern called CQRS. Let Tendermint blockchain infrastructure to maintain consensus and p2p network.

## If possible use Rust or any language that compiles to Webassembly to write your code

Technically you can write your code in any language since you only talk to Tendermint using ABCI. Even Cosmos itself is written in Golang. However there are additional advantages if you follow what I said. Regardless the memory safety from Rust, if your code can be compiled to WASM, the new widely adopted execution binary format can be running almost anywhere - not your grandpa's Java - I mean it. You can even store your compiled WASM binary into the blockchain just like Solidity smart contract of Ethnereum. Since it is compiled to binary, it is much smaller than text code. WASM can be loaded into TEE thanks for [Enarx](enarx.io) project. We will dive deeper into TEE shortly. There are many programming languages can compile to WASM format. C/C++, Rust, Golang, Typescript... you name it. There are more and more languages start to support WASM very month. Oh, I forgot to mention WASM runs in almost all browser too. Did I say "anywhere", I mean it. 

## You will need Trusted Computing or Trusted Execution Environment

This is the hardest and most important part. You will need some kind of trust to let your client send their privacy information to somewhere your code is supposed to run correctly. Sounds easy? Well, probably not. Human is complex animal because human can lie. Silicon chips cannot lie, but the software running on them are made by human, so software CAN lie. Modern software is so complicated that no human being can verify each line of code to make sure it works as expected. When hacker attack a piece of software, hacker can compromise the system to make it looks like running as usual and response as expected when dealling with verifiers. But under the clam surface hackers control the software doing something malicious that no one know. Since human cannot verify software line by line, human make some software to monitor or verify other software, eg. anti virus or security software. Since those so-called security software is still software, it can be compromised too, and even worse, it will pretend to be health even after compromised. Due to these reasons, we cannot simple trust a computer. We need some kind of higher level root of trust to help us. Here comes Trusted Computing and Trust Execution Environment technologies. 

Both of those two technologies rely deeply on hardware (TPM or CPU) which is harder to compromise than software. Even so, we still need to verify a node is trustable or not from other known trusted machine. Well, the question become which machine is considered "trustable". Actually no single machine is trustable even the one you are currently use reading my blog. The solution is using consensus. If you know Bitcoin or other blockchain technologies, you will know in Bitcoin network you do not trust any single node, you trust the consensus only based on the 51% assumption. For other consensus, such as BFT, there is another assumption like no more than 1/3 malicious nodes. We can use the same assuption and consensus in our trusted network. Let's assume we use Tendermint (based on BFT) as long as more than 2/3 nodes are trusted, we will know the result can be trusted. That means a malicous node will be found and kicked out. That's why we need a platform built on top of hardware rooted Trusted Computing + Blockchain.

# How does our dApp tech stack look like

Let's review our tech stack from bottom up. Starting from the silicon chips.

## CPU and TPM

As the root of trust, we can either use TPM or CPU's TEE. There are pros and cons of using TEE:
- Code running inside TEE is protected by CPU manufacture. No one outside of the TEE knows what happen inside, even the RAM and OS
- TEE has size limitation. Complicated code running inside TEE is low effecient. 
- CPU is expensive, also vendor locked in is also something to be considered. Different CPU vendor has its own spcial TEE. Good news is that Enarx is working on a substrate layer to make differnet TEE run the same WASM code without modification. 

On the other hand, TEE also has its own pros and cons:
- TPM is cheap but slow. It cannot protect the system instead provide evidence so that software can run remote attestation to verify. So we will need to design a thicker stack to work with TPM, starting from CPU booting, BIOS/UEFI to OS and application layers. This is complicated.
- 