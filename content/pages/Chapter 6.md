---
title: Chapter 6
tags:
categories:
date: 2024-12-31
lastMod: 2025-01-05
---
Generic server processes

  + `start`

  + `init`

  + `handle_call` is synchronous request

  + `handle_cast` is asynchronous request

  + `handle_info` handle plain messages, not specific to `GenServer`

  + callback function execute in server process

  + Plugging into GenServer

```elixir
defmodule KeyValueStore do
	use GenServer
end
```

    + `use` macro

      + During compilation, when this instruction is encountered, the specific macro from the `GenServer` module is invoked. That macro then injects several functions into the calling module

    + use `@impl GenServer` to tells the compiler that the function about to be defined is a callback function for the GenServer behaviour


```elixir
defmodule EchoServer do
  use GenServer
  
  @impl GenServer
  def handle_call(some_request, server_state) do
  	{:reply, some_request, server_state}
  end
end
```

    + name registration


```elixir
GenServer.start(
  CallbackModule,
  init_param,
  name: :some_name
)

# During compilation, __MODULE__ is replaced with the name of the module where the code resides:
defmodule KeyValueStore do
  def start() do
  	GenServer.start(__MODULE__, nil, name: __MODULE__)
  end
  
  def put(key, value) do
  	GenServer.cast(__MODULE__, {:put, key, value})
  end
  ...
end
```

    + `{:stop, reason}` or `:ignore` to stop server process

      + `handle_call` return `{:stop, reason, response, new_state}`

  + Process lifecycle

![2025-01-02_17-04-10.png](/assets/2025-01-02_17-04-10_1735808663443_0.png)
