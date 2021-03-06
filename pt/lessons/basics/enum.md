---
version: 1.3.0
title: Enum
---

Um conjunto de algoritmos para fazer enumeração em coleções.

{% include toc.html %}

## Enum

O módulo `Enum` inclui mais de 70 funções para trabalhar com enumeráveis. Todas as coleções que aprendemos na [lição anterior](../collections/), com exceção das tuplas, são enumeráveis.

Esta lição abrangerá apenas um subconjunto das funções disponíveis, no entanto, podemos examiná-las pessoalmente.
Vamos fazer um pequeno experimento no IEx.

```elixir
iex> Enum.__info__(:functions) |> Enum.each(fn({function, arity}) ->
...>   IO.puts "#{function}/#{arity}"
...> end)
all?/1
all?/2
any?/1
any?/2
at/2
at/3
...
```

Usando isso, é claro que temos uma grande quantidade de funcionalidades, e isso é por uma razão.
A enumeração é o núcleo da programação funcional e é uma coisa incrivelmente útil.
Ao aproveitar isso combinado com outras vantagens de Elixir, como a documentação sendo um cidadão de primeira classe como acabamos de ver, também pode ser incrivelmente poderoso para o desenvolvedor.

Para obter uma lista completa de funções, visite a documentação oficial do [`Enum`](https://hexdocs.pm/elixir/Enum.html); para enumeração preguiçosa use o módulo [`Stream`](https://hexdocs.pm/elixir/Stream.html).

### all?

Quando usando `all?/2`, e muitas das funções de `Enum`, provemos uma função para aplicar nos elementos da nossa coleção. No caso do `all?/2`, a coleção inteira deve ser avaliada como `true`, em outros casos `false` será retornado:

```elixir
iex> Enum.all?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 3 end)
false
iex> Enum.all?(["foo", "bar", "hello"], fn(s) -> String.length(s) > 1 end)
true
```

### any?

Diferente da anterior, `any?/2` retornará `true` se ao menos um elemento for `true`:

```elixir
iex> Enum.any?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 5 end)
true
```

### chunk_every

Se você necessita quebrar sua coleção em pequenos grupos, `chunk_every/2` é a função que você provavelmente está buscando:

```elixir
iex> Enum.chunk_every([1, 2, 3, 4, 5, 6], 2)
[[1, 2], [3, 4], [5, 6]]
```

Há algumas opções para `chunk_every/4` porém não vamos entrar nelas, revise [`a documentação oficial para esta função`](https://hexdocs.pm/elixir/Enum.html#chunk_every/4) para aprender mais.

### chunk_by

Se necessita agrupar uma coleção baseado em algo diferente do tamanho, podemos usar a função `chunk_by/2`. Ela recebe um enumerável e uma função, e quando o retorno desta função muda, um novo grupo é iniciado e começa a criação do próximo:

```elixir
iex> Enum.chunk_by(["one", "two", "three", "four", "five", "six"], fn(x) -> String.length(x) end)
[["one", "two"], ["three"], ["four", "five"], ["six"]]
```

### map_every

Algumas vezes quebrar uma coleção não é o suficiente para fazer exatamente o que você precisa. Se este é o caso, `map_every/3` pode ser muito útil para tratar apenas itens específicos da sua coleção (`nth`), sempre atingindo o primeiro:

```elixir
# Apply function every three items
iex> Enum.map_every([1, 2, 3, 4, 5, 6, 7, 8], 3, fn x -> x + 1000 end)
[1001, 2, 3, 1004, 5, 6, 1007, 8]
```

### each

Pode ser necessário iterar sobre uma coleção sem produzir um novo valor, para este caso podemos usar `each/2`:

```elixir
iex> Enum.each(["one", "two", "three"], fn(s) -> IO.puts(s) end)
one
two
three
:ok
```

__Nota__: A função `each/2` retorna um átomo `:ok`.

### map

Para aplicar uma função a cada elemento e produzir uma nova coleção use a função `map/2`:

```elixir
iex> Enum.map([0, 1, 2, 3], fn(x) -> x - 1 end)
[-1, 0, 1, 2]
```

### min

`min/1` retorna o valor mínimo de uma coleção:

```elixir
iex> Enum.min([5, 3, 0, -1])
-1
```

`min/2` faz o mesmo, porém permite especificar um valor mínimo padrão através de uma função quando o valor está vazio.

```elixir
iex> Enum.min([], fn -> :foo end)
:foo
```

### max

`max/1` retorna o valor máximo de uma coleção:

```elixir
iex> Enum.max([5, 3, 0, -1])
5
```

`max/2` é para `max/1` o que `min/2` é para `min/1`:

```elixir
iex> Enum.max([], fn -> :bar end)
:bar
```

### filter

A função `filter/2` nos permite filtrar uma coleção para incluir apenas os elementos que são avaliados como `true` usando uma função provida.

```elixir
iex> Enum.filter([1, 2, 3, 4], fn(x) -> rem(x, 2) == 0 end)
[2, 4]
```

### reduce

Com `reduce/3` podemos transformar nossa coleção em um único valor, para fazer isto aplicamos um acumulador opcional (`10` neste exemplo) que será passado a nossa função; se não prover um acumulador, o primeiro valor será usado:

```elixir
iex> Enum.reduce([1, 2, 3], 10, fn(x, acc) -> x + acc end)
16

iex> Enum.reduce([1, 2, 3], fn(x, acc) -> x + acc end)
6

iex> Enum.reduce(["a","b","c"], "1", fn(x,acc)-> x <> acc end)
"cba1"
```

### sort

Ordenar nossas coleções é fácil com uma das funções de ordenação.

`sort/1` usa a funcionalidade de ordenação do Erlang para determinar a ordem:

```elixir
iex> Enum.sort([5, 6, 1, 3, -1, 4])
[-1, 1, 3, 4, 5, 6]

iex> Enum.sort([:foo, "bar", Enum, -1, 4])
[-1, 4, Enum, :foo, "bar"]
```

`sort/2` nos permite utilizar nossa própria função de ordenação:

```elixir
# with our function
iex> Enum.sort([%{:val => 4}, %{:val => 1}], fn(x, y) -> x[:val] > y[:val] end)
[%{val: 4}, %{val: 1}]

# without
iex> Enum.sort([%{:count => 4}, %{:count => 1}])
[%{count: 1}, %{count: 4}]
```

### uniq_by

Podemos usar `uniq_by/2` para eliminar itens duplicados em nossas coleções:

```elixir
iex> Enum.uniq_by([1, 2, 3, 2, 1, 1, 1, 1, 1], fn x -> x end)
[1, 2, 3]
```
