---
title: How to Deploy a Rust Binary on a Light Alpine Docker Image
draft: false
date: 2024-05-24
tags: [Rust, Docker, Alpine]
---

# Introduction

When it comes to deploying Rust applications, using Docker containers has become a popular choice. Docker allows you to package your application along with its dependencies, making it easier to deploy and run consistently across different environments. In this simple blog post, I want to simply and quickly explain how to do it, since it can be tricky to deploy a Rust compiled binary on a lightweight Alpine Docker image.

If you would just use typical way to build rust binary with `cargo build --release` and then copy it to the Alpine image, you will get an error like this:

```bash
sh: ./binary: not found
```

Simply binary would not be able to run since it's missing some dependencies that are not included in the Alpine image.

## Prerequisites

Before we begin, make sure you have the following installed on your system:

- Rust programming language
- Docker

## Step 1: Install also musl-tools

```bash
apt-get install -y musl-tools
```

`musl-tools` is a package that provides utilities for building and working with applications that use the musl libc (musl Library). Musl is an alternative standard library implementation for the C programming language.
The musl-tools package typically includes:
  * Compiler wrappers: These tools help in compiling code with musl instead of the default glibc.
  * Static linking: Utilities to aid in statically linking binaries against musl, which helps in producing more portable and standalone binaries.
  * Compatibility aids: Tools to ensure that software built with musl behaves correctly and maximizes compatibility.


## Step 2: Build the Rust Binary

Second, we need to build the Rust binary that we want to deploy. You can do this inside dockerfile or just ouside and then just copy it to the Assuming you have your Rust project set up, navigate to the project directory and run the following command:

```bash
cargo build --target x86_64-unknown-linux-musl --release
```

### Step 3: Finally build the docker image

Once the binary is built, we can just copy the binary to the Alpine image and run it. Here is a simple Dockerfile that does this:

```dockerfile
FROM alpine:3.19

COPY ./binary /usr/local/bin/binary

CMD ["/usr/local/bin/binary"]
```

It doesn't have to be exactly like this, you can add more stuff to the Dockerfile, but this is the basic idea. Just copy the binary to the image and run it or add this process directly to the dockerfile and you are good to go.
