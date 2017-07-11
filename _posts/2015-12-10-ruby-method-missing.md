---
layout: post
title: "Ruby - Understanding method_missing"
image: "binocular-telescope"
---

Everytime you call a method in a ruby object ruby tries to find this method definition in all hierarchy, 
for example calling the `to_s` method of ``Fixnum`` makes ruby to search it in ``Fixnum`` and ``Object``.
In case of ruby doesn't find the method it will be caught by [method_missing](http://ruby-doc.org/core-2.1.0/BasicObject.html#method-i-method_missing) method declared in ``BasicObject``.

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
    puts "#{method_name} with #{args.length} arguments could not be found"
  end
end

a = A.new
a.testing # testing with 0 arguments could not be found
a.testing(1, 2, 3) # testing with 3 arguments could not be found
```

Now lets create something more complex and useful, lets create a class to represent our enviroment, also we want to execute some code only in a specific enviroment. We could create a method that receives the enviroment and a block of code

``` ruby
class Enviroment
  def initialize(env)
    @env = env
  end

  def on_env(env)
    if env == @env
      yield
    end
  end
end
```

And use this way

```ruby
enviroment = Enviroment.new "development"

# now we can call any on_<enviroment>
enviroment.on_env "production" do
  puts "you are in production env"
end

enviroment.on_env "development" do
  puts "you are in development env"
end
```

Or we can use the method_missing to create a more dynamic, DSLic implementation

```ruby
class Enviroment
  def initialize(env)
    @env = env
  end

  def method_missing(method_name, *args, &block)
    # lets check if the called methods starts with on_<env>
    if match = /on_(\w+)/.match(method_name.to_s)
      # if yes we can execute the block given
      block.call if match[1] == @env
    else
      # otherwise we just delegate to super.method_missing
      super
    end
  end
end
```

Now instead of pass the enviroment we need only call a method called **on_**env.

```ruby
enviroment = Enviroment.new "development"

# now we can call any on_<enviroment>
enviroment.on_production do
  puts "you are in production env"
end

enviroment.on_development do
  puts "you are in development env"
end
```

## References
  - BasicObject: [ruby-doc.org/core-2.1.0/BasicObject.html](http://ruby-doc.org/core-2.1.0/BasicObject.html).
  - Regexp: [ruby-doc.org/core-2.1.0/Regexp.html](http://ruby-doc.org/core-2.1.0/Regexp.html)
