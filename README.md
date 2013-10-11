# bag_of_holding

Cache expensive API calls using MongoDB.

## Installation

Add this line to your application's Gemfile:

    gem 'bag_of_holding'

And then execute:

    $ bundle


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

You can add this module to your `ExpensiveOperation`.

```ruby
class ExpensiveOperation
  include BagOfHolding
  def get_value(with, some, args)
    # ...
  end
end
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
