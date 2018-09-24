## Important topics

A person needs to have an attribute where we can store books/items he or she has checked out from the library (I called it `book_shelf`). 

The flow of checking out an item could be: 

1. Search for the item in library
2. Check out the item

When an item is checked out we need to store information about it in the `book_shelf` with a return date. 

## What is YAML?

YAML is a file format for storing object trees and data serialization which is both human readable and computationally powerful. 

All the items in the library must be stored in a `yml` file. The file feeds to be structured this way.

_lib/data.yml_

```yml
---
- :item:
    :title: Alfons och soldatpappan
    :author: Gunilla Bergström
  :available: true
  :return_date: 
- :item:
    :title: Skratta lagom! Sa pappa Åberg
    :author: Gunilla Bergström
  :available: false
  :return_date: '2016-05-25'
- :item:
    :title: Osynligt med Alfons
    :author: Gunilla Bergström
  :available: true
  :return_date: 
- :item:
    :title: Pippi Långstrump
    :author: Astrid Lindgren
  :available: true
  :return_date: 
- :item:
    :title: Pippi Långstrump går ombord
    :author: Astrid Lindgren
  :available: true
  :return_date: 
```
## Reading a YML file

The YML file can be parsed as an object and stored in a variable. Let's try that and call the variable `collection`. 

Open IRB and issue the following commands: (note that your IRB console can/will look a bit different than the example below, depending on what version of ruby you are running on your computer)

```bash
$ irb
2.2.3 :001 > require 'yaml'
 => true 
2.2.3 :002 > collection = YAML.load_file('./lib/data.yml')
 => [{:item=>{:title=>"Alfons och soldatpappan", :author=>"Gunilla Bergström"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Skratta lagom! Sa pappa Åberg", :author=>"Gunilla Bergström"}, :available=>false, :return_date=>"2016-05-25"}, {:item=>{:title=>"Osynligt med Alfons", :author=>"Gunilla Bergström"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Pippi Långstrump", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Pippi Långstrump går ombord", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil}]
```

**Knowing this you can create a collection of books for your library.**

## Writing to a YML file
If we introduce any changes to the object (in the `collection` variable we created in the example above), we would like to make then persistent, meaning we would like to save the new information in the YAML file.  We need to update (write) to the same file where we got the data from. For example, if we do this in IRB

```bash
# To start with, we modify the first object in the collection
# by setting `:available` to `false`
2.2.3 :019 > collection[0][:available] = false
 => false
 # And now, we open the YML file and store the entire `collection` again. 
2.2.3 :020 > File.open('./lib/data.yml', 'w') { |f| f.write collection.to_yaml }
 => 567
 ```
 
**Knowing this, you will be able to build a method to update the collection of books in the libarary after th check-out has been performed.**


## Searching in a Hash

Let's say that you have the above collection of books. Now, you want to search for a specific item (book). If we would like to search for "Pippi Långstrump", we could do something like this:

```bash
2.2.3 :002 > collection.detect { |obj| obj[:item][:title] == "Pippi Långstrump"  }
 => {:item=>{:title=>"Pippi Långstrump", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil} 
```

So, `collection` is an instance of the Hash class. We are calling `#detect` on it. What does the `#detect` method do?

Note that `#detect` will return an **exact** match, so it requires that you know the exact value of the key you are searching for. 

How about if you just know part of the title of the book you are searching for? In the method above we use the equal operator (`==`), but what if there is another way? Well, we can make use of `#include?`. 

Try this out (Note that we are only passing in "Pippi"). 

```bash
2.2.3 :004 > collection.detect { |obj| obj[:item][:title].include? "Pippi"  }
 => {:item=>{:title=>"Pippi Långstrump", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil} 
```

This searches through the collection and returnes the first object it finds that matches the criteria - in this case the part of the title. But wait, in our collection we have 2 books with the word "Pippi" in the title.

Perhaps it would be better if we returned both of them with our search method?

Try the following example, and note that I'm using `#select` rather than `#detect` on my `collection`.

```bash
2.2.3 :005 > collection.select { |obj| obj[:item][:title].include? "Pippi"  }
 => [{:item=>{:title=>"Pippi Långstrump", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil}, {:item=>{:title=>"Pippi Långstrump går ombord", :author=>"Astrid Lindgren"}, :available=>true, :return_date=>nil}]
```

And now I get both objects back. 

**Knowing this you can build a `search` method that will allow users to find a book the want to check out.**

```

 




 