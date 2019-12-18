---
title: "Trustless Serverless Dapp"
date: 2019-12-17T10:11:41-08:00
draft: false
---

![](https://algorithmia.com/blog/wp-content/uploads/2018/03/Serverless-architecture-backend-web.png)
# Do not expect dApp is ready today, there are missing pieces

TCP/IP was invented around the 1970s, Database 1980, but web app and mobile internet become popular around 2000. It takes about 30 years. Bitcoin was invented in 2009 which is so far the most popular blockchain application, There is no way for us to expect decentralized apps to get popular within 10 years?  It will take some time to get all the pieces in the tech stack to get invented. 

# Existing 3 layers cloud app tech stack

Before we get into those missing pieces in the dApp tech stack, let's review what we have in the existing internet tech stack.

A typical internet app has 3 layers. Client-side layer (browser or mobile app client), server-side layer and database layer. In the cloud era, most servers and databases are deployed to the cloud service provider such as Amazon AWS. In most cases, static resources, such as HTML/CSS/js code and mages are loaded from CDN to speed up the loading process. But the dynamic content still needs to be calculated at the server-side. In many cases, the server logic is closely related to the database layer. That's why the best practice is to deploy server instances close to database instances. 

# Serverless and Function-as-a-Service

A recent trend is the Serverless or Function as a Service. One of the major reasons for moving to serverless is that server instance utilize. Depends on how busy your servers are, in many cases your server is just idle waiting for the client's requests. Although we can reduce the number of instances of server clusters to reduce the cost, we still need to maintain a minimized running instances to deal with a possible short burst of client requests. Provisioning a new instance is kind of slow, you cannot just increase a few instances in a few million seconds when new client requests come in. Wait a minute? what if we can? Right, that is how serverless comes in.

Serverless is not actually "server" "less", it means you do not "occupy" a server when idle. You share a server with thousands of other applications and your function is loaded and run only if your client has a request comes in. I know serverless has other meanings, but I just make it as simple as possible. That's why serverless is sometimes called "Function as a Service", because as a developer you do not actually rent and deploy your code to this server instance, instead you upload your code to a function as a service provider, let them handle the request. Of course, you will need to design your application logic to be able to "pure" functional. Pure functional means stateless, all the states have to be stored in some kind of fast database, not in the memory of your instance because you do not even own an instance, you just own your code and few million seconds of the CPU running time. That is it.

Serverless technology allows the code to be cold started much faster than provisioning a cloud server instance. However, if your function is really "cold" it still needs a relatively long time to a cold start. Really "Cold" means either the first time deployment or idle for a very long period of time. Here is an article about [Cold start time](https://mikhail.io/serverless/coldstarts/big3/). Usually when your code is still "warm" you won't worry too much about the start time. It is usually between 1 and a few seconds. 

If you are very picky about fast response, we can still move one step further to reduce the few seconds latency. 

# CDN Serverless Worker

CDN is widely used because it is fast. It is fast because it is close to the end-user. CDN providers usually owns more data center across over the world. When you request a static resource, CDN will find your nearest (network distance, not geolocation) CDN server to feed the resource back to you. 

If CDN is fast, why do not we deploy my function code to the nearest CDN server, so that I can get a fast response? You are right, and someone is doing so, for example [CloudFlare Worker and Serverless Framework](https://www.cloudflare.com/learning/serverless/glossary/serverless-and-cloudflare-workers/). But wait a minute, did I say the server would be better to be deployed somewhere close to your data storage? It is easier to deploy your function code to CloudFlare's CDN server to handle those functions that do not require database access (one example verifying a signature or hash function). How about those functions designed to process data? Can you also move data to those CDN instance near me? Good suggestion, this is the first main topic for today decentralized database

# Decentralized database

Under the hood, blockchain is mainly an append-only distributed database without central management. Miners can append a new state (called block) to the database via some kind of consensuses in between. Because there is a consensus, all other miners agree on the new state and update their own database to up to date. Well, there is more to talk about blockchain but we just focus on our main topic today, do not get a sidetrack. 

So if we deploy block nodes to major CDN providers. There is always a node near you and provide the function and data together as long as the following conditions are met:

- You can trust the node, or there is some kind trustless that you do not need to trust the node. The node will run your code in a trusted environment and behavior as you expected without compromise or data leaking. 
- You are tolerant to slightly delay caused by the network synchronization on the database. because it will take some time for your nearest node to get the latest block update. Well, all distributed system has delays, it is just a matter of your tolerance.
- You and your app's client understand the whole system is running decentralized. That means no one including you and the miners have no responsibility and cannot revert anything wrote to the database. It is an append-only database. For example, if your clients lost their private keys, they are gone, period.
 
# the dApp tech stack overview

Well, this seems to be one of the tech stacks of decentralized application, so-called dApp. Let's recap what we discussed:

- You write your code in a purely functional way. Basically, your code does two kinds of tasks: a) Based on client query, check the distributed database (the blockchain) and response to your client. b)Based on the client's command creating a new transaction send to the miner. The miners are responsible to broadcast over the network and run consensus to update the global state in a new block.

- You store your code (if need to compile, ideally compile to Web Assembly. We will discuss why we go Web Assembly in detail later) to the blockchain. If the code is too big, it is OK to store the hash which other miners can download your code from IPFS. You do not deploy it anywhere. In other words, you have no control over the rum time of your code. No one has.

- The node needs to get some kind of certificate or so-called proof-of-trust so that clients will know this node is safe to get connected. This kind of certificate must be issued decentralized. That means no one can fake a certificate by compromise the issuing center.

Let's go through them one by one see what we have by the end of 2019.

## Using Tendermint / ABCI to separate your dApp logic from blockchain infrastructure

You are supposed to build a dApp, not a whole new blockchain, right? So why bother those complicated blockchain infrastructures? Let those experts handle it. Before 2019, we actually have no solution for this but now it is much better. One of the solutions to be considered is ABCI from Tendermint or Cosmos. You can either build your full dApp stack ground up with the only support to ABCI or, build on top of Cosmos SDK by leverage Cosmos' existing modules to save your time. With the help from Cosmos, you will focus on those 2 things I mentioned earlier - just in case you forgot - Query and Command. This reminds me of a programming pattern called CQRS. Let Tendermint blockchain infrastructure to maintain consensus and p2p network.

## If possible use Rust or any language that compiles to Web assembly to write your code

Technically you can write your code in any language since you only talk to Tendermint using ABCI. Even Cosmos itself is written in Golang. However, there are additional advantages if you follow what I said. Regardless of the memory safety from Rust, if your code can be compiled to WASM, the new widely adopted execution binary format can be running almost anywhere - not your grandpa's Java - I mean it. You can even store your compiled WASM binary into the blockchain just like the Solidity smart contract of Ethnereum. Since it is compiled to binary, it is much smaller than text code. WASM can be loaded into TEE thanks for the [Enarx](enarx.io) project. We will dive deeper into TEE shortly. There are many programming languages can compile to WASM format. C/C++, Rust, Golang, Typescript... you name it. There are more and more languages start to support WASM very month. Oh, I forgot to mention WASM runs in almost all browsers too. Did I say "anywhere", I mean it. 

## You will need Trusted Computing or Trusted Execution Environment

This is the hardest and most important part. You will need some kind of trust to let your client send their private information to somewhere your code is supposed to run correctly. Sounds easy? Well, probably not. Human is a complex animal because we can lie. Silicon chips cannot lie, but the software running on them is made by a human, so software CAN lie. Modern software is so complicated that no human being can verify each line of code to make sure it works as expected. When a hacker attacks a piece of software, the hacker can compromise the system to make it looks like running as usual and response as expected when dealing with verifiers. But under the clam surface hackers control the software doing something malicious that no one knows. Since humans cannot verify code line by line so they make some software to monitor or verify other software, eg. anti-virus or security software. Since so-called security software is still software, it can be compromised too, and even worse, it will pretend to be healthy even after compromise. Due to these reasons, we cannot simply trust a computer. We need some kind of a higher level root of trust to help us. Here come Trusted Computing and Trust Execution Environment technologies. 

Both of those two technologies rely deeply on hardware (TPM or CPU) which is harder to compromise than software. Even so, we still need to verify a node is trustable or not from other known trusted machines. Well, the question become which machine is considered "trustable". Actually, no single machine is trustable even the one you are currently using to read my blog. The solution is using consensus. If you know Bitcoin or other blockchain technologies, you will know in the Bitcoin network you do not trust any single node, you trust the consensus only based on the 51% assumption. For other consensuses, such as BFT, there is another assumption like no more than 1/3 malicious nodes. We can use the same assumption and consensus in our trusted network. Let's assume we use Tendermint (based on BFT) as long as more than 2/3 nodes are trusted, we will know the result can be trusted. That means a malicious node will be found and kicked out. That's why we need a platform built on top of hardware rooted Trusted Computing + Blockchain.

# How does our dApp tech stack look like

Let's review our tech stack from bottom up. Starting from the silicon chips.

## CPU and TPM

As the root of trust, we can either use TPM or CPU's TEE. There are pros and cons of using TEE:
- The code running inside TEE is protected by CPU manufacture. No one outside of the TEE knows what happen inside, even the RAM and OS
- TEE has a size limitation. A complicated code running inside TEE is low efficient. 
- CPU is expensive, also vendor locked in is also something to be considered. Different CPU vendor has its own special TEE. The good news is that Enarx is working on a substrate layer to make different TEE run the same WASM code without modification. 

On the other hand, TEE also has its own pros and cons:
- TPM cannot protect the system instead provide evidence so that software can run remote attestation to verify. So we will need to design a thicker stack to work with TPM, starting from system booting, BIOS/UEFI to OS and application layers. This is complicated.
- TPM is cheap. It doesn't require high-end semiconductor technologies to build. So there would be smaller vendors compete in this industry and unlikely vendor locked-in. 
- TPM vendors need to upload their vendor certification to the blockchain so that anyone can verify their product without going through a centralized location, like WWW as of today.

No matter the root-of-trust layer is CPU'sTEE or TPM, we will need remote attestation from unpredictable verifiers to prevent collusion. We will discuss the consensus shortly.  

## BIOS / UEFI

Because BIOS boot ahead of OS. If any malware (such as rootkit) hides in this layer, it would be much harder to get detected or removed. That's why we need TPM to guard this layer. TPM won't verify if a BIOS is valid or not. What TPM can do is to store the hash of booting BIOS to its PCR. After the node started, remote attestation from other verify will request for the PCR and make its own judgment remotely. In order to do this, an original hash of BIOS needs to be stored in blockchain for future reference. A vendor certificated upgrade version of bios needs to have its hash stored in blockchain before roll out an upgrade, otherwise, the remote attestation will consider unauthorized changes of BIOS when it verifies against the old value in the blockchain. 

## OS

We try to run all the logic in isolated containers. So the OS should be as simple as possible as long as it can securely load containers. The smaller the footprint, the less likely get a hacker attack. There are many minimized Linux OS available. I personally prefer not-to-tiny NixOS. I suggest NixOS not because it is smaller, but it is "functional". We can easily verify a NixOS is the same as it was before or the same as it claimed to be. Besides NixOS, other smaller docker container focus Linux systems are available too, such as RancherOS.

## Container

I assume we are going to run all our logic inside containers to get isolation and protection benefits. I use Docker as an example given there are other container technologies but Docker is the most popular one. We require all tenant code to be either inside docker image or out of docker but provide a docker image to load the code. In this case, verifiers can easily verify the hash of the docker image and /or the hash of the code to determine if the node is actually loading the correct code. 

By default, the docker engine will load a docker image docker hub. Given the docker hub is also the centralized single point of failure, we can consider load images from IPFS. The node will first verify the images downloaded from IPFS before loading. This can also be verified by remote attestation verifiers. 
