---
layout: post
title: "Ramos - a new programming language I've been building"
image: "ramos"
---

For the last 3 months I'm working on a side project, a new programming language called
Ramos, small, indentation-driven, immutable and strict, built with Rust.

You can try it at [andrewsaguiar.com/ramos](https://www.andrewsaguiar.com/ramos/), or see the
codes at [github.com/andrewaguiar/ramos](https://github.com/andrewaguiar/ramos).

### Why a new language

In the beginning I started this pet project to test other AI. beyond claude I also used
Qwen, Deepseek and Zai GLM, to compare how each of them perform.

I always liked Elixir's immutability and its actor model, and I also like how Python uses
indentation instead of `{}`. Ramos is my try of putting the two together, with a bit of Ruby
style too, like `person.hello()` calls. The idea is to keep the language small, with few ways
of doing the same thing.

The philosophy is around 4 ideas:

  - **Immutable**: every value (list, tuple, map, struct) are persistent, nothing changes in
    place. To "change" something you build a new value and rebind the name to it.
  - **Strict**: the rules are not just a style guide, they are hard errors that stop the
    interpreter before your program runs.
  - **Limited**: one clear way of doing a thing instead of many.
  - **Opinionated**: when there is still a choice left, Ramos makes it for you.

### A little example

```elixir
module Main
  function main()
    total = (2 + 3) * 4 - 6 / 2
    doubled = double_all([1, 2, 3, 4])
    println("total is #{total}")
    println("doubled is #{doubled}")

  helper double_all(list)
    case list
      [] -> []
      [head | tail] -> [head * 2] ++ double_all(tail)
```

Everything is an expression, pattern matching is use everywhere, and string interpolation
with `#{}` works the same way it does in Ruby or Elixir.

### Actors

The Elixir actor model is one of my favorite things, so Ramos have it too. An actor is just a
module that implements `call(f, args, state, config)` and returns a `(reply, new_state)` tuple.

```elixir
module Cache
  implements Actor

  function call(f, args, state, config)
    case f
      :get ->
        [key] = args
        (Map.get(state, key, nil), state)
      :set ->
        [key, value] = args
        (:ok, Map.put(state, key, value))

  function start()
    start_actor(:cache, Cache, {}, {})

  function get(key)
    call_actor(:cache, Cache, :get, [key])

  function set(key, value)
    call_actor(:cache, Cache, :set, [key, value])
```

```elixir
Cache.start()
Cache.set("key", "v2")
value = Cache.get("key")     # == "v2"
```

`call_actor` waits for the reply, `cast_actor` sends and forgets, and each actor processes it's
own mailbox in order, running on real threads.

### Trying it

The whole project is a single Rust crate, you build it once and get a `ramos` binary that do
everything:

```elixir
cargo build --release
ramos new pet-project
ramos run pet-project
```

A few other commands worth knowing: `ramos repl` for a interactive prompt, `ramos check
file.rmo` to check the strict rules without running the code (good for CI), and `ramos learn`,
that prints a full crash course of the language in the terminal, made to be read by a person
or given to an AI agent.

It is still a early, small project, but already fun to write real programs with it. If you
give it a try let me know what you find.
