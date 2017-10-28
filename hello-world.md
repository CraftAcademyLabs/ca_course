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

!FILENAME tests/acceptance/hello-world-test.js
```javascript
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

We will make a small addition to this test file with a test for the `h1` tag with the words `Hello World` being displayed on the view.

!FILENAME tests/acceptance/hello-world-test.js
```javascript
test('template renders "Hello World"', function(assert) {
  visit('/hello-world');

  andThen(function() {
    assert.equal(find('h1').text(), 'Hello World');
  });
});

``` 

If we run the sets now, they will, of course fail. Let's do it anyway. In your terminal, in a separate tab, run the test command with the `--server` option, and keep it running.

```
$ ember test --server
``` 

A browser window will open and you should see the following.

![](/assets/ember-3-failing-tests.png)

The important take away at this stage is the message that `Assertion Failed: The URL '/hello-world' did not match any routes in your application`. There is simply no route `hello-world`. Let's create one and see if we can change the error message. 

Run the following generator to create a route.

```
$ ember g route hello-world
```

You will see that your tests will rerun and the browser window with the test output will reload. You should see a different output now. Probably something similar to this:

![](/assets/ember-4-changed-error-message.png)

Alright, the route is in place and one of our test is passing. Now let's make the second test case go green. 

Open up the application layout (`app/templates/application.hbs`) and remove the component that displays Ember's default welcome message. Your `application.hbs` should look like this: 

!FILENAME app/templates/application.hbs
```javascript
{{outlet}}
```

And finally, let's add some html to the template rendered when we call the `/hello-world` url. 

!FILENAME app/templates/hello-world.hbs
```html
<h1>Hello World</h1>
```
If you examine the browser window with your test output, you should see that all test are going 'green' (if you experience a different output, restart your test server before debugging your code and searching for typos). 

![](/assets/ember-5-tests-passing.png)

We will add a final test to this Hello World application. We want to make sure that the index url of the app redirects to the `/hello-world` url. 

!FILENAME tests/acceptance/hello-world-test.js
```javascript
test('visiting "/" redirects to "/hello-world"', function(assert) {
  visit('/');

  andThen(function() {
    assert.equal(currentURL(), '/hello-world');
  });
});
```

And in order to get this test to pass, we will add a redirect to the `index`  route. 

We need to create a new file (this time around, without using a generator) and add the following code. 

!FILENAME app/routes/index.js
```javascript
import Route from '@ember/routing/route';

export default Route.extend({
  beforeModel() {
    this.transitionTo('hello-world'); 
  }
});
```

![](/assets/ember-6-hello-world.gif)












