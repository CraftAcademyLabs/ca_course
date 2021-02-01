---
title: "Checkout Challenge"
author: [Thomas Ochman, Craft Academy]
date: Version 0.1
subject: "Ruby"
keywords: [Ruby, Example, Learn To Code]
subtitle: "Extra Challenge for Ruby Introduction"
titlepage: true
titlepage-color: f28e24
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "FFFFFF"
titlepage-rule-height: 2
...

# Checkout Challenge

Our client is an on-line marketplace, here is a sample of some of the products available on our site:


|Product code  | Name        | Price|
|:--------------:|-------------|:--------------------:|
|001           | Tie     | $9.25  |
|002           | Sweater | $45.00 |
|003           | Skirt   | $19.95 |


Our marketing team want to offer promotions as an incentive for our customers to purchase these items.

- If you spend over $60, then you get 10% off of your purchase.
- If you buy 2 or more ties then the price drops to $8.50.

Our check-out can scan items in any order, and because our promotions will change, it needs to be flexible regarding our promotional rules.

The interface to our checkout looks like this:

```ruby
co = Checkout.new(promotional_rules)
co.scan(item)
co.scan(item)
price = co.total
```

Implement a checkout system that fulfills these requirements.

```ruby
Test data
---------
Basket: 001,002,003
Total price expected: $66.78

Basket: 001,003,001
Total price expected: $36.95

Basket: 001,002,001,003
Total price expected: $73.76
```

# Solution

Solution to the Checkout Challenge by one of CraftAScademy's former students, Amber.

```ruby
class Checkout

  attr_reader :total, :cart, :promotion, :pre_price, :products, :product

  def initialize(promo = {})
    @promotion = promo[:promo]
    @cart = []
    @products = set_products
    validate_promo(@promotion)
  end

  def scan(item)
    @cart << find_item(item)
    total
  end

  def remove_from_cart(item)
    position_in_hash = 0
    while @cart[position_in_hash] != find_item(item)
      position_in_hash += 1
      if position_in_hash >= @cart.length
        raise 'Item not found'
      end
    end
    @cart.slice!(position_in_hash)
    total
  end

  def total
    pre_price = 0.0
    cart.each do |x|
      pre_price += x[:price]
    end
    give_promotion(pre_price)
    @total.round(2)
  end

  private

  def find_item(item)
    @product = @products.detect { |x| x[:name] == item}
  end

  def give_promotion(pre_price)
    case promotion
    when '60bucks'
      sixty_bucks_promo(pre_price)
    when 'hotdogs'
      @total = pre_price - hot_dog_promo('hotdog')
    when 'both'
      check_discounts(pre_price)
    else
      @total = pre_price
    end
  end

  def check_discounts(pre_price)
    if sixty_bucks_promo(pre_price) == pre_price
      hot_dog_discount = hot_dog_promo('hotdog')
    else
     hot_dog_discount = hot_dog_promo('hotdog')*0.9
    end
    @total = sixty_bucks_promo(pre_price) - hot_dog_discount
  end

  def validate_promo(promo)
    unless promo == '60bucks' || promo == 'hotdogs' || promo == 'both' || promo.nil?
      raise 'Invalid promo'
    end
  end

  def sixty_bucks_promo(pre_price)
    pre_price > 60.0 ?
     @total = pre_price*0.9 : @total = pre_price
  end

  def hot_dog_promo(item)
    count = Hash.new(0)
    cart.each do |x|
      count[x] += 1
    end
    count[find_item(item)] >= 2 ? (count[find_item(item)]*0.75) : 0
  end

  def set_products
    [
      {
        code: 1,
        name: 'blanket',
        desc: 'A soft and fluffy blanket',
        price: 19.95
      },
      {
        code: 2,
        name: 'lounger',
        desc: 'A sturdy and attractive beach chair',
        price: 45.0
      },
      {
        code: 3,
        name: 'hotdog',
        desc: 'An overpriced meat product',
        price: 9.25
      }
    ]
  end
end
```
