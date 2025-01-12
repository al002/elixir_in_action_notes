---
title: Chapter 9
tags:
categories:
date: 2024-12-28
lastMod: 2025-01-05
---
Use `Registry` to register process and then discover it

```bash
iex(1)> Registry.start_link(name: :my_registry, keys: :unique)

iex(2)> spawn(fn ->
  Registry.register(:my_registry, {:database_worker, 1}, nil)
  receive do
    msg -> IO.puts("got message #{inspect(msg)}")
  end
end)

iex(3)> [{db_worker_pid, _value}] =
Registry.lookup(
  :my_registry,
  {:database_worker, 1}
)

iex(4)> send(db_worker_pid, :some_message)
got message :some_message
```

Via tuples


  + allows you to use an arbitrary third-party registry to register OTP-compliant processes, such as GenServer and supervisors

  + `GenServer.start_link(callback_module, some_arg, name: some_name)`

    + `:name` option can also be provided in the shape of `{:via, some_module, some_arg}`. Such a tuple is also called a via tuple

```elixir
defmodule Todo.ProcessRegistry do
  def start_link do
  	Registry.start_link(keys: :unique, name: __MODULE__)
  end
  
  def via_tuple(key) do
  	{:via, Registry, {__MODULE__, key}}
  end
  
  def child_spec(_) do
    Supervisor.child_spec(
      Registry,
      id: __MODULE__,
      start: {__MODULE__, :start_link, []}
    )
  end
end

```

Supervision tree

  + a nested structure of supervisors and workers

  + describes how the system is started and how it’s taken down

  + This is how error recovery works in supervision trees—you try to recover from an error locally, affecting as few processes as possible. If that doesn’t work, you move up and try to restart the wider part of the system

  + OTP-compliant processes

    + https://www.erlang.org/doc/design_principles/spec_proc.html#special-processes.

  + Avoid process restarting

    + `restart: :temporary` in `child_spec/1`

    + `restart: :transient` in `child_spec/1`

      + restarted only if it terminates abnormally

  + Restart strategies

    + `:one_for_one`

      + a supervisor handles a process termination by starting a new process in its place, leaving other children alone

    + `:one_for_all`

      + When a child crashes, the supervisor terminates all other children and then starts all children

    + `:rest_for_one`

      + When a child crashes, the supervisor terminates all younger siblings of the crashed child. Then, the supervisor starts new child processes in place of the terminated ones

      + `younger siblings` means process which after crashed process in the supervisor childrens

Starting processes dynamically

  + `DynamicSupervisor`

```elixir
defmodule Todo.Cache do
  def start_link() do
    IO.puts("Starting to-do cache.")

    DynamicSupervisor.start_link(
      name: __MODULE__,
      strategy: :one_for_one
    )
  end
  
  defp start_child(todo_list_name) do
    DynamicSupervisor.start_child(
      __MODULE__,
      {Todo.Server, todo_list_name}
    )
  end
  ...
end
```

    + `DynamicSupervisor.start_child/2` is a cross-process synchronous call

Two important situations in which you should explicitly handle an error

  + In critical processes that shouldn’t crash

  + When you expect an error that can be dealt with in a meaningful way
