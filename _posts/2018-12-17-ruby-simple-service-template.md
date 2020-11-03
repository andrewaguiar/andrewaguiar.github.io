---
layout: post
title: "Ruby - Simple service template"
image: "capybaha"
---

I suppose you guys know quite well that fat models and fat controllers are not a good thing.
Putting all logic in controllers is bad because you mix web stuff with business logic and
make the code more difficult to understand and maintain.

Knowing this several people suggested the service object as a good design pattern to solve this,
simple create an object that represents this logic (like a function) and call it from controller
or any other place.

A very popular project that implements this idea is [interactor](https://github.com/collectiveidea/interactor), from
its github page

> An interactor is a simple, single-purpose object.
> 
> Interactors are used to encapsulate your application's business logic. Each interactor represents one thing that your application does.

I like this idea but dislike the implementation, I think interactor got too complex and presented a very bad idea, **mixing
input and output in one magic intern object called context**, it makes things too magical and implicit, does not have clear
responsibility separation and together with [Organizers](https://github.com/collectiveidea/interactor#organizers) caused some
very tricky and hard to debug situations in the place I work.

I think a very simpler idea would be better, creating a simple boilerplate of a service object like the below does not require a
entire gem to be installed, keeps input and output separated (input == args, output == return) and solves the problem very well

```ruby
module Service
  module Base
    def self.call(*args)
      new.call(*args)
    end
  end
end
```

Usage

```ruby
class DoSomeProcessing
  include Service

  def call(job)
    puts "start processing [#{job}]"
    puts "processing [#{job}]"
    puts "finish processing [#{job}]"

    "#{job} done"
  end
end

# calling
result = DoSomeProcessing.call('write a post in blog')
puts result
# => "write a post in blog done"
```
