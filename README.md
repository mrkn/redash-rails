# Redash and Rails integration

This library helps you to integrate re:dash and your Rails apps.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'redash-rails'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install redash-rails

## Usage

### Installing rails datasource

Use `redash-rails-install` command.

```console
$ redash-rails-install $REDASH_ROOT_DIR
```

### Helper library for rails queries

[lib/redash.rb](lib/redash.rb) provides helper functions to write queries.

The following script is an example usage of these helper functions:

```ruby
require 'redash'
include Redash

result = {}

add_result_column result, 'age', 'Age', 'integer'
add_result_column result, 'survived_count', 'Survived count', 'integer'
add_result_column result, 'dead_count', 'Dead count', 'integer'

summary = Passenger.find_each.inject({}) do |h, pas|
  if pas.age
    age = (pas.age % 10) * 10
    h[age] ||= []
    h[age][pas.survived] ||= 0
    h[age][pas.survived] += 1
  end
  h
end

summary.each do |age, age_summary|
  add_result_row(result, age: age, survived_count: age_summary[1], dead_count: age_summary[0])
end

commit_to_redash(result)
```

See [mrkn/redash_rails_demon](https://github.com/mrkn/redash_rails_demo) repository for more details.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/mrkn/redash-rails.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
