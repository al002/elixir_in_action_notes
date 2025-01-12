---
title: Chapter 10
tags:
categories:
date: 2024-12-27
lastMod: 2025-01-05
---
Tasks

  + The Task module can be used to concurrently run a job

  + Awaited tasks

    + An awaited task is a process that executes some function, sends the function result back to the starter process, and then terminates

```bash
iex(1)> long_job =
  fn ->
    Process.sleep(2000)
    :some_result
  end
  
iex(2)> task = Task.async(long_job)

# await will wait response from the task process
# default 5 seconds to timeout
iex(3)> Task.await(task)
:some_result
```

    + if any task process crashes, the starter process will crash too

    + `Task.async/1` has all-or-nothing semantics

  + Non-awaited tasks

```bash
iex(1)> Task.start_link(fn ->
  Process.sleep(1000)
  IO.puts("Hello from task")
end)

{:ok, #PID<0.89.0>} # Result of Task.start_link/1

Hello from task! # Printed 1 second later

```

  + Supervising dynamic tasks

```bash
iex(1)> Task.Supervisor.start_link(name: MyTaskSupervisor)

iex(2)> Task.Supervisor.start_child(
  MyTaskSupervisor,
  fn ->
    IO.puts("Task started")
    Process.sleep(2000)
    IO.puts("Task stopping")
  end
)

{:ok, #PID<0.118.0>}
Task started
Task stopping
```

Agents

  + provides an abstraction that’s similar to `GenServer`

  + require a bit less ceremony and can, therefore, eliminate some boilerplate associated with `GenServer`

```bash
iex(1)> {:ok, pid} = Agent.start_link(fn -> %{name: "Bob", age: 30} end)
{:ok, #PID<0.86.0>}

iex(2)> Agent.get(pid, fn state -> state.name end)
"Bob"
```

    + execute passed lambda

  + Limitations of agents

    + cannot handle plain messages

    + cannot run some logic on termination

ETS tables

  + ETS (Erlang Term Storage) tables are a mechanism that allows you to share some state between multiple processes in a more efficient way

  + usecase

    + shared key–value structures and counters

  + ETS characteristics

    + There’s no specific ETS data type. A table is identified by its ID (a reference
type) or a global name (an atom).

    + ETS tables are mutable. A write to a table will affect subsequent read operations.

    + Multiple processes can write to or read from a single ETS table. Writes and
reads might be performed simultaneously.

    + Minimum concurrency safety is ensured. Multiple processes can safely write to the same row of the same table. The last write wins.

    + An ETS table resides in a separate memory space. Any data coming in or out is deep copied.

    + ETS doesn’t put pressure on the garbage collector. Overwritten or deleted data is immediately released.

    + An ETS table is deeply connected to its owner process (by default, the process that created the table). If the owner process terminates, the ETS table is reclaimed.

    + Other than on owner-process termination, there’s no automatic garbage collection of an ETS table. Even if you don’t hold a reference to the table, it still occupies memory.

```
iex(1)> table = :ets.new(:my_table, [])
#Reference<0.970221231.4117102596.53103>

iex(2)> :ets.insert(table, {:key_1, 1})
true

iex(3)> :ets.insert(table, {:key_2, 2})
true

iex(5)> :ets.lookup(table, :key_1)
[key_1: 3]
```

  + table types

    + `:set`—This is the default. One row per distinct key is allowed

    + `:ordered_set`—This is just like :set, but rows are in term order (comparison via the < and > operators)

    + `:bag`—Multiple rows with the same key are allowed, but two rows can’t be completely identical

    + `:duplicate_bag`—This is just like `:bag,` but it allows duplicate rows

  + table permissions

    + `:protected`—This is the default. The owner process can read from and write to the table. All other processes can read from the table

    + `:public`—All processes can read from and write to the table

    + `:private`—Only the owner process can access the table

  + full-blown match specification

    + `:ets.select/2`

      + https://erlang.org/doc/man/ets.html#select-2

      + make task simpler

        + `ex2ms`

          + https://github.com/ericmj/ex2ms


