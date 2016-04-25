# Important topics

A person needs to have an attribute where we can store books/items he or she has checked out from the library (I called it `book_shelf`). 

The flow of checking out an item is
1. Search for the item in library
2. Check out the item

When an item is checked out we need to store information about it in the `book_shelf`with a return date. 

###What is YAML?

YAML is a file format for storing object trees and data serialization which is both human readable and computationally powerful. 

All the items in the library must be stored in a `yml` file. The file feeds to be structured this way.

!FILENAME lib/data.yml
```yml
---
- :item:
    :title: Alfons bråkar
    :author: T. Ochman
  :available: true
  :return_date: 
- :item:
    :title: Alfons leker
    :author: A. Andersson
  :available: false
  :return_date: '2016-05-25'
- :item:
    :title: Alfons bygger koja
    :author: T. Ochman
  :available: true
  :return_date: 
- :item:
    :title: Bible
    :author: TGAOTU
  :available: true
  :return_date: 
```

Now, this can be parsed to a collection. Open Irb.

```bash
$ irb
2.2.3 :001 > require 'yaml'
 => true 
2.2.3 :002 > collection = YAML.load_file('./lib/data.yml')
 => [{:item=>{:title=>"Alfons bråkar", :author=>"T. Ochman"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Alfons leker", :author=>"A. Andersson"}, :available=>false, :return_date=>"2016-05-25"}, {:item=>{:title=>"Alfons bygger koja", :author=>"T. Ochman"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Bible", :author=>"TGAOTU"}, :available=>true, :return_date=>nil}]
```


 