---
title: Chapter 7
tags:
categories:
date: 2024-12-30
lastMod: 2025-01-05
---
Mix project conventions

  + place module under a common top-level alias

    + like `Todo.List`, `Todo.Server`

  + one file, one module

    + except small helper module or protocol implementation for the module

  + filename should be an underscore-case

    + `TodoServer` module would reside in `todo_server.ex` file in the lib folder

  + The folder structure should correspond to multipart module names

    + `Todo.Server` should reside in the `lib/todo/server.ex` file

Addressing the process bottleneck

  + use process considerations

    + The code must manage a long-living state

    + The code handles a kind of resource that can and should be reused between multiple invocations, such as a TCP connection, database connection, file handle, and so on

    + A critical section of the code must be synchronized. Only one instance of this code may be running in any moment
