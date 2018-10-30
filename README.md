# Result

Result is a module for handling functions returning a `t:Result.t/0`.
This module is inspired by the f# Result module.
A result can be either the tuple {:ok, term} where term will be the expected return value of a function,
or the tuple {:error, term} where term will be an explanation of what went wrong while executing a function.

Using this module, it will be possible to combine functions that return a `t:Result.t/0`, and functions that take the value contained by the ok variant.
In the case one of the functions returns an error variant, subsequent functions expecting an ok result can be prevented from being executed.
Also, functions can be connected that will only execute in the case of an error.

## Examples

```elixir
defmodule ResultExample do

  def divide(0, _), do: {:error, :zero_division_exception}
  def divide(0.0, _), do: {:error, :zero_division_exception}
  def divide(x, y), do: Result.return(x / y)
  def subtract(x, y), do: Result.return(x - y)

end

ResultExample.divide(4, 2)
|> Result.bind(fn x -> ResultExample.subtract(x, 2) end)
{ :ok, 0.0}
```

```elixir
ResultExample.divide(4, 2)
|> Result.bind(fn x -> ResultExample.subtract(x, 2) end)
|> Result.bind(fn x -> ResultExample.divide(x, 2) end)
|> Result.bind(fn x -> ResultExample.subtract(x, 2) end)
{:error, :zero_division_exception}
```

```elixir
iex> ResultExample.divide(0, 2)
...> |> Result.or_else(2)
2
```

```elixir
ResultExample.divide(0, 2)
|> Result.or_else_with(fn _err -> {:ok, 0} end)
{:ok, 0}
```

## Installation

Add `result` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:result, "~> 0.1.0"}
  ]
end
```

Documentation generated by [ExDoc](https://github.com/elixir-lang/ex_doc)
and published on [HexDocs](https://hexdocs.pm)
the docs can be found at [https://hexdocs.pm/result](https://hexdocs.pm/result)
