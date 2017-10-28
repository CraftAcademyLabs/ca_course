## Getting started with Ember
### Say "Hello World" - with acceptance tests

Install ember-cli if not installed
```
$ npm install -g ember-cli
```
Once ember installation is compleate, check your ember version. You should see an output similar to this:

```
$ ember -v
ember-cli: 2.16.2
node: 7.4.0
os: darwin x64
```

Alright, let's scaffold a new Ember application and get thigs moving
```
$ ember new hello-world
```

The `ember new` command generates a lot of scaffolded code, installs all necessary dependencies and initializes a `git` repository in the project folder. Once installation is done, `cd` into the `hello-world` folder.
```
$ cd hello-world
```

![](/assets/ember-1.png)

Now, let's fire up the local server and see what we've got from the application scaffold.

``` 
$ ember s
```

Visit `http://localhost:4200` in your browser. You should see the following page

![](/assets/ember-2-scaffold-welcome.png)

### Our first test
Ember comes with a whole set of handy generators that hels us developers create some common files. For now, we will use the `acceptance-test` generator to get us started with our first tests. 

```
$ ember g acceptance-test hello-world
installing acceptance-test
  create tests/acceptance/hello-world-test.js
```
This generates a test file for us and adds a test case for accessing the `/hello-world` route. 


```
import { test } from 'qunit';
import moduleForAcceptance from 'hello-world/tests/helpers/module-for-acceptance';

moduleForAcceptance('Acceptance | hello world');

test('visiting /hello-world', function(assert) {
  visit('/hello-world');

  andThen(function() {
    assert.equal(currentURL(), '/hello-world');
  });
});
```






