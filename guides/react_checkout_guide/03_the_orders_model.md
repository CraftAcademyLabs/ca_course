## The orders model

The next step we need to take is to generate an Order model to have something to pull out from the database.

`$ rails generate model order`

We also need to associate the `order` to the `order_items` that we have, and the `products`.

`$ rails generate model OrderItems order:references product:references`

Go ahead and go through the files that are generated and make sure that there are no problems.

We will now add specs to test the associations we created between the models.
In the newly generated `order_items_spec.rb` go ahead and add two specs.
You will need more than that but for this guide, we will limit ourselves to these specs.

```ruby
require 'rails_helper'

RSpec.describe OrderItem, type: :model do
  it { is_expected.to belong_to :order }
  it { is_expected.to belong_to :product }
end
```

`order_spec.rb`

```ruby
require 'rails_helper'

RSpec.describe Order, type: :model do
	it {is_expected.to have_many :order_items}
end
```

We need to make sure that the associations are present as well.

`order_item.rb`

```ruby
class OrderItem < ApplicationRecord
  belongs_to :order
  belongs_to :product
end
```

`order.rb`

```ruby
class Order < ApplicationRecord
	has_many :order_items
end
```

Run your migrations and then run your specs. Everything should be green like Bruce Banners alter ego.

![the hulk is happy](https://media.giphy.com/media/i3lbNZhnB1Jle/giphy.gif)

