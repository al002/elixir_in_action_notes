---
title: Chapter 13
tags:
categories:
date: 2024-12-24
lastMod: 2025-01-05
---
OTP releases

  + An OTP release is a standalone, compiled, runnable system that consists of the minimum set of OTP applications needed by the system

```bash
$ mix release
* assembling todo-0.1.0 on MIX_ENV=dev
* using config/runtime.exs to configure the release at runtime
Release created at _build/dev/rel/todo
```

  + Using a release

```bash
# start in the foreground with `iex` shell
$ RELEASE_NODE="todo@localhost" _build/prod/rel/todo/bin/todo start_iex
```

```bash
# start in the background
RELEASE_NODE="todo@localhost" _build/prod/rel/todo/bin/todo daemon
```

Analyzing system behavior

  + The key to understanding a highly concurrent system lies in logging and tracing

  + `IO.inspect`

    + surround any part of the code with IO.inspect (or pipe into it via |>) without affecting the behavior of the program

  + `dgb`

    + https://hexdocs.pm/elixir/Kernel.html#dbg/2

  + `pry`

    + https://hexdocs.pm/iex/IEx.html#pry/0

    + https://hexdocs.pm/elixir/debugging.html

  + benchmarking and profiling tools

    + `:timer.tc/1`

      + https://erlang.org/doc/man/timer.html#tc-1

    + mix profile.cprof https://hexdocs.pm/mix/Mix.Tasks.Profile.Cprof.html

    + mix profile.eprof https://hexdocs.pm/mix/Mix.Tasks.Profile.Eprof.html

    + mix profile.fprof https://hexdocs.pm/mix/Mix.Tasks.Profile.Fprof.html

    + `Benchee`

      + https://hexdocs.pm/benchee

    + https://www.erlang.org/doc/efficiency_guide/profiling.html

  + Logging

    + https://hexdocs.pm/logger/Logger.html

    + https://www.erlang.org/doc/apps/kernel/logger_chapter.html

  + Tracing

    + `iex(todo@localhost)1> :sys.trace(Todo.Cache.server_process("bob"), true)`

    + excessive tracing may hurt the system’s performance

    + `:dbg`

``` bash
iex(tracer@localhost)1> :dbg.tracer()
iex(tracer@localhost)2> :dbg.n(:"todo@localhost")
iex(tracer@localhost)3> :dbg.p(:all, [:call])
iex(tracer@localhost)4> :dbg.tp(Todo.Server, [])


(<12505.1106.0>) call 'Elixir.Todo.Server':whereis(<<"bob">>)
(<12505.1106.0>) call 'Elixir.Todo.Server':child_spec(<<"bob">>)
(<12505.1012.0>) call 'Elixir.Todo.Server':start_link(<<"bob">>)
(<12505.1107.0>) call 'Elixir.Todo.Server':init(<<"bob">>)
(<12505.1107.0>) call 'Elixir.Todo.Server':handle_continue(init, ...)
(<12505.1106.0>) call 'Elixir.Todo.Server':entries(<12505.1107.0>, ...)
(<12505.1107.0>) call 'Elixir.Todo.Server':handle_call({entries, ...})
```

    + Recon

      + https://github.com/ferd/recon
