---
title: "Leave Your Data There, I Will Send My App Over"
date: 2020-01-28T13:47:50-08:00
draft: false
---
# Leave your data there, let me send my app over

## Today's cloud computing cannot handle tomorrow's use cases

No matter what kind of computation we need to put input data and algorithm code together into a computer, and run the algorithm against the data so that we can get the result.

In most cases today, we will deploy our code to a cloud provider (Amazon, Google, Microsoft, Alibaba, etc.)'s cloud server inside a data center first, then upload the data into the same server, run the code, output the result. This process is so-called cloud computing. 

There are some cases that the above common practice won't work. 

- The compute node in one country, but the data is in another country where sending data out of the border may cause a legal issue. 
- Either the data owner or the app owner does not trust the cloud provider. They have concerns that the cloud provider cannot keep their data or code secure. 
- The data owner doesn't trust the app. He has a concern that the app developer has some back door to steal his data. 
- The data size is huge that transfer from its original location to datacenter costs too much.

There would be more cases I cannot cover here. In those cases, we need a new way to do computation. Such as "Do not send data to me, I will send my code to you" solution. 

## How about send the code over, why not?

Generally, the size of an app code is far less than the size of data to compute. And there are computers everywhere, why do not we send the app to the data owner's location and do the computing there. This solution sounds a reasonable and easy solution, but why have we not done that way? There are several problems. The biggest one is trust.

The concern of trust comes from two factors:
- How do I know the result is correctly computed from exactly the data and by exactly the code? 
- How do I know no one is watching or steal my data or my code during this process?

In cloud computing cases, those concerns could be easily handled by the trust built from the brand name. We use Amazon because we trust Amazon given it is a big name, and they hired an army of security experts to protect their data center from hacked by malware. When the computer is outside of the cloud provider's data center but located in the middle of nowhere, those concerns are valid. 

## Let's solve it in decentralized way

How do we solve it? I would use Trusted Computing and Confidential Computing in a Decentralized Way. 
 
### Some design concerns

There are a few design concerns we need to address:
- There should be a relatedly massive amount of compute nodes to get involved in computing. Although not all of them will be used during the computation, verifiable randomness is the best solution dealing with the collation and DDoS attacks. 
- Computing nodes (Executors) and verify nodes are selected randomly and can be easily verified its randomness by other verifiers. 
- Executors have TEE enabled CPU to run the tasks inside the enclave.

### Workflow

The task execution workflow would look like the following steps:

- Before eligible to run the task, the selected computing nodes (executor) can provide evidence showing its hardware/software states as Proof of Trust (PoT). Other verifiers can verify those Proof of Trust (PoT) remotely and make a consensus between all verifiers to judge the node is qualified or not. 

- Both data and code are in encrypted cipher when at rest and in transit. Only the data owner and the code owner have their security keys. 

- The executor creates enclave inside its TEE, load that encrypted code and data into the TEE. Send TEE verification requests to verifiers. 

- After verifier made such a consensus, the data owner and the code owner send their security keys to a trusted execution environment (TEE).

- The executor decrypts the code and the data into the clear format, then run the code against the data inside TEE.

- After execution, TEE output the result then destroys the TEE. 

### Why can we trust?

In this design, we have the following assumptions:

- Executors will report its hardware/software state honestly. This assumption can be guaranteed by TPM and Trusted Computing technology.

- The code and data inside the enclave are entirely hidden from any outsiders. Even the OS has no way to know anything inside the enclave. This assumption can be guaranteed by TEE technology.

- Verifiers cannot collude with the executor to cheat others. This assumption is guaranteed by blockchain technologies and the VRF algorithm.

## Conclusion

So, such a decentralized confidential computing network based on blockchain technologies can provide a possibility that is not possible before. The data owners do not need to send their data to datacenter at risk of leaking privacy, and they do not need to trust app developers nor cloud providers either. They only need to trust math and semiconduction technologies. 
