---
layout: post
title: "Ruby - The method_missing"
---

Ruby is a very dynamic language and has several metaprogramming features that can easy your life, 
here I present some of them.

## Method Missing

Everytime you call a method in a ruby object ruby tries to find this method definition in all hierarchy, 
for example calling the `to_s` method of Fixnum makes ruby to search it in Fixnum and Object. 
In case of ruby doesn't find the method it will be caught by ``method_missing`` declared in BasicObject.

```ruby
class A
end

a = A.new
a.testing # NoMethodError: undefined method `testing' for #<MyTest:0x007f90392568b0>
```

Considering that we can redefine the method_missing in our class we could change this error message like this.

```ruby
class A
  def method_missing(method_name, *args, &block)
    puts "#{method_name} with #{args.length} could not be found"
  end
end

a = A.new
a.testing # testing with 0 could not be found
a.testing(1, 2, 3) # testing with 3 could not be found
```

