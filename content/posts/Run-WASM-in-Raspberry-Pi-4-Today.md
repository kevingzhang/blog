---
title: "Run WASM in Raspberry Pi 4 Today"
date: 2020-03-03T11:48:30-08:00
draft: false
---

WebAssembly (WASM) can run in almost all OS and architect, including Raspberry Pi. However, at the time of my test (Mar 1, 2020), only the wasmer claim and successfully run on my Pi 4. Other runtimes are either in planned mode or WIP. 

I would like to share some of my experiences running WASM in Raspberry PI4 and some mistakes I made during the test. I hope it can help you if you want to try the same thing on your Pi.

Disclaimer: WASM is a rapidly growing community and new tools new builds coming up every day. All the experiences I shared with you today may not be valid at the time you read it. So please take it on your own. 

# Why Wasmer?
Wasmtime is the first runtime I tried. But it doesn't support ARM yet.  Currently, only x86-64 works with Wasmtime. I know some other day it will work on ARM.

WAVM claimed the AArch64 support is in WIP (Work in Progress)mode, so I only tried on my Mac. It works excellent, just not on my Pi.

So Wasmer seems to be the only choice. I also read others blogs about the successful running Wasmer on Pi. So I tried on my own. 

# Need a 64 bit OS for Raspberry Pi
The Raspbian OS comes with my Pi is 32 bit, although the Pi's hardware is 64 bit. I guess it is due to compatibility concerns. Others suggest switch existing Raspbian's kernel to 64 bit by adding a line 



arm_64bit=1

to the config.txt in /boot directory. (Reference: https://medium.com/@kavoshex/raspian-added-support-for-64-bit-kernel-6cf27c2b26db). I think this will work but I did not try because I would like to taste some other 64 bit Pi OS. So I move to Manjaro 64 bit (https://manjaro.org/download/#ARM).

# You cannot just "curl" to install wasmer
Installing Manjaro went smoothly. I found it looks more beautiful than Raspbian (well, I know I consider every new thing more impressive). 

But when I try to install wasmer using the easiest way doesn't work.

By default we can install wasmer using curl:
`curl https://get.wasmer.io -sSfL | sh`
At least at the time when I tested this works on x86-64. The script did not find the corresponding binary for ARM64. 

Well, I have to build from source, I know it will take very long on an ARM CPU even if I have 4G RAM on my Pi.

I followed the instruction from https://docs.wasmer.io/ecosystem/wasmer/building-from-source and built the wasmer from the source. There is one thing I need to note here: Currently (Mar 1, 2020), only Singlepass backend works on ARM. So I need to set the backend to singlepass. The command is `make release-singlepass`.

It took a while to build, but the result is a fresh cool wasmer binary I was waiting for testing.

# Rust Integration API Needs Some Little Tweak
After build from source, the wamser can work in command line. But I really want to test is embedded inside Rust host. So I took the examples from https://docs.wasmer.io/integrations/rust/examples, and try them. Unfortunetely, they don't work. 

I quickly found the reason. In the example code, wasmer-runtime dependency is not compiled to support ARM64. So I need to build from source too.
```
Cargo.toml
[package]
name = "hello-world"
version = "0.1.0"
authors = ["The Wasmer Engineering Team <engineering@wasmer.io>"]
edition = "2018"
​
[dependencies]
# Add the wasmer-runtime as a dependency
wasmer-runtime = "0.13.1"

```

I cloned the wasmer-runtime project to my local folder, then redirect the path to my local path. Like this:
```
[package]
name = "hello-world"
version = "0.1.0"
authors = ["The Wasmer Engineering Team <engineering@wasmer.io>"]
edition = "2018"

[dependencies]
# Add the wasmer-runtime as a dependency
wasmer-runtime = {path = "../../../../../wasmer/lib/runtime"}
```

Note the wasmer-runtime path changed to my local folder.

Build it again.... Well, unluck failed.

# Build wasmer-runtime to Singlepass Backend
Follow the error message, I found the Cargo.toml file inside /wasmer/lib/runtime by default set the feature to cranelift. That makes sense to X86-64 but not working on ARM64 yet. So I simply change the default feature to singlepass. The Cargo.toml [features] session now looks like this: 
```
[features]
default = ["singlepass", "default-backend-singlepass"]
docs = []
cranelift = ["wasmer-clif-backend"]
cache = ["cranelift"]
llvm = ["wasmer-llvm-backend"]
singlepass = ["wasmer-singlepass-backend"]
default-backend-singlepass = ["singlepass"]
default-backend-llvm = ["llvm"]
default-backend-cranelift = ["cranelift"]
deterministic-execution = ["wasmer-singlepass-backend/deterministic-execution", "wasmer-runtime-core/deterministic-execution"]

```


Build again... Yeah, I made it!

# Playing around Wasmer Rust Integration
I did not try all examples, but it looks good to run rust host embedding wasmer-runtime all inside Raspberry Pi 4 with 4G RAM.

The goal I want to try wasm on ARM is that I am looking for a confidential computing solution in my future project. I will write other blogs about this research project. Since wasm is small, portable, and secure, it is the best option for future Function-as-a-Service on Edge Computing as long as we can provide Trusted Computing to guard the execution. Well, this could be a big topic. I would like to leave it to my later blog posts. Please stay tuned and leave comments below. I am happy to discuss with anyone who interests in Confidential Computing, Trusted Computing, and Edge Computing topics.

Happy Coding!
