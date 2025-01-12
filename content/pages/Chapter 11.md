---
title: Chapter 11
tags:
categories:
date: 2024-12-26
lastMod: 2025-01-05
---
OTP applications

  + An OTP application is a component that consists of multiple modules and that can depend on other applications

  + When the application is started, the function `HelloWorld.Application.start/2` is called

```elixir
defmodule HelloWorld.Application do
  use Application
  
  def start(_type, _args) do
    children = []
    opts = [strategy: :one_for_one, name: HelloWorld.Supervisor]
    Supervisor.start_link(children, opts)
  end
end
```

  + Starting the application

    + start the application in the running BEAM instance

      + call `Application.start/1`

      + verifies whether all the applications you’re depending on are started

      + calling the callback module’s `start/2` function

      + `Application.ensure_all_started/2` is available, recursively starts all dependencies that aren’t yet started

    + `Application.stop/1` to stop application

    + `System.stop/0` to stop entire system, including dependencies

  + Library applications

    + not provide`mod: ...` option in `application/0` function

```elixir
defmodule HelloWorld.Application do
  ...
  def application do
  	[]
  end
  ...
end
```

  + The compiled code structure

```
YourProjectFolder
  _build
    dev
      lib
        App1
          ebin
          priv
        App2
      	...
```

  + Visualizing the system

    + `:observer.start()`

Library guidelines

  + https://hexdocs.pm/elixir/library-guidelines.html#avoid-application-configuration
