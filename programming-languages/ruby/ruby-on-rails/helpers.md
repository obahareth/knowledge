# Helpers

## Readability

- [annotate_models](https://github.com/ctran/annotate_models) - Automatically generates attributes as comments at the top of files to see what attributes are in models, fixture files, specs, factories, and more. Example:

  ```ruby
  # == Schema Info
  #
  # Table name: line_items
  #
  #  id                  :integer(11)    not null, primary key
  #  quantity            :integer(11)    not null
  #  product_id          :integer(11)    not null
  #  unit_price          :float
  #  order_id            :integer(11)
  #
  
   class LineItem < ActiveRecord::Base
     belongs_to :product
  # ...
  ```

## Migrations

- [strong_migrations](https://github.com/ankane/strong_migrations) - Detect potentially dangerous migrations and prevent them from running by default, along with instructions on safer ways to do what you want.

## View Helpers

Rails already provides a ton of view helpers (e.g. `number_to_currency`), they're listed [here](https://guides.rubyonrails.org/action_view_overview.html#overview-of-helpers-provided-by-action-view).