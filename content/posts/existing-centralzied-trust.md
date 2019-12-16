---
title: "Existing Centralized Trust Solution"
date: 2019-12-16T13:37:29-08:00
draft: true
tags: ["trusted computing", "tech", "blockchain"]
categoy: ["tech"]
---

Although we generally do not trust a machine in the middle of nowhere, in most cases we still use them, every day, every minute, every second. Cloud computing is one example. Nowadays most of the computing is happening in the data center of cloud computing providers, such as Amazon, Google, Microsoft, and others. We trust them because they are big names, and we trust them because we know they spend tons of resources to keep our data secure. Well, that was our expectation, but the reality could be different. Finding data leakage and cyber-attack news is not hard. Not to mention possible superpower (eg. government) influence.  No matter how we design the protection system, as long as there is an existing well-known center to be attacking target, there would be a huge chance to get broken. 

How about we distribute the data and processing power from the center to countless smaller compute nodes, then connect them using a p2p network. There would be no single point of failure and a single point of bait for attackers. 

This sounds attractive but lays a few problems need to be resolved. 

To defeat attackers, we need to think like an economist. We need to follow two principles:
the attacker's cost higher than the benefit.  
the defender's cost lower than the attacker's cost.
If the confidential information is the source of profit from the attacker's point of view, protect information is much harder than protect any physical matter. Unlike gold or oil or any real matter, information can be copied and each copy has the same value as the original copy. This is the same reason why DRM (Digital Right Management) never worked well. If we distribute by store multiple copies of information to many nodes, we just expend the attacking surface, that getting worse. 

Distributing information to many smaller nodes also makes protection harder or more expensive. Big tech giants can afford expensive facilities and highly skilled professionals to protect their main data center, but smaller distributed node owners may not able to afford it.

 What's the solution then?

Before I bring up my solution, please follow my logic first. There are a few things we can do in combination:
As we discussed in a previous post, we can use cryptography to protect data at rest and data in transit. Then we could only focus on the hardest case: data in use. Therefore, the cost of defense is significantly reduced.  
If there are millions of nodes in the p2p network, at any given time, only very few nodes are doing valuable computation which worth for hackers to attack. However, the zero-knowledge algorithm can keep anyone from knowing who they are. If hackers just random attack every node, the chance will be too low to cover hacker's cost. 
We keep the diversity of the tech stack and topology of our p2p network. Each node is independent of other nodes, there is no such a single-center node to control others. Each node is running a technical stack from the combinations of several hardware/software technologies. If a couple of technologies are comprised will not cause more then 1/3 of total nodes to fail at any given time. 
Economic incentivize is playing an important role in the p2p network so that all nodes are willing to contribute their computing power and well maintained to make money. Bad behaviors are easy to get noticed and punished. 

These are the principles of designing the decentralized trusted computing network which we will discuss in my further posts.