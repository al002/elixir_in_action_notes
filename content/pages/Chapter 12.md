---
title: Chapter 12
tags:
categories:
date: 2024-12-25
lastMod: 2025-01-05
---
Distribution primitives

  + Starting a cluster

```bash
$ iex --sname node1@localhost
iex(node1@localhost)1>

Node.connect(:node2@localhost)
```

    + Adding a new node to such a cluster amounts to establishing a connection to a single node from the cluster. The new node will then automatically connect to all nodes in the cluster

    + list all nodes in a cluster, including the current ont

      + `Node.list([:this, :visible])`

  + Communicating between nodes

    + 
```bash
iex(node1@localhost)4> Node.spawn(
  :node2@localhost,
  fn -> IO.puts("Hello from #{node()}") end
)
Hello from node2@localhost
```

      + lambda run in node2

    + send messages to process


      + location transparency

        + send messages to process, regardless of their location

```bash
iex(node1@localhost)5> caller = self()
iex(node1@localhost)6> Node.spawn(
  :node2@localhost,
  fn -> send(caller, {:response, 1+2}) end
)

iex(node1@localhost)7> flush()
{:response, 3}
```

      + Whatever works in one BEAM instance will work across different instances

        + except lambda

      + you can use the same registered name on different nodes

```bash
iex(node1@localhost)8> Process.register(self(), :shell)
true
iex(node2@localhost)3> Process.register(self(), :shell)
true

# send from node1
iex(node1@localhost)9> send(
  {:shell, :node2@localhost},
  "Hello from node1!"
)

iex(node2@localhost)4> flush()
"Hello from node1!"

```

    + Process discovery


      + `Registry` isn’t cluster aware and works only in the scope of a local node

      + `:global`


```bash
iex(node1@localhost)10> :global.register_name({:todo_list, "bob"}, self())
:yes

iex(node2@localhost)6> :global.whereis_name({:todo_list, "bob"})
#PID<7954.90.0>
```

```elixir
GenServer.start_link(
  __MODULE__,
  arg,
  name: {:global, some_global_alias}
)

GenServer.call({:global, some_global_alias}, ...)
```

      + groups of process

        + register several processes under the same alias

        + `pg` module

          + https://www.erlang.org/doc/man/pg.html

```bash
iex(node1@localhost)1> :pg.start_link()

iex(node2@localhost)1> Node.connect(:node1@localhost)
iex(node2@localhost)2> :pg.start_link()

iex(node1@localhost)2> :pg.join({:todo_list, "bob"}, self())
:ok
iex(node2@localhost)3> :pg.join({:todo_list, "bob"}, self())
:ok

iex(node1@localhost)3> :pg.get_members({:todo_list, "bob"})
[#PID<8531.90.0>, #PID<0.90.0>]
iex(node2@localhost)4> :pg.get_members({:todo_list, "bob"})
[#PID<0.90.0>, #PID<7954.90.0>]
```

      + Links and monitors work even if processes reside on different nodes

    + `:rpc` module to call a function on remote node and get its result

    + Message passing is the core distribution primitive

Network considerations

  + node can connect only to a node that has the same type of name

  + Cookies

    + To connect two nodes, they must agree on a magical *cookie*

```bash
iex(node1@localhost)1> Node.get_cookie()
:JHSKSHDYEJHDKEDKDIEN

iex(node1@localhost)1> Node.set_cookie(:some_cookie)
iex(node1@localhost)2> Node.get_cookie()
:some_cookie

$ iex --sname node1@localhost --cookie another_cookie
iex(node1@localhost)1> Node.get_cookie()
:another_cookie
```
