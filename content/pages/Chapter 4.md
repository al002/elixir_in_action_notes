---
title: Chapter 4
tags:
categories:
date: 2025-01-02
---
Basic principles of abstraction in Elixir

  + A module is in charge of abstracting some behavior.

  + The module’s functions usually expect an instance of the abstraction as the first
argument.

  + Modifier functions return a modified version of the abstraction.

  + Query functions return some other type of data.

Abstracting with modules


```elixir
defmodule TodoList do
	...
  def add_entry(todo_list, entry) do
    entry = Map.put(entry, :id, todo_list.next_id)
    new_entries = Map.put(
      todo_list.entries,
      todo_list.next_id,
      entry
    )
    %TodoList{todo_list |
      entries: new_entries,
      next_id: todo_list.next_id + 1
    }
  end
  ...
end
```

    + The entire operation will be atomic

    + This is the consequence of immutability. The effect of adding an entry is visible to others only when the `add_entry/2` function finishes and its result is taken into a variable. If something goes wrong and you raise an error, the effect of any transformations won’t be visible

Polymorphism with protocols

```elixir
defprotocol String.Chars do
  def to_string(term)
end

defimpl String.Chars, for: Integer do
  def to_string(term) do
  	Integer.to_string(term)
  end
end
```

  + type can be any other arbitrary alias (but not a regular, simple atom)
