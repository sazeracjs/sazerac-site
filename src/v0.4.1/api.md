---
title: API
type: v0.4.1
order: 2
---

Helpers
-------

The `sazerac` module exports 3 helper functions:

```js
import { test, given, forCases } from 'sazerac'
```

### test(testFn, definerFn)

Defines test cases for a function

- Arguments:
  - `{Function} testFn` - a function to test
  - `{Function} definerFn` - a function that defines test cases for `testFn`

- Example:

```js
test(mySumFunction, () => {
  // test cases for mySumFunction defined here...
})
```

### given([...args])

Defines the functional arguments for a test case

- Arguments:
  - `{...object} args` - arguments that will be passed to the function being tested

- Returns:
  - `{TestCase}`

- Example:

```js
test(mySumFunction, () => {
  var testCase = given(1, 2, 3)
})
```

### forCases(...testCases)

Groups multiple test case objects into a collection

- Arguments:
  - `{...TestCase | Array<TestCase>} testCases`

- Returns:
  - `{TestCaseCollection}`

- Example:

```js
test(isPrime, () => {
  var primeTestCases = [2, 3, 5, 7].map(given)
  var testCaseCollection = forCases(primeTestCases)
})
```


TestCase | TestCaseCollection
-----------------------------

The `TestCase` and `TestCaseCollection` objects have an identical API:

### expect(value, [message])

Defines the expected return value for the test case. Uses [chai](http://chaijs.com) `assert.deepEqual()` to assert that the expected return value equals the actual return value.

- Arguments:
  - `{object} value` - expected return value
  - `{string} message` - describes the test case expectation

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
test(mySumFunction, () => {
  given(1, 2, 3).expect(6)
  given(4, 5).expect(9, 'should equal 9')
})
```

### expectError(errorMessage, [message])

Defines an expected error to be thrown when this test case is executed.

- Arguments:
  - `{object} errorMessage` - expected message from the error thrown
  - `{string} message` - describes the test case expectation
  
- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
function throwAnError(msg) {
  throw new Error(`an error occurred: ${msg}`)
}

test(throwAnError, () => {
  given('not good!')
    .expectError('an error occurred: not good!')
})
```

### describe(message)

Defines a describe message for the test case.

- Arguments:
  - `{string} message`

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .expect(6)
    .describe('sum of 1, 2, and 3')
})
```

### should(message)

Defines a message to describe the test case assertion. This message is passed to the `it` function when the test is executed.

- Arguments:
  - `{string} message`

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .expect(6)
    .should('should equal 6')
})
```

### assert(message, assertFn)

Defines a custom assertion for the test case

- Arguments:
  - `{string} message` - describes the assertion
  - `{Function} assertFn` - function for defining the assertion. Receives the actual return value of the function being tested as its only argument.

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
test(mySumFunction, () => {
  given(1, 2, 3)
    .assert(
      'should set the value of the sum property to 6',
      (actualReturnValue) => {
        // do anything with actualReturnValue here,
        // for example:
        assert.equal(actualReturnValue.sum, 6)
      }
    )
})
```

### before(beforeFn)

Adds a function to run before a test case is executed

- Arguments:
  - `{Function} beforeFn` - function to run before the test case

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

```js
import sinon from 'sinon'

const myStubFn = sinon.stub()

test(myStubFn, () => {
  given()
    .before(() => myStubFn.returns(42))
    .expect(42)
})
```

### after(afterFn)

Adds a function to run after a test case is executed

- Arguments:
  - `{Function} afterFn` - function to run after the test case

- Returns:
  - `{TestCase | TestCaseCollection}`

- Example:

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
