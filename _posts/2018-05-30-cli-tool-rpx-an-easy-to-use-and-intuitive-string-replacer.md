---
layout: post
title: "CLI Tool - RPX an easy to use and intuitive string replacer"
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

## Installation

Just download the binary in `bin/rpx` and add it to the path

```shell
wget https://raw.githubusercontent.com/andrewaguiar/rpx/master/bin/rpx
chmod +x rpx
```

Or clone the project and make the binary

```shell
git clone git@github.com:andrewaguiar/rpx.git
cd rpx
mix escript.build
```

Then add it to PATH

```shell
export PATH="$PATH:rpx_location"
```

## Usage

Type `rpx` to see instructions.

```shell
NAME
       rpx -- simple and powerfull string replacer

SYNOPSIS
       rpx <string-to-be-replaced> <replacement> [base-path] [-xar]

DESCRIPTION

       Rpx scans all allowed files recursively and shows all occurences of <string-to-be-replaced> in each file, then it
       asks for confirmation before replace all occurrences by <replacement>.

       The following options are available:

       --regex | -r
              Treats the <string-to-be-replaced> as a regex instead of a simple text (default false).
```

### Creating a bin

run `./make_dist` and the binary will be generated in `./bin`.
