# NOTES: Testing in React for beginners with Jest

These notes were taken by following along [this](https://leveluptutorials.com/tutorials/react-testing-for-beginners/react-testing-for-beginners) tutorial from LevelUpTutorial.

[Jest - Official Documentation](https://jestjs.io/docs/getting-started)

## Table of contents

* [Introduction to testing with Jest](https://github.com/benjaminpeto/NOTES-react-testing#introduction-to-testing-with-jest)
* [Jest explained](https://github.com/benjaminpeto/NOTES-react-testing#jest-explained)
* [Writing Unit Tests With Jest](https://github.com/benjaminpeto/NOTES-react-testing#writing-unit-tests-with-jest)
* [Writing integration tests](https://github.com/benjaminpeto/NOTES-react-testing#writing-integration-tests)
* [Mock functions and why](https://github.com/benjaminpeto/NOTES-react-testing#mock-functions-and-why)
* [Mocking modules](https://github.com/benjaminpeto/NOTES-react-testing#mocking-modules)
* [Introduction to react testing](https://github.com/benjaminpeto/NOTES-react-testing#introduction-to-react-testing)
* [React testing library and debug](https://github.com/benjaminpeto/NOTES-react-testing#react-testing-library-and-debug)
* [Testing with test IDs](https://github.com/benjaminpeto/NOTES-react-testing#testing-with-test-ids)
* [Events in react testing library](https://github.com/benjaminpeto/NOTES-react-testing#events-in-react-testing-library)
* [Integration testing in react and cleanup](https://github.com/benjaminpeto/NOTES-react-testing#integration-testing-in-react-and-cleanup)
* [Snapshot testing 101](https://github.com/benjaminpeto/NOTES-react-testing#snapshot-testing-101)
* [Spying and mocking functions in react](https://github.com/benjaminpeto/NOTES-react-testing#spying-and-mocking-functions-in-react)
* [Form events with controlled inputs](https://github.com/benjaminpeto/NOTES-react-testing#form-events-with-controlled-inputs)
* [Testing for errors and global mocks](https://github.com/benjaminpeto/NOTES-react-testing#testing-for-errors-and-global-mocks)
* [Negative assertions and testing with react router](https://github.com/benjaminpeto/NOTES-react-testing#negative-assertions-and-testing-with-react-router)
* [What to test](https://github.com/benjaminpeto/NOTES-react-testing#what-to-test)
* [Mocking fetch](https://github.com/benjaminpeto/NOTES-react-testing#mocking-fetch)
* [Mocking fetch async tests and working with data](https://github.com/benjaminpeto/NOTES-react-testing#mocking-fetch-async-tests-and-working-with-data)
* [Testing loading states and more pitfalls](https://github.com/benjaminpeto/NOTES-react-testing#testing-loading-states-and-more-pitfalls)
* [Refactoring with tests](https://github.com/benjaminpeto/NOTES-react-testing#refactoring-with-tests)
* [Code coverage](https://github.com/benjaminpeto/NOTES-react-testing#code-coverage)
* [Where to go from here](https://github.com/benjaminpeto/NOTES-react-testing#where-to-go-from-here)




## Introduction to testing with [Jest](https://https://jestjs.io/)

Boilerplate code for a basic JS test.

> App.test.js
```javascript
test('name of the test', () => {
  expect(true).toBeTruthy();
});
```
  
  *An assertion is a boolean expression. It is used to test a logical expression. An assertion is true if the logical expression that is being tested is true.*



  ## Jest explained

Jest is a test runner library for JavaScript. It works with multiple libraries and frameworks such as Babel, TypeScript, Node, React, Angular, Vue, etc.

* Zero config on most JS projects
* Tests are isolated to maximize performance
* Has great documentation and has the entire toolkit in one place
* Runs previously failed tests first
* Jest can collect code coverage information from entire projects, including untested files
* Great CLI

[Jest - Methods/Assertions](https://jestjs.io/docs/api)



  ## Writing Unit Tests With Jest

Let's see an example for a function which sum up two numbers.

> App.js
```javascript
export const add = (x, y) => {
  return x + y;
};
```

> App.test.js
```javascript
import { add } from './App';

test('add', () => {
  const value = add(1, 2);
  expect(value).toBe(3);
});

// Output: Test 'add' passed
  :white_check_mark:
```

*Unit testing ensures that all code meets quality standards before it's deployed. This ensures a reliable engineering environment where quality is paramount.*




  ## Writing integration tests

  > App.js
```javascript
export const add = (x, y) => {
  return x + y;
};

export const total = (shipping, subTotal) => {
  return "€" + add(shipping + subTotal);
};
```

> App.test.js
```javascript
import { add, total } from './App';

test('add', () => {
  const value = add(1, 2);
  expect(value).toBe(3);
});

test('total', () => {
  expect(total(5, 20)).toBe("€25");
});

// Output: Test 'add' passed
  :white_check_mark:
// Output: Test 'total' passed
  :white_check_mark:
```

This is an integration test, because we're not only testing the *total function*, but also testing the *add function*, as **total relies on the add function**.

Writing a Unit Test for a function which relies on another function, we need to levarage from Mock functions and create fake functions. [Read more](https://github.com/benjaminpeto/NOTES-react-testing#mock-functions-and-why)

  ## Mock functions and why

Without importing a function to our test, we can write a "Fake"/"Mock" function, which we can test, and allows us to make Unit test on a function which may depend on another function.

This can be useful for example when we don't want to connect for a database, we can mock that up as well.

> App.test.js
```javascript
const add = jest.fn((x, y) => 3);

test('add', () => {
  expect(add(1, 2)).toBe(3);
  expect(add).toHaveBeenCalledTimes(1);
  expect(add).toHaveBeenCalledWith(1, 2);
});

// Output: Test 'add' passed
  :white_check_mark:
```

  ## Mocking modules

> add.js
```javascript
export const add = (x, y) => x + y;
```

> add.test.js - Unit test, tests only one thing
```javascript
import { add } from './add';

test('add', () => {
  expect(add(1, 2)).toBe(3);
  expect(add(5, 2)).toBe(7);
});
```

> App.js
```javascript
import { add } from './add';

export const total = (shipping, subTotal) => {
  return "€" + add(shipping + subTotal);
};
```

> App.test.js - Integration test, tests things working together
```javascript
import { total } from './App';
import { add } from './add';

jest.mock('./add', () => ({
  add: jest.fn(() => 50)
}));

test('add', () => {
  expect(add(10, 40)).toBe('€50');
  expect(add).toHaveBeenCalledTimes(1);

  // Redundant
  add.mockImplementation(() => 25);
  expect(add(5, 20)).toBe('€25');
  expect(add).toHaveBeenCalledTimes(2);
});
```

We imported the depencency, then we used ```jest.mock``` to mock the location of the dependency and we used an arrow function to return an object, which has the fake function *of add* inside.

*It's a perfect use case if you want to test your code with third-party API, in that case you don't want to test the actual API, so you can mock that, and just test your own code.*


  ## Introduction to react testing

  ## React testing library and debug

  ## Testing with test IDs

  ## Events in react testing library

  ## Integration testing in react and cleanup

  ## Snapshot testing 101

  ## Spying and mocking functions in react

  ## Form events with controlled inputs

  ## Testing for errors and global mocks

  ## Negative assertions and testing with react router

  ## What to test

  ## Mocking fetch

  ## Mocking fetch async tests and working with data

  ## Testing loading states and more pitfalls

  ## Refactoring with tests

  ## Code coverage

  ## Where to go from here
