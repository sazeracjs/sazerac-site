---
title: Guide
type: v0.4.1
order: 1
---

What is Sazerac?
----------------

Sazerac is a library for [data-driven testing](https://hackernoon.com/sazerac-data-driven-testing-for-javascript-e3408ac29d8c#.xppc8jjvo) in JavaScript. It works with [mocha](https://mochajs.org/), [jasmine](https://jasmine.github.io/), and [jest](https://facebook.github.io/jest/). Using Sazerac, and the data-driven testing pattern in general, will reduce the complexity and increase readability of your test code.


Why Use It?
-----------

Let's say you have a function `isPrime()`. When given a number, it returns `true` or `false` depending on whether the number is a prime number.

```js
function isPrime(num) {
  for(var i = 2; i < num; i++) {
    if(num % i === 0) return false;
  }
  return num > 1;
}
```

If you're using a framework like [jasmine](https://jasmine.github.io/), your tests might look something like this:

```js
describe('isPrime()', () => {

  describe('when given 2', () => {
    it('should return true', () => {
      assert.isTrue(isPrime(2))
    })
  })

  describe('when given 3', () => {
    it('should return true', () => {
      assert.isTrue(isPrime(3))
    })
  })

  describe('when given 4', () => {
    it('should return false', () => {
      assert.isFalse(isPrime(4))
    })
  })

  // and more ...

})
```

It's a lot of code to write for only 3 test cases and such a basic function!

The same tests can be defined with Sazerac as follows:

```js
test(isPrime, () => {
  given(2).expect(true)
  given(3).expect(true)
  given(4).expect(false)
})
```

Sazerac runs the `describe` and `it` functions needed for these test cases. It adds reporting messages in a consistent format based on the input and output parameters. For this example, the test report ends up looking like this:

```
isPrime()
  when given 2
    ✓ should return true
  when given 3
    ✓ should return true
  when given 4
    ✓ should return false
```


Installation
------------

Install Sazerac as an npm module and save it to your `package.json` file as a development dependency:

```js
npm install sazerac --save-dev
```

Import the `test` and `given` helper functions into your project:

```js
import { test, given } from 'sazerac'
```


Basic Use
---------

Sazerac's `test()` and `given()` functions are the basic building blocks for creating data-driven test cases.

`test()` sets the function you want to test.

```js
test(mySumFunction, () => {
  // test cases for mySumFunction defined here...
})
```

`given()` creates a test case by defining the arguments that will be passed to your function.

```js
test(mySumFunction, () => {
  var t = given(1, 2, 3)
})
```

The object returned by `given()` provides functions for defining your test case. `expect()` is the most basic of these. It defines the expected value for you test case. 

```js
test(mySumFunction, () => {
  // when mySumFunction is given the arguments 1, 2, and 3
  var t = given(1, 2, 3)
  // we expect it to return 6
  t.expect(6)
})
```


Custom Assertions
-----------------

Sometimes the expected return value for your test case is too complex to define with `expect()`. To define a custom assertion, `assert()` can be used instead. The `assert()` function expects a message describing the assertion, and a function that defines the assertion.

```js
t.assert(
  'should set the value of the sum property to 6',
  (actualReturnValue) => {
    // do anything with actualReturnValue here,
    // for example:
    assert.equal(actualReturnValue.sum, 6)
  }
)
```

Since you have the actual return value of your test case, any assertion can be performed on the return value. For example, you could add assertions with the library of your choice, such as [chai.js](http://chaijs.com/) or  [should.js](https://shouldjs.github.io/)

You can also add multiple assertions for a single test case

```js
t.assert(
  'should set a sum property',
  (actualReturnValue) => {
    assert.hasProperty(actualReturnValue, 'sum')
  }
)
t.assert(
  'should set the value of the sum property to 6',
  (actualReturnValue) => {
    assert.equal(actualReturnValue.sum, 6)
  }
)
```

Each assertion will be executed as a separate test.


Expect Error Thrown
-------------------

The `expectError()` function can be used to assert that a test case threw a specified error message. The test case will pass if an error is thrown, and the error message matches the string passed to `expectError()` exactly.

```js
function throwAnError(msg) {
  throw new Error(`an error occurred: ${msg}`)
}

test(throwAnError, () => {
  given('not good!')
    .expectError('an error occurred: not good!')
})
```


Reporting Messages
------------------

Sazerac generates messages that are passed to `describe()` and `it()` functions, and ultimately end up in your test report. You can customize these, or stick with the default format.

### Default Format

Messages for each test case are defined based on the given parameters and the expected return value.

The `describe` message for a group of test cases is defined based on the name of the function being tested.

```console
{name_of_tested_function}
  when given {parameters}
    ✓ should return {expected_return_value}
```

Here's an example:

```js
test(mySumFunction, () => {
  given(1, 2, 3).expect(6)
})
```

will output:

```console
mySumFunction()
  when given 1, 2, and 3
    ✓ should return 6
```


### Setting Describe Messages

You can set a message describing the test case using the `describe()` function

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .describe('1 + 2 + 3')
    .expect(6)
})
```

will output:

```console
mySumFunction()
  1 + 2 + 3
    ✓ should return 6
```

### Setting Should Messages

You can set a message describing the test assertion using the `should()` function

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .describe('1 + 2 + 3')
    .should('= 6')
    .expect(6)
})
```

will output:

```console
mySumFunction()
  1 + 2 + 3
    ✓ = 6
```

### Formatting With sprintf.js

Sazerac uses [sprintf.js](https://github.com/alexei/sprintf.js) to provide given and expected values for custom string formatting.

You can use any sprintf.js functionality to format your message strings. For example:

```js
test(addTwoNumbers, () => {
  given(2, 3)
    .expect(5)
    .describe('%s + %s')
    .should('= %s')
})
```

will output:

```console
addTwoNumbers()
  2 + 3
    ✓ = 5
```


Setup and Teardown
------------------

The `before` and `after` functions can be used to run setup and teardown code for a test case, or a collection of test cases. This is equivalent to running `beforeEach` and `afterEach` in Jasmine, Mocha, or Jest.

Mocking function behaviors is a common use case for these:

```js
import sinon from 'sinon'

const myStubFn = sinon.stub()

test(myStubFn, () => {
  given()
    .before(() => myStubFn.returns(42))
    .after(() => myStubFn.resetBehavior())
    .expect(42)

  given().expect(undefined)
})
```


Grouping Multiple Cases
-----------------------

If you have multiple test case inputs with the same expected return value, they can be grouped with the `forCases(cases)` function:

```js
import { test, given, forCases } from 'sazerac'

test(isPrime, () => {
  const primes = [ given(2), given(3), given(5), given(7) ];
  const nonPrimes = [ given(1), given(4), given(6), given(8) ];
  forCases(primes).expect(true)
  forCases(nonPrimes).expect(false);
})
```

`forCases()` will execute a separate test for each case that you pass it. It accepts either an array or a series of arguments, depending on which style you prefer.

```js

test(isPrime, () => {
  // pass it an array of test cases
  const arr = [ given(2), given(3), given(5), given(7) ];
  forCases(arr).expect(true)

  // or each test case as a separate argument
  forCases(
    given(1),
    given(4),
    given(6),
    given(8)
  ).expect(false);
})
```


Chaining
--------

All test case functions can be chained, in any order.

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .expect(6)
    .describe('when called with a set of numbers')
    .should('should return the sum of the numbers')
})
```