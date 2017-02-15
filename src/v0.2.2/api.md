---
title: API
type: v0.2.2
order: 2
---

`test()` sets the function you want to test. `given()` creates a test case by defining the arguments that will be passed to your function.

```js
test(mySumFunction, () => {
  var t = given(1, 2, 3)
})
```

The test case returned by `given` provides functions for defining your test case.

### .expect(returnValue)

Sets the expected return value for a test case. Uses `assert.deepEqual` from the [chai](http://chaijs.com/) assertion library.

```js
t.expect(6)
```

### .describe(message)

Sets a message for the reporter to describe the test case. This message will be passed to the `describe` function when the tests run.

```js
t.describe('when called with a set of numbers')
```

### .should(message)

Sets a message for the reporter to describe the test expectation. This message will be passed to the `it` function when the tests run.

```js
t.should('should return the sum of the numbers')
```

### .assert(message, assertFunction)

Adds an assertion for the test case. `message` describes this assertion, and will be passed to the `it` function when tests run. `assertFunction` is called with the actual return value of the executed test function.

```js
t.assert('should set the value of the sum property to 6', (actualReturnValue) => {
  // use any assertion library here
  assert.equal(actualReturnValue.sum, 6)
})
```

Multiple assertions can be added to a single test case

```js
t.assert('should set a sum property', (actualReturnValue) => {
  assert.hasProperty(actualReturnValue, 'sum')
}).assert('should set the value of the sum property to 6', (actualReturnValue) => {
  assert.equal(actualReturnValue.sum, 6)
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
});
```

`forCases` returns an object with the same functions that can be applied to an individual test case: `expect`, `describe`, `should`, and `assert`


Message Formatting
------------------

### Default Format

Sazerac formats `describe` and `it` messages based on given parameters and the expected return value:

```js

test(myFunction, () => {
  given(1, 2, 3).expect(true)
})

/*

report output:

myFunction()
  when given 1, 2, and 3
    ✓ should return true

*/

```

### Custom Format

For custom formatted messages, sazerac uses [https://github.com/alexei/sprintf.js](https://github.com/alexei/sprintf.js).

Custom formatting can be applied to `.describe` messages using given parameters:

```js

test(addTwoNumbers, () => {
  given(2, 3).expect(5).describe('%s plus %s')
})

/*

report output:

addTwoNumbers()
  2 plus 3
    ✓ should return 5

*/

```

Same for `.should` messages, but expected value is used:

```js

test(addTwoNumbers, () => {
  given(2, 3).expect(5).describe('%s plus %s').should('equals %s')
})

/*

report output:

addTwoNumbers()
  2 plus 3
    ✓ equals 5

*/

```
