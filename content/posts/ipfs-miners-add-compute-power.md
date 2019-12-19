---
title: "Ipfs Miners Add Compute Power"
date: 2019-12-18T21:24:15-08:00
draft: false
---

# What a wonderful decentralized storage world

IPFS (and other similar projects on the decentralized storage market) provides us a new way of thinking on storage. Instead of storing everything in a centralized location, why do not we make multiple copies and distributed to millions of nodes all over the world? The user can get one copy from any of them based on the network distance or bandwidth. This could become the largest CDN system in the world under the economic incentive model to IPFS miners. 

# Can we decentralize the compute power as well?

Let's assume we have solved the data security issue that we are OK to store anything on any node, but where do we do the computation? In traditional IaaS, PaaS even the latest FaaS stack we still need to deploy our code to some kind of servers, although it may be called "Serverless". You may ask what is the point if we still need to send already decentralized data back to a centralized server to run computation? Why cannot we run a compute at the nearest IPFS node and get the result directly? Well, that is a good idea but we need to solve the following problems first.

## Data security point of view, Data in use is much harder than data at rest and data in transit

We discussed a few posts ago about 3 states of data security: Data at rest, data in transit and data in use. Among those 3 states data is the hardest to deal with.

The hardest one is "Data in use". This mainly because data cannot be computed while keeping encrypted mode. FHE is one exception, but it is too far from practical. So we have to decrypt the data before the data being computed. In order to protect this fragile stage of data lifetime, we need some kind of trust computing: TEE or TPM. Using those technologies to create a remote trusted enclosure so that the decrypted data can be processed inside without leaking and the result can be trusted.

## Code need to be running seamlessly in a different environment: From Docker to Wasm

Modern applications are very complicated and need a lot of dependencies to run property. That's why before we can run a server a lot of environments need to be installed and configured. Container technologies such as Docker helped us a lot. We can put everything into a docker image and loaded it into a docker container, it should run without any problem as expected. No doubt, docker is much smaller than an old generation VM. However, Web assembly pushes our imagination to a new higher level. 
 
Computer technologists are never good at naming their inventions. Javascript, for example, has nothing to do with Java. Web Assembly is [neither web nor assembly](https://www.javascriptjanuary.com/blog/webassembly-neither-web-nor-assembly-but-revolutionary). With the help from WASI and other toolings, WebAssembly can be running in almost everywhere. More important, Wasm is much secure than regular native code because they are running inside an isolated environment with carefully designed runtime. This kind of security not only protect the host machine but also protect data security. If we do things right, with the combination of hardware security (TEE/TPM) and Wasm runtime we can build trust among data vendors, code venders, and hosts, although they do not trust each other. 

## How about the database?

IPFS is very good at store static content. Even for private content can be safely stored in public IPFS as long as we keep our decrypted key private. How about the database? All dynamic applications need to run on top of some kind of database. We have moved the static content and computing power to the decentralized node, can we also move the database to the edge? Yes, but not the regular database, some cryptographic process is necessary to keep data safe in such a public distribution system. 

Some database crypto technologies allow index or query databases against encrypted content. We can have a local database cache in IPFS miner nodes. They only listen to blockchain's new state change to update the local state. Because we do not need to decrypt data to query, it would be faster in many query cases. When need to decrypt data to run some data processing task, the decrypted will be securely sent to the trusted environment so that data only decrypted inside the trusted environment without leaking. 

# Token incentive to miners

IPFS miners, as well as all other crypto miners, do their job because they are incentivized by a token economy. Add additional computing tasks also need to give enough economic incentive to the miners so that they are willing to make money from their existing IPFS node. 

For any given existing IPFS node owner, besides original IPFS filecoin, they can also get additional income from doing edge computing jobs as long as they can get some kind of trust certificate to prove itself a trusted environment. This creates a new kind of business model: **Trust as a Service**

# Trust-as-a-Service

To be continued in my next post...

