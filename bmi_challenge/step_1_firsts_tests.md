## Firsts tests


Create a new file in your `spec` folder. Call it `person_spec.js`.

```shell
$ touch spec/person_spec.js
```

*** Note that I use the `snake case` format for my file names. You are free to use any format you want but be consistent. ***

We are going to write two test for our Person object. In order to calculate the BMI we will be needing a Person to have two attributes, Weight and Hight.

```js
# spec/person_spec.js

describe("Person", function() {
  var person;

  beforeEach(function() {
    person = new Person(90, 186);
  });

  it("should have weight of 90", function() {
    expect(person.weight).toEqual(90);
  });

  it("should have height of 186", function() {
    expect(person.height).toEqual(186);
  });
});
```

If you reload your `SpecRunner.html` you'll get two errors. 




