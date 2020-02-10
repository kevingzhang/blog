---
title: "Leave Your Data There, I Will Send My App Over"
date: 2020-01-28T13:47:50-08:00
draft: false
---
## Today's cloud computing cannot handle tomorrow's use cases

Computation involves data being processed by an algorithm.

Today, cloud computing makes a compelling environment for data processing, in which we will typically deploy our code to a service provider's data center and also upload our data to servers of the same data center. 

There are cases that the above common practice won't work. 

- National data residency requirements prohibit sending the data to another country where the cloud computing node resides. 
- Either the data owner or the app owner does not trust the cloud provider. They have concerns that the cloud provider cannot keep their data or code secure. 
- The data owner doesn't trust the app, worrying that the app developer has a back door to steal his data. 
- The data size is so large that it is cost proohibitive to transfer the data from its original location to the datacenter.

In the cases above, among others, we need to change the way computation is done. How about the "Do not send data to me, I will send my code to you" approach? 

## How about send the code over, why not?

Generally, the size of the application code is far less than the size of the data to process. So it is more economical to send the application code to where the data resides do the computing there. But why have we not done so? There are several problems. The biggest one is trust.

The concern of trust comes from two angles:
- How do I know the result is correctly computed from data and the code that have been provided? 
- How do I know no one is watching or try to steal the data or the code during this process?

In today's cloud computing scenario, these concerns could be easily addressed by the trust in the brand name. We use Amazon because we trust Amazon given it is a big name, and they hired an army of security experts to protect their data center from hacking or malware. But when the computer is outside of a trusted cloud provider's data center and located in the middle of nowhere, those concerns become very valid. 

## Let's solve it in decentralized way

How do we solve this probelm? I propose the use of Trusted Computing and Confidential Computing in a Decentralized Way. 
 
### Some design concerns

My proposal has the following key elements:
- There should be a relatedly massive amount of compute nodes involved in this decentralized network of computers. Although not all of them will be used during the computation, verifiable randomness is the best solution for dealing with the collusion and DDoS attacks. 
- Computing nodes (executors) and verifying nodes are selected using verifiable randomness. 
- Executors have TEE-enabled CPU to run the tasks inside the secure enclave. TEE stands for trusted execution environment.

### Workflow

The task execution workflow has the following steps:

- Before becoming eligible to run a computation task, the selected computing nodes (executors) must provide evidence showing its hardware/software states as Proof of Trust (PoT). Other node (verifiers) on the network can verify those Proof of Trust (PoT) remotely and reach a consensus between all verifiers to whether the node is qualified or not. 

- Both data and code are in encrypted format when at rest and in transit. Only the data owner and the code owner have their decryption keys. 

- The executor 1) creates an enclave inside its TEE, 2) loads that encrypted code and data into the enclase, and 3) sends a TEE verification request to verifiers on the network. 

- After verifiers have reached a consensus, the data owner and the code owner send their decryption keys to the trusted execution environment (TEE).

- The executor decrypts the code and the data, and runs the code against the data inside the TEE.

- After execution, TEE outputs the result and then destroys the enclave. 

### Why can we trust?

In this design, we have the following assumptions:

- Executors will report its hardware/software state honestly. This assumption can be guaranteed by TPM (trusted platform module) and other Trusted Computing technologies.

- The code and data inside the enclave are entirely hidden from any outsiders. Even the OS has no way to know anything inside the enclave. This assumption can be guaranteed by the TEE technology.

- Verifiers cannot collude with the executor to cheat others. This assumption is guaranteed by blockchain technologies and the VRF (verifiable random function) algorithm.

## Conclusion

In summary, the proposed decentralized confidential computing network based on blockchain technologies provides a new way of computation that was not possible before. The data owners do not need risk data privacy in sending their data to someone else's data center, and they do not need worry about the application developer either. They only need to trust the secure enclave and blockchain consensus, which have been proven trustworthy under very reasonable conditions. 
