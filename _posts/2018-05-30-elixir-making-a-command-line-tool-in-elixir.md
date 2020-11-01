---
layout: post
title: "Elixir - Making a command line tool in elixir"
image: "closed"
---

When I started programming in Ruby and Elixir some years ago I noticed that I've started using terminal more often
(previously I worked with Java + Eclipse so terminal wasn't so required).

Several times I wanted to perform a simple task: search and replace occurencies in all source code. Firstly I
tried and managed to use `sed` to this task.

```
sed -i 's/SEARCH_REGEX/REPLACEMENT/g' INPUTFILE
```

Even `sed` working well I missed some features like:

  - List only git managed files.
  - See a list of all occurrencies before replacing.
  - Be able to choose which occurrencies would be replaced.

So I decided to create a command line tool with these features.

## RPX

Rpx [github.com/andrewaguiar/rpx](https://github.com/andrewaguiar/rpx) usage is pretty simple, just type `rpx <term> <replacement>` and it will present all occurencies of all git managed
files `git ls-files`.

![rpx preview](/public/images/posts/rpx-preview.png)

You can then choose which occurencies you would like to replace by typing ids, ranges or even (a)ll.

## Programming the RPX

The entrypoint of rpx is Rpx.CLI module, basically it deals with options, show usage and validates the input

```elixir
defmodule Rpx.CLI do
  alias Rpx.Colors

  def main(args) do
    args |> parse_args |> run
  end

  defp run({args_config, [term, replacement], []}) do
    Rpx.run(args_config, term, replacement)
  end

  defp run(_) do
    IO.puts(
      """
      #{Colors.bold("NAME")}
             rpx -- simple and powerfull string replacer based on non gitignore files
      #{Colors.bold("SYNOPSIS")}
             rpx <string-to-be-replaced> <replacement> [base-path] [-r]
      #{Colors.bold("DESCRIPTION")}
             Rpx scans all git ls-files recursively and shows all occurences of <string-to-be-replaced> in each file, then it
             asks for confirmation before replace all occurrences by <replacement>.
             The following options are available:
             #{Colors.bold("--regex | -r")}
                    Treats the <string-to-be-replaced> as a regex instead of a simple text (default false).
      """
    )
  end

  defp parse_args(args) do
    switches = [regex: :boolean]
    aliases = [r: :regex]

    OptionParser.parse(args, switches: switches, aliases: aliases)
  end
end
```

In `parse_args` function we use `OptionParser` to parse some arguments (using full version and aliases).

```
  defp parse_args(args) do
    switches = [regex: :boolean]

    aliases = [r: :regex]

    OptionParser.parse(args, switches: switches, aliases: aliases)
  end
```

After parsing args we send it to run and in case of success we run the program itself, otherwise we show instructions.

```
term = %Rpx.Term{value: term_string, regex: args_config[:regex] != nil}

all_files = Searcher.find()

matched_lines = Scanner.generate(all_files, term)

Scanner.print(matched_lines, term)

Summarizer.print(matched_lines)

run_replacer(matched_lines, term, replacement)
```

