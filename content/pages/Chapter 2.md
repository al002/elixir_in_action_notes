---
title: Chapter 2
tags:
categories:
date: 2025-01-04
---
Organizing code

  + Modules


    + A module is a collection of functions, somewhat like a namespace

```elixir
defmodule Geometry do
  def rectangle_area(a, b) do
  	a*b
  end
end
```

  + Functions


```elixir
def rectangle_area(a, b) do
	...
end
```

```elixir
def rectangle_area(a, b), do: a * b
```

    + Arity


      + the number of arguments a function receives

      + A function is uniquely identified by its containing module, name, and arity

      + Rectangle.area/2, where /2 denotes the function’s arity.

      + same name but different arities are two different functions

      + commonly, a lower-arity function delegates to a higher-arity function, providing some default arguments

      + `\\` operator followed by the argument’s default value

```elixir
defmodule Calculator do
	def add(a, b \\ 0), do: a + b
end
```

        + generates two functions

```elixir
defmodule Calculator do
  def add(a), do: add(a, 0)
  def add(a, b), do: a+b
end
```

      + cannot accept a variable number of arguments

    + Visibility


      + `def` macro is `exported`

      + `defp` macro is private

        + can be used only inside the module it’s defined in

  + Imports and alias


```elixir
# Import

defmodule MyModule do
  import IO

  def my_function do
  	puts "Calling imported function."
  end
end
```

    + Importing a module allows to call its public functions without prefixing them with the module name

```elixir
# Alias

defmodule MyModule do
  alias IO, as: MyIO

  def my_function do
  	MyIO.puts("Calling imported function.")
  end
end

defmodule MyModule do
  alias Geometry.Rectangle, as: Rectangle
  
  def my_function do
  	Rectangle.area(...)
  end
end
```

  + Module attributes

    + used as compile-time constants

```elixir
defmodule Circle do
  @pi 3.14159
  
  def area(r), do: r*r*@pi
  
  def circumference(r), do: 2*r*@pi
end
```

    + attribute can be registered

      + stored in the generated binary

      + can be accessed at run time

      + @module, @doc

```elixir
defmodule Circle do
  @moduledoc "Implements basic circle functions"
  @pi 3.14159

  @doc "Computes the area of a circle"
  def area(r), do: r*r*@pi

  @doc "Computes the circumference of a circle"
  def circumference(r), do: 2*r*@pi
end
```

      + Type specification (typespecs)

        + provide type information for functions

```elixir
defmodule Circle do
  @pi 3.14159

  @spec area(number) :: number
  def area(r), do: r*r*@pi

  @spec circumference(number) :: number
  def circumference(r), do: 2*r*@pi
end
```

  + Type system


    + Numbers


      + can be integers or floats

      + operator `/` always returns a float

    + Atoms


      + literally named constants

```elixir
:an_atom
```

      + consists of `text` and `value`

        + `text` is what ever after colon character `:`

          + At runtime, `text` kept in the `atom table`

        + `value` is data goes into the variable

```elixir
variable = :some_atom
```

          + it’s a reference to the atom table

          + `variable` not contain entire text, but a reference to `atom table`

          + memory consumption is low

          + comparisons is fast

          + code is readable

      + Aliases

        + Omit the beginning colon

        + Start with an uppercase character

```elixir
AnAtom
```

        + transformed into `:"Elixir.AnAtom"

```bash
iex(1)> AnAtom == :"Elixir.AnAtom"
true
```

        + implicit add `Elixir` prefix

          + if an alias already contains `Elixir`, prefix is not added

```bash
iex(2)> AnAtom == Elixir.AnAtom
true
```

        + Module aliases

```elixir
alias IO, as: MyIO
```

            + `MyIO` should transformed into `IO`

            + `IO` is atom

              + transformed into `Elixir.IO`

```bash
iex(5)> MyIO == Elixir.IO
true
```

        + As booleans

          + `:true` `true`

          + `:false` `false`

        + nil

          + `:nil`

          + `nil`

          + `nil` and `false` is falsy value, everything else is truthy

          + If all expressions evaluate to a falsy value, the result of the _last expression_ is returned.

    + Tuples

```bash
iex(1)> person = {"Bob", 25}
{"Bob", 25}

iex(3)> put_elem(person, 1, 26)
{"Bob", 26}
```

      + appropriate for grouping a small, fixed number of elements together

    + Lists

      + manage dynamic, variable-sized collections of data

```bash
iex(1)> prime_numbers = [2, 3, 5, 7]
[2, 3, 5, 7]
```

      + work like singly linked lists

      + most of the operations on lists have an O(n) complexity

        + include `Kernel.length/1` function

    + Immutability


      + modifying tuples

        + always a complete, shallow copy of the old versioin

      + modifying list

        + shallow copies of the first n – 1 elements followed by the modified element. After that, the tails are completely shared

      + benefits

        + side-effect-free functions and data consistency

    + Maps


```elixir
iex(1)> empty_map = %{}

iex(2)> squares = %{1 => 1, 2 => 4, 3 => 9}

iex(3)> squares = Map.new([{1, 1}, {2, 4}, {3, 9}])
%{1 => 1, 2 => 4, 3 => 9}
```

      + 

    + Binaries and bitstrings

```bash
iex(1)> <<1, 2, 3>>
<<1, 2, 3>>

iex(2)> <<256>>
<<0>>

iex(5)> <<257::16>>
<<1, 1>>
```

      + bitstring

        + total size of all the values isn’t a multiple of 8

```bash
iex(7)> <<1::1, 0::1, 1::1>>
<<5::size(3)>>
```

        + concat bitstring

```bash
iex(8)> <<1, 2>> <> <<3, 4>>
<<1, 2, 3, 4>>
```

    + Strings

      + Strings are represented using either a binary or a list type

      + Binary string

```bash
iex(1)> "This is a string"
"This is a string"

iex(2)> "Embedded expression: #{3 + 0.14}"
"Embedded expression: 3.14"
```

        + sigils syntax

```bash
iex(5)> ~s(This is also a string)
"This is also a string"

iex(6)> ~s("Do... or do not. There is no try." -Master Yoda)
"\"Do... or do not. There is no try.\" -Master Yoda"

iex(7)> ~S(Not interpolated #{3 + 0.14})
"Not interpolated \#{3 + 0.14}"
```

          + 

      + Character lists

```bash
iex(1)> IO.puts([65, 66, 67])
ABC

# ~c sigil
iex(2)> IO.puts(~c"ABC")
ABC

# single quote
iex(3)> IO.puts('ABC')
ABC
```

        + better use `~c` sigil

        + charlist not compatible with binary string

          + some functions work only with character lists

            + pure Erlang library

            + `String.to_charlist/1` convert a binary string to charlist

        + prefer binary string

    + Functions

```elixir
square = fn x ->
	x*x
end

Enum.each(
  [1, 2, 3],
  fn x -> IO.puts(x) end
)

Enum.each(
  [1, 2, 3],
  &IO.puts/1
)


iex(7)> lambda = fn x, y, z -> x * y + z end

iex(8)> lambda = &(&1 * &2 + &3)

```

      + Closures

        + by holding a reference to a lambda, you indirectly hold a reference to all variables it uses, even if those variables are from the external scope

    + Higher-level types


      + Range


        + It's enumerable

```bash
iex(1)> range = 1..2
```

        + represented as a map

          + memory footprint is small and constant

      + Keyword

        + Special case of a list


          + each element is a two-element tuple


            + first is atom

            + second can be of any type

```bash
iex(1)> days = [{:monday, 1}, {:tuesday, 2}, {:wednesday, 3}]

iex(2)> days = [monday: 1, tuesday: 2, wednesday: 3]
```

        + Allows you to omit the square brackets if the last argument is a keyword list

```bash
iex(8)> IO.inspect([100, 200, 300], width: 3, limit: 1)
[100,
...]
```

      + MapSet

        + The implementation of a set

          + store of unique values

```bash
iex(1)> days = MapSet.new([:monday, :tuesday, :wednesday])
MapSet.new([:monday, :tuesday, :wednesday])


```

      + Times and dates

```bash
# Date
iex(1)> date = ~D[2023-01-31]
~D[2023-01-31]

iex(2)> date.year
2023

iex(3)> date.month
1

# Time
iex(1)> time = ~T[11:59:12.00007]

# NativDateTime
iex(1)> naive_datetime = ~N[2023-01-31 11:59:12.000007]

# DateTime, work with datetimes and supports time zones
iex(1)> datetime = ~U[2023-01-31 11:59:12.000007Z]
```

      + DateTime

    + IO lists


      + Special sort of list that’s useful for incrementally building output that
will be forwarded to an I/O device

      + Element must one of following

        + An integer in the range of 0 to 255

        + A binary

        + An IO list

```bash
iex(1)> iolist = [[[~c"He"], "llo,"], " worl", "d!"]
```

      + appending to an IO list is O(1) because you can use nesting

```bash
iex(3)> iolist = []
iolist = [iolist, "This"]
iolist = [iolist, " is"]
iolist = [iolist, " an"]
iolist = [iolist, " IO list."] 
```

      + 

  + Operators


    + | Operator | Decription |
| \{{< logseq/mark >}}\=, \!{{< / logseq/mark >}} | Strict equality/inequality |
| ==,!= | Weak equality/inequality |
| <, >, >=, <= | Less than, greater than, less than or equal, greater than or equal |

  + Macros


    + perform powerful code transformations at compile time

```elixir
unless some_expression do
  block_1
else
  block_2
end

# tranform to
if some_expression do
  block_2
else
  block_1
end
```

  + Understanding the runtime

    + When start runtime, an OS process for the BEAM instance is started

    + VM keeps track of all modules loaded in-memory

      + If module is not loaded

        + tries to find the compiled module file—the bytecode—on the disk

    + Module name is alias correspond to `:"Elixir:ModuleName"`


      + `Geometry` correspond to `:"Elixir.Geometry"`

      + `elixirc` generate `.beam` file

        + multiple module in one file, `elixirc` will generate multiple `.beam` file

      + When call function of a module

        + The VM looks for the file in the current folder and then in the code paths

        + code path are used: `:code.get_path`

    + Erlang module use simple filenames


      + `:code.get_path`

      + `xyz.beam`

        + `xyz` is the expanded form of an alias

          + `Elixir.MyModule` when the module is named `MyModule`

    + Dynamically calling functions


```bash
iex(1)> apply(IO, :puts, ["Dynamic function call."])
Dynamic function call.
```

    + Starting the runtime

      + interactive shell

        + `iex`

        + shell is *interprets* input, not performant as compiled code

        + but modules are always compiled

      + running scripts

        + `elixir mys_ource.ex`

        + The BEAM instance is started.

        + The file my_source.ex is compiled in-memory, and the resulting modules are loaded to the VM. No .beam file is generated on the disk.

        + Whatever code resides outside of a module is interpreted.

        + Once everything is finished, BEAM is stopped.

        + `--no halt` make BEAM instance not to terminate

      + the `mix` tool

        + Whenever you need to build a production-ready system, mix is your best option.

      + 
