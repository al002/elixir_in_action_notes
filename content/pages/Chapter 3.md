---
title: Chapter 3
tags:
categories:
date: 2025-01-03
---
Pattern matching

  + `=` operator is called the match operator


    + assignment-like expression is an example of *pattern matching*

```bash
iex(1)> person = {"Bob", 25}
```

    + Matching tuples


```bash
iex(1)> {name, age} = {"Bob", 25}
```

    + Matching constants


```bash
iex(2)> person = {:person, "Bob", 25}

iex(3)> {:person, name, age} = person
{:person, "Bob", 25}

{:ok, contents} = File.read("my_app.config")
```

      + By using constants in patterns, you tighten the match, ensuring some part of the right side has a specific value.

      + `^` pin operator

```bash
iex(7)> expected_name = "Bob"
"Bob"
iex(8)> {^expected_name, _} = {"Bob", 25}
{"Bob", 25}
iex(9)> {^expected_name, _} = {"Alice", 30}
** (MatchError) no match of right hand side value: {"Alice", 30}
```

        + Using `^expected_name` in patterns indicates you expect the value of the variable `expected_name` to be in the appropriate position in the right-side term

          + like `({"Bob", _} = …)`

    + Matching lists


```bash
iex(1)> [first, second, third] = [1, 2, 3]
[1, 2, 3]


iex(3)> [head | tail] = [1, 2, 3]
[1, 2, 3]

iex(4)> head
1

iex(5)> tail
[2, 3]
```

    + Matching maps


```bash
iex(1)> %{name: name, age: age} = %{name: "Bob", age: 25}
%{age: 25, name: "Bob"}

iex(6)> %{age: age, works_at: works_at} = %{name: "Bob", age: 25}
** (MatchError) no match of right hand side value
```

    + Matching bitstrings and binaries


```bahs
iex(6)> <<b1, rest :: binary>> = binary
<<1, 2, 3>>

iex(7)> b1
1

iex(8)> rest
<<2, 3>>
```

    + Matching binary strings


```bash
iex(16)> command = "ping www.example.com"
"ping www.example.com"

iex(17)> "ping " <> url = command
"ping www.example.com"

iex(18)> url
"www.example.com"

```

    + Compound matches


      + match expression


        + `pattern = expression`

        + `iex(2)> a = 1 + 3`

          + 1 The expression on the right side is evaluated.

          + 2 The resulting value is matched against the left-side pattern.

          + 3 Variables from the pattern are bound.

          + 4 The result of the match expression is the result of the right-side term

        + match chaining

```bash
iex(3)> a = (b = 1 + 3)
4

iex(4)> a =b=1+3
4
```

          + 1 The expression 1 + 3 is evaluated.

          + 2 The result (4) is matched against the pattern b.

          + 3 The result of the inner match (which is, again, 4) is matched against the pattern a.

```bash
iex(6)> date_time = {_, {hour, _, _}} = :calendar.local_time()

iex(7)> {_, {hour, _, _}} = date_time = :calendar.local_time()

iex(8)> date_time
{{2023, 11, 11}, {21, 32, 34}}

iex(9)> hour
21
```

          + ordering is not matter

            + because the result of a pattern match is always the result of the term being matched (whatever is on the right side of the match operator)

    + General behavior


      + two parts:

        + *pattern* (left side)

        + *term* (right side)

      + You assert your expectations about the right-side term. If these expectations aren’t met, an error is raised.

      + You bind some parts of the term to variables from the pattern.

  + Matching with functions

```elixir
def my_fun(arg1, arg2) do
	...
end
```

    + `arg1` and `arg2` are patterns

    + the arguments you provide are matched against the patterns specified in the function definition

    + Multiclause functions


      + A clause is a function definition specified by the def expression

```elixir
defmodule Geometry do
  def area({:rectangle, a, b}) do
    a*b
  end

  def area({:square, a}) do
  	a*a
  end
  
  def area({:circle, r}) do
  	r * r * 3.14
  end
end
```

      + clause order is matter, the runtime tries to select the clauses, using the order in the source code

    + Guards


      + The guard is a logical expression that adds further conditions to the pattern

```elixir
defmodule TestNum do
  def test(x) when x < 0 do
  	:negative
  end
  
  def test(x) when x == 0 do
  	:zero
  end
  
  def test(x) when x > 0 do
  	:positive
  end
end
```

      + operators and functions allowed in guards

        + Comparison operators (=\=, !=, =\{{< logseq/mark >}}, !{{< / logseq/mark >}}, >, <, <=, and >=)

        + Boolean operators (and and or) and negation operators (not and !)

        + Arithmetic operators (+, -, *, and /)

        + Type-check functions from the Kernel module (e.g., is_number/1, is_atom/1, and so on)

        + https://hexdocs.pm/elixir/patterns-and-guards.html#guards

      + when error raised inside guard, it won't propagate, guard expression will return `false`. The corresponding clause won’t match, but some other clause might

    + Multiclause lambdas


      + general lambda syntax

```elixir
fn
  pattern_1, pattern_2 ->
  	...
  pattern_3, pattern_4 ->
  	...
  ...
end
```

```bash
iex(3)> test_num =
fn
  x when is_number(x) and x < 0 -> :negative
  x when x == 0 -> :zero
  x when is_number(x) and x > 0 -> :positive
end
```

  + Conditionals

    + Branching with multiclause functions

```bash
iex(1)> defmodule Polymorphic do
  def double(x) when is_number(x), do: 2 * x
  def double(x) when is_binary(x), do: x <> x
end

iex(2)> Polymorphic.double(3)
6

iex(3)> Polymorphic.double("Jar")
"JarJar"
```

    + Classical branching expressions

      + if and unless

```elixir
if condition do
	...
else
	...
end

if condition, do: something, else: another_thing

unless result == :error, do: send_notification(...)
```

      + cond

```elixir
cond do
  expression_1 ->
  	...
  expression_2 ->
  	...
  ...
end
```

      + case

```elixir
case expression do
  pattern_1 ->
  	...
  pattern_2 ->
  	...
  ...
end
```

        + no differences between case and multiclause functions

    + The with expression

```elixir
def extract_user(user) do
  case extract_login(user) do
    {:error, reason} ->
      {:error, reason}
    {:ok, login} ->
      case extract_email(user) do
      	{:error, reason} ->
      	  {:error, reason}
      	{:ok, email} ->
      	  case extract_password(user) do
            {:error, reason} ->
              {:error, reason}
            {:ok, password} ->
      	  	  %{login: login, email: email, password: password}
      	  end
      end
  end
end


def extract_user(user) do
  with {:ok, login} <- extract_login(user),
    {:ok, email} <- extract_email(user),
    {:ok, password} <- extract_password(user) do
    {:ok, %{login: login, email: email, password: password}}
  end
end
```

  + Loops and iterations

    + The principal looping tool in Elixir is *recursion*

```elixir
defmodule ListHelper do
  def sum([]), do: 0
  
  def sum([head | tail]) do
  	head + sum(tail)
  end
end
```

    + Tail function calls

      + If the last thing a function does is call another function (or itself), you’re dealing with a tail call

```elixir
def original_fun(...) do
  ...
  another_fun(...)
end
```

```elixir
defmodule ListHelper do
  def sum(list) do
  	do_sum(0, list)
  end
  
  defp do_sum(current_sum, []) do
  	current_sum
  end
  
  defp do_sum(current_sum, [head | tail]) do
  	new_sum = head + current_sum
  	do_sum(new_sum, tail)
  end
end
```

    + Recognizing tail calls

```elixir
def fun(...) do
  ...
  if something do
    ...
    another_fun(...) # Tail call
  end
end
```

```elixir
def fun(...) do
	1 + another_fun(...) # Not a tail call
end
```

        + After another_fun finishes, you must increment its result by 1 to compute the final result of fun

      + https://github.com/sasa1977/elixir-in-action/blob/3rd-edition/code_samples/ch03/recursion_practice_tc.ex

    + Higher-order functions

      + A higher-order function is a type of function that takes one or more functions as its input or returns one or more functions (or both)

```bash
iex(1)> Enum.each(
[1, 2, 3],
fn x -> IO.puts(x) end
)
1
2
3
```

    + Comprehensions

```bash
iex(4)> multiplication_table =
for x <- 1..9,
    y <- 1..9,
    into: %{} do
  {{x, y}, x*y}
end
```

    + Streams

      + A stream is a special kind of enumerable that can be useful for doing lazy composable operations over anything enumerable

```bash
iex(7)> employees
  |> Stream.with_index()
  |> Enum.each(fn {employee, index} ->
  IO.puts("#{index + 1}. #{employee}")
  end)
```
