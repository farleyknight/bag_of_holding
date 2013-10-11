# bag_of_holding

Cache expensive operations using MongoDB.

## Features

* Stores your expensive operations using an easy to use DSL.
* Configurable to invalidate cache globally or locally.



## Installation

Add this line to your application's Gemfile:

```ruby
gem 'bag_of_holding'
```

Execute bundler and finally install the initializer:

```bash
bundle
rails g bag_of_holding:install
```

## Usage

Let's say you have a class that computes the fibonaci numbers. You might have in your `lib/expensive_operation.rb`:

```ruby
class ExpensiveOperation
  def get_value(with, some, args)
    # ...
  end
end
```

This could be anything: An ActiveRecord query, an HTTP call, a complex mathematical operation. It gets called
semi-frequently. Less than chat room calls, but it's frequency causes performance problems.

In short, you need some caching.

Now, you could create a table in our SQL database to handle this. But once you've grown to 3 or 4 different types of
data that needs storing, you figure that it's best to have a more generic way to do this caching.

### Typical Use Case

You can add `BagOfHolding` as a module to your `ExpensiveOperation`.

```ruby
class ExpensiveOperation
  include BagOfHolding
  def get_value(with, some, args)
    # ...
  end
end
```

Now you can call that `get_value` like:

```ruby
expensive = ExpensiveOperation.new
expensive.cached.get_value(with, some, args) #=> Proceeds to call the method and then cache the result.
```

Later on, you may call this operation again, but this time it's cached:


```ruby
expensive = ExpensiveOperation.new
expensive.cached.get_value(with, some, args) #=> This should give you the same value as above, without performing the cache.
```

Note that this also works on classes as well:

```ruby
ExpensiveOperation.cached.expensive_class_method(with, some, args) #=> Computing value, and then caching
ExpensiveOperation.cached.expensive_class_method(with, some, args) #=> Returns cached value
```

### Debugging

This is all fine and dandy, until you start getting cache hits or cache misses that you're not expecting. The best
way to debug your caches is with the `caches` method on each object:

```ruby
user = User.find(4)
user.caches #=> List of all caches attached to user with id == 4.
```

Now instead of calling `expensive.get_value(...)` you can call `expensive.cached.get_value(...)`. This will
check the MongoDB for your expensive operation, and if it's there, hands that back. If not, it makes the call,
stores it, and hands it back.


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
