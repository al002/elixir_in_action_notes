---
title: Chapter 5
tags:
categories:
date: 2025-01-01
---
Concurrency primitives

  + Creating processes


```elixir
iex(1)> run_query =
  fn query_def ->
    Process.sleep(2000)
    "#{query_def} result"
  end

spawn(fn ->
  expression_1
  ...
  expression_n
end)

iex(4)> spawn(fn ->
  query_result = run_query.("query 1")
  IO.puts(query_result)
end)
#PID<0.48.0>

query 1 result # Printed after 2 seconds

iex(5)> async_query =
  fn query_def ->
    spawn(fn ->
      query_result = run_query.(query_def)
      IO.puts(query_result)
    end)
  end
  
iex(6)> async_query.("query 1")
#PID<0.52.0>
query 1 result # 2 senconds later

iex(7)> Enum.each(1..5, &async_query.("query #{&1}"))
:ok

query 1 result
query 2 result
query 3 result
query 4 result
query 5 result # 2 seconds later
```

  + Message passing


    + `self/0` get current process pid

    + send message

      + `send(pid, {:an, :arbitrary, :term})`

    + receive message

```elixir
receive do
  pattern_1 -> do_something
  pattern_2 -> do_something_else
end

receive do
  message -> IO.inspect(message)
after
  5000 -> IO.puts("message not received")
end
```

      + 1. Take the first message from the mailbox

      + 2. Try to match it against any of the provided patterns, going from top to bottom

      + 3. If a pattern matches the message, run the corresponding code

      + 4. If no pattern matches, take the next message, and start from step 2

      + 5. If there are no more messages in the queue, wait for a new one to arrive. When a new message arrives, start from step 2.

      + 6. If the after clause is specified and no message is matched in the given amount of time, run the code from the after block.

    + Stateful server processes

      + server process

        + runs for a long time (or forever) and can handle various requests (messages)

```elixir
defmodule DatabaseServer do
  def start do
  	spawn(&loop/0)
  end

  defp loop do
    receive do
      ...
      loop()
  	end
  ...
  end

end
```

      + server process is internally sequential

```elixir
def start do
  spawn(fn ->
    initial_state = ...
    loop(initial_state)
  end)
end

defp loop(state) do
  ...
  loop(state)
end
```

      + The data should be modeled using pure functional abstractions

      + A stateful process serves as a container of such a data structure

        + The process keeps the state alive and allows other processes in the system to interact with this data via the exposed API.

  + Registered processes


```bash
iex(1)> Process.register(self(), :some_name)

iex(2)> send(:some_name, :msg)

iex(3)> receive do
	msg -> IO.puts("received #{msg}")
end
received msg
```

    + The name can only be an atom

    + A single process can have only one name

    + Two processes can’t have the same name

  + Runtime considerations

    + A process is sequential

      + if many processes send messages to a single process, that single process may become a bottleneck, which significantly affects overall throughput of the system
