# Money Open Exchange Rates + Historical (for Rails)
Simple fork, with an added `bank#exchange_at` based on `money-historical-bank` gem.

For now, the cache is based on `Rails.cache`, it doesn't follow OXR's cache logic.

My (intended) usage:

Money gem patch
```ruby
module MoneyExt
  module OxrMoneyExtender
    extend ActiveSupport::Concern

    included do
      def exchange_at(date, other_currency, &rounding_method)
        other_currency = Money::Currency.wrap(other_currency)
        if currency == other_currency
          self
        else
          @bank.exchange_at(date, self, other_currency, &rounding_method)
        end
      end
    end
  end
end
```
and in an initializer:
```ruby
Rails.application.reloader.to_prepare do
  Money.include(MoneyExt::OxrMoneyExtender)
end
```

And now you can call `exchange_at` on any money:
```ruby
Money.new(700, :USD).exchange_at(Date.new(2024,4,4), :EUR)
```
