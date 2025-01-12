---
title: Chapter 1
tags:
categories:
date: 2025-01-05
---
Erlang

  + High availability / Concurrency

![2024-12-30_14-49-36.png](/assets/2024-12-30_14-49-36_1735541391110_0.png)

+ Fault tolerance

  + Erlang process is completely isolated from each other.

    + no memory share

    + a crash of one process doesn’t cause a crash of other processes

    + communicate via asynchronous messages

+ Scalability

  + BEAM can take advantage of all available CPU cores

+ Distribution

  + processes works the same way in same BEAM instance or remote computer

  + Erlang-based system is automatically ready to be distributed over multiple machines

+ Responsiveness

  + I/O operations are internally delegated to separate threads, or a kernel-poll service of the underlying OS is used

  + perprocess garbage collection

    + each process is individually collected

+ Live update

  + Erlang development platform

    + language

    + virtual machine (BEAM)

    + OTP framework

    + tools

Elixir

  + Elixir targets the Erlang runtime.

  + compiled to BEAM-compliant bytecode

  + clean syntax

Disadvantage

  + Speed

  + CPU intensive task should consider some other technology

  + ecosystem not large as some other language

Summary

  + Developing highly available system

  + Elixir make such development more pleasant
