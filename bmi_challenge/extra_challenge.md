## Extra challenge

Here is a challenge for you. Do you remember the Fizz Buzz game we ware working on in the Prep-course? At that time we wrote the program using Ruby. Let's do it in JavaScript and implement a web interface using HTML and jQuery. 

I've already done most of the work for you. Your job will be to write some unit tests for the logic and implement the web part of the application. 

To successfully complete the challenge, I want you to deploy it using GitHub pages (also something we've done during the Prep-course, remember?)


Here is the Fizz Buzz game in JavaScript:

```js
function FizzBuzz() {
};

FizzBuzz.prototype._hasZeroReminder = function (number, divisor) {
    return (number % divisor === 0);
};

FizzBuzz.prototype.divisibleByThree = function (number) {
    return this._hasZeroReminder(number, 3);
};

FizzBuzz.prototype.divisibleByFive = function (number) {
    return this._hasZeroReminder(number, 5);
};

FizzBuzz.prototype.divisibleByFifteen = function (number) {
    return this._hasZeroReminder(number, 15);
};

FizzBuzz.prototype.fizz_buzz = function (number) {
    if (this.divisibleByFifteen(number)) {
        return "FizzBuzz";
    }
    else if (this.divisibleByFive(number)) {
        return "Buzz";
    }
    else if (this.divisibleByThree(number)) {
        return "Fizz";
    }
    else
        return number;
};
```


