---
title: Chapter 8
tags:
categories:
date: 2024-12-29
lastMod: 2025-01-05
---
Runtime errors

  + error types


    + errors, exits, and throws

    + error

```
raise("Something went wrong")
** (RuntimeError) Something went wrong
```

    + exit

```
iex(2)> spawn(fn ->
    exit("I'm done")
    IO.puts("This doesn't happen")
  end)
```

    + throws

```
iex(3)> throw(:thrown_value)
** (throw) :thrown_value
```

  + Handling errors

```
try do
  ...
  catch error_type, error_value ->
  ...
end
```

    + `catch` blocks are patterns

    + tail call isn’t possible if the function call resides in a `try` expression

  + A run-time error has a type, which can be :error, :exit, or :throw

  + A run-time error also has a value, which can be any arbitrary term

  + If a run-time error isn’t handled, the corresponding process will terminate

Errors in concurrent systems

  + Linking processes

    + `links`

      + If two processes are linked, and one of them terminates, the other process receives an exit signal—a notification that a process has crashed

      + always bidirectional

    + normal termination

      + exit reason is `:normal`

```elixir
spawn(fn ->
  spawn_link(fn ->
    Process.sleep(1000)
    IO.puts("Process 2 finished")
  end)

  raise("Something went wrong")
end)
```

    + the crash of a single process will emit exit signals to all of its linked processes. If the default behavior isn’t overridden, those processes will crash as well. Ultimately, the entire tree of linked processes will be taken down

    + Trapping exits

      + `Process.flag(:trap_exit, true)`

```elixir
spawn(fn ->
  Process.flag(:trap_exit, true)
  spawn_link(fn -> raise("Something went wrong") end)
  
  receive do
  	msg -> IO.inspect(msg)
  end
end)
```

  + Monitors

    + unidirectional propagation of a process crash

    + `monitor_ref = Process.monitor(target_pid)`

  + Supervisors

    + manages the life cycle of other processes in a system

    + The supervisor process traps exits and then starts the child processes

    + If, at any point in time, a child terminates, the supervisor process receives a corresponding exit message and performs corrective actions, such as restarting the crashed process.
    + If the supervisor process terminates, its children are also taken down

```elixir
Supervisor.start_link([Todo.Cache], strategy: :one_for_one)
```

    + Child specification

      + How should the child be started?

      + What should be done if the child terminates?

      + What term should be used to uniquely identify each child?

      + to-do cache specification

```elixir
%{
  id: Todo.Cache,
  start: {Todo.Cache, :start_link, [nil]},
}
```

```elixir
Supervisor.start_link(
  [
    %{
      id: Todo.Cache,
      start: {Todo.Cache, :start_link, [nil]}
    }
  ],
  strategy: :one_for_one
)
```

      + `module_name.child_spec(arg)` return the module specification

        + The default implementation is injected by use GenServer

```
Supervisor.start_link(
  [{Todo.Cache, nil}],
  strategy: :one_for_one
)
```

```bash
iex(1)> Todo.Cache.child_spec(nil)
%{id: Todo.Cache, start: {Todo.Cache, :start_link, [nil]}}
```

      + When invoke `Supervisor.start_link(child_specs, options)`


        + The new process is started, powered by the Supervisor module

        + The supervisor process goes through the list of child specifications and starts each child, one by one

        + Each specification is resolved, if needed, by invoking child_spec/1 from the corresponding module

        + The supervisor starts the child process according to the :start field of the child specification

    + Restart frequency

      + Supervisor won’t restart a child process forever

      + By default, the maximum restart frequency is three restarts in 5 seconds

    + 
