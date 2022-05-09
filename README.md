# NOTES: Testing in React for beginners with Jest

These notes were taken by following along [React testing for beginners](https://leveluptutorials.com/tutorials/react-testing-for-beginners/react-testing-for-beginners) tutorial from LevelUpTutorials by Scott Tolinski.

[Jest - Official Documentation](https://jestjs.io/docs/getting-started)

## Table of contents

* [Introduction to testing with Jest](https://github.com/benjaminpeto/NOTES-react-testing#introduction-to-testing-with-jest)
* [Jest explained](https://github.com/benjaminpeto/NOTES-react-testing#jest-explained)
* [Writing Unit Tests With Jest](https://github.com/benjaminpeto/NOTES-react-testing#writing-unit-tests-with-jest)
* [Writing integration tests](https://github.com/benjaminpeto/NOTES-react-testing#writing-integration-tests)
* [Mock functions and why](https://github.com/benjaminpeto/NOTES-react-testing#mock-functions-and-why)
* [Mocking modules](https://github.com/benjaminpeto/NOTES-react-testing#mocking-modules)
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

Unit testing means testing individual modules of an application in isolation (without any interaction with dependencies) to confirm that the code is doing things right.

*Let's see an example for a function which sum up two numbers.*

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



  ## React testing library and debug

  Example for testing a React Class Component

> Counter.js
```javascript
import React, { Component } from 'react';

export default class extends Component {
  state = {
    count: 0,
  }

  render() {
    const { count } = this.state;
    return (
      <div>
        <button>
          {count}
        </button>
      </div>
    );
  }
}
```

> Counter.test.js
```javascript
import React from 'react';
import { render } from 'react-testing-library';
import Counter from './Counter';

test('<Counter />', () => {
  // Renders component
  const wrapper = render(<Counter />);
  // Debug function gives insight what it's actually looking at in the rendering and outputs the DOM as string
  wrapper.debug(); 
  // basic assertion checking if the tagName of button in fact is button
  expect(wrapper.getByText('0').tagName).toBe('BUTTON');
});
```
// Output: Test ``` '<Counter />' ``` passed



  ## Testing with test IDs

Using **getByTestId** to identify button, and destructured the component from the wrapper variable.

> Counter.test.js
```javascript
import React from 'react';
import { render } from 'react-testing-library';
import Counter from './Counter';

test('<Counter />', () => {
  const { debug, getByTestId } = render(<Counter />);

  debug();
  // Asserts counter-button is a button
  expect(getByTestId('counter-button').tagName).toBe('BUTTON');
  // Asserts counter-button starts at 0
  expect(getByTestId('counter-button').textContent).toBe('0');
});
```



  ## Events in react testing library

> Counter.js
```javascript
import React, { Component } from 'react';

export default class extends Component {
  state = {
    count: 0,
  };

  count = () => {
    this.setState(prevState => ({
      count: prevState.count + 1,
    }));
  }

  render() {
    const { count } = this.state;
    return (
      <div>
        <button
          type="submit"
          data-testid="counter-button"
          onClick={this.count}
        >
          {count}
        </button>
      </div>
    );
  }
}
```

> Counter.test.js
```javascript
import React from 'react';
import { render, cleanup, fireEvent } from 'react-testing-library';
import Counter from './Counter';

afterEach(cleanup); // cleanup will unmount everything from the DOM

test('<Counter />', () => {
  const { getByTestId } = render(<Counter />);

  const counterButton = getByTestId('counter-button');

  // Asserts counter-button is a button
  expect(counterButton.tagName).toBe('BUTTON');
  // Asserts counter-button starts at 0
  expect(counterButton.textContent).toBe('0');

  fireEvent.click(counterButton); // fire a click event on the button
  expect(counterButton.textContent).toBe('1'); // expects the content of button outputs '1'

  fireEvent.click(counterButton);
  expect(counterButton.textContent).toBe('2');
});
```
// Output: Test ``` '<Counter />' ``` passed

We are testing what the user sees in the DOM, we are **NOT** testing React's state itself. This is the best practices what Jest enforces.



  ## Integration testing in react and cleanup

Integration testing means checking if different modules are working fine when combined together as a group.

> MovieForm.js
```javascript
import React, { Component } from 'react';

export default class extends Component {
  render() {
    return (
      <div>
        <form data-testid="movie-form">
          <input type="text" />
          <button>Submit</button>
        </form>
      </div>
    );
  }
}
```

> NewMovie.js
```javascript
import React, { Component } from 'react';
import MovieForm from './MovieForm';

export default class NewMovie extends Component {
  render() {
    return (
      <div>
        <h1 data-testid="page-title">New Movie</h1>
        {/*integration of MovieForm*/}
        <MovieForm />
      </div>
    );
  }
}
```

> NewMovie.test.js
```javascript
import React from 'react';
import { render, cleanup } from 'react-testing-library';
import NewMovie from './NewMovie';

afterEach(cleanup);

test('<NewMovie />', () => {
  const { debug, getByTestId, queryByTestId } = render(<NewMovie />);

  // check if the page-title is present
  expect(getByTestId('page-title').textContent).toBe('New Movie');
  // check if movie-form is being rendered
  expect(queryByTestId('movie-form')).toBeTruthy();

  debug();
});
```


  ## Snapshot testing 101

Snapshot tests are a very useful tool whenever you want to make sure the UI does not change unexpectedly.

A typical snapshot test case renders a UI component, takes a snapshot, then compares it to a reference snapshot file stored alongside the test. The test will fail if the two snapshots do not match: either the change is unexpected, or the reference snapshot needs to be updated to the new version of the UI component.

*Snapshot tests are most useful when as much of the retrieved DOM structure is used in the test case. Testing React components would be verifying the DOM structure for different purposes and preventing regression in the DOM structure between versions. Snapshot testing shouldn’t be used as a default way of testing and is only recommended in certain specific use cases.*

> NewMovie.test.js
```javascript
import React from 'react';
import { render, cleanup } from 'react-testing-library';
import NewMovie from './NewMovie';

afterEach(cleanup);

test('<NewMovie />', () => {
  const {
    getByTestId, queryByTestId, container,
  } = render(<NewMovie />);

  expect(getByTestId('page-title').textContent).toBe('New Movie');
  expect(queryByTestId('movie-form')).toBeTruthy();
  expect(container.firstChild).toMatchSnapshot();
});
```



  ## Spying and mocking functions in react

We will make a spy/mock function which will allow us, to mock the functionality of our component. 

*const onSubmit* will be the spy function, which we'll render with the MovieForm component and pass down our spy function as the *onSubmit* prop.

> MovieForm.js
```javascript
import React, { Component } from 'react';

export default class extends Component {
  state = {
    text: '',
  };

  render() {
    const { submitForm } = this.props;
    const { text } = this.state;

    return (
      <div>
        <form
          data-testid="movie-form"
          onSubmit={() => submitForm({
            text,
          })
          }
        >
          <input type="text" />
          <button>Submit</button>
        </form>
      </div>
    );
  }
}
```

> MovieForm.test.js
```javascript
import React from 'react';
import { render, cleanup, fireEvent } from 'react-testing-library';
import MovieForm from './MovieForm';

afterEach(cleanup);

const onSubmit = jest.fn(); // mock/spy function


test('<MovieForm />', () => {
  const {
    queryByTestId, getByText,
  } = render(
    <MovieForm submitForm={onSubmit} />, // spy function gets passed down as a prop of onSubmit
  );
  expect(queryByTestId('movie-form')).toBeTruthy();
  fireEvent.click(getByText('Submit'));

  expect(onSubmit).toHaveBeenCalledTimes(1);
});
```



  ## Form events with controlled inputs

First we create a mock function, which will spy on how many times have been clicked and with what?

We don't need to test if the state has been set correctly, **we need to test that the function have been submitted with the correct data.**

> MovieForm.js
```javascript
import React, { Component } from 'react';

export default class extends Component {
  state = {
    text: '',
  };

  render() {
    const { submitForm } = this.props;
    const { text } = this.state;

    return (
      <div>
        <form
          data-testid="movie-form"
          onSubmit={() => submitForm({
            text,
          })
          }
        >
          <label htmlFor="text">
            Text
            <input
              type="text"
              id="text"
              onChange={e => this.setState({ text: e.target.value })}
            />
          </label>
          <button>Submit</button>
        </form>
      </div>
    );
  }
}
```

> MovieForm.test.js
```javascript
import React from 'react';
import { render, cleanup, fireEvent } from 'react-testing-library';
import MovieForm from './MovieForm';

afterEach(cleanup);

const onSubmit = jest.fn(); // mock/spy function


test('<MovieForm />', () => {
  const {
    queryByTestId, getByText, getByLabelText,
  } = render(
    <MovieForm submitForm={onSubmit} />,
  );
  expect(queryByTestId('movie-form')).toBeTruthy();

  // NOTE syntax below might not work,
  // however it's more concise and easier to write, it worked for me tho
  // getByLabelText('Text').value = 'hi';
  // fireEvent.change(getByLabelText('Text'));

  fireEvent.change(getByLabelText('Text'), {
    target: { value: 'hi' },
  });

  fireEvent.click(getByText('Submit'));
  expect(onSubmit).toHaveBeenCalledTimes(1);
  expect(onSubmit).toHaveBeenCalledWith({
    text: 'hi',
  });
});
```



  ## Testing for errors and global mocks

Rendering the ``` <Movie /> ``` component without props, we can test for errors. If we want to spy on errors, we can also mock them.

Mocking the ``` console.error ``` and seeing if it does get fired, it's definitely a technique to use for actually count on that thing.

> Movie.js
```javascript
import React from 'react';
import PropTypes from 'prop-types';
import { Link } from 'react-router-dom';
import styled from 'styled-components';
import Overdrive from 'react-overdrive';

const POSTER_PATH = 'http://image.tmdb.org/t/p/w154';

const Movie = ({ movie }) => {
  if (!movie) return null;
  return (
    <Link to={`/${movie.id}`}>
      <Overdrive id={`${movie.id}`}>
        <Poster src={`${POSTER_PATH}${movie.poster_path}`} alt={movie.title} />
      </Overdrive>
    </Link>
  );
};

export default Movie;

Movie.propTypes = {
  movie: PropTypes.shape({
    title: PropTypes.string.isRequired,
    poster_path: PropTypes.string.isRequired,
    id: PropTypes.string.isRequired,
  }).isRequired,
};
```

> Movie.test.js
```javascript
import React from 'react';
import { render, cleanup } from 'react-testing-library';
import Movie from './Movie';

afterEach(cleanup);

console.error = jest.fn();

test('<Movie />', () => {
  render(<Movie />);
  expect(console.error).toBeCalled();
});
```



  ## Negative assertions and testing with react router

First we will mock our ``` console.error```, then we make sure we call it. Then we define some fake data and pass that data into a component where we have a fake router wrapping around it.

> Movie.test.js
```javascript
import React from 'react';
import { render, cleanup } from 'react-testing-library';
import { MemoryRouter } from 'react-router-dom';
import Movie from './Movie';

afterEach(() => {
  cleanup();
  console.error.mockClear(); // clean up the error
});

console.error = jest.fn();

test('<Movie />', () => {
  render(<Movie />);
  expect(console.error).toHaveBeenCalled();
});

// mocking data, instead of calling the API
const movie = {
  id: 'hi',
  title: 'Terminator',
  poster_path: 'arnold.jpg',
};

// testing react router with <MemoryRouter />
test('<Movie /> with movie', () => {
  render(
    <MemoryRouter>
      <Movie movie={movie} />
    </MemoryRouter>,
  );
  expect(console.error).not.toHaveBeenCalled();
});
```



  ## What to test

We are testing if the link and the image is where it supposed to be, and what is supposed to be on our ``` <Movie /> ``` component.

> Movie.js
```javascript
import React from 'react';
import PropTypes from 'prop-types';
import { Link } from 'react-router-dom';
import styled from 'styled-components';
import Overdrive from 'react-overdrive';

export const POSTER_PATH = 'http://image.tmdb.org/t/p/w154';

const Movie = ({ movie }) => {
  if (!movie) return null;
  return (
    <Link to={`/${movie.id}`} data-testid="movie-link">
      <Overdrive id={`${movie.id}`}>
        <Poster
          data-testid="movie-img"
          src={`${POSTER_PATH}${movie.poster_path}`}
          alt={movie.title}
        />
      </Overdrive>
    </Link>
  );
};

export default Movie;

Movie.propTypes = {
  movie: PropTypes.shape({
    title: PropTypes.string.isRequired,
    poster_path: PropTypes.string.isRequired,
    id: PropTypes.string.isRequired,
  }).isRequired,
};
```

> Movie.test.js
```javascript
import React from 'react';
import { render, cleanup } from 'react-testing-library';
import { MemoryRouter } from 'react-router-dom';
import Movie, { POSTER_PATH } from './Movie';

afterEach(() => {
  cleanup();
  console.error.mockClear(); // clean up the error
});

console.error = jest.fn();

test('<Movie />', () => {
  render(<Movie />);
  expect(console.error).toHaveBeenCalled();
});

// mocking data, instead of calling the API
const movie = {
  id: 'hi',
  title: 'Terminator',
  poster_path: 'arnold.jpg',
};

// testing react router with <MemoryRouter />
test('<Movie /> with movie', () => {
  const { getByTestId } = render(
    <MemoryRouter>
      <Movie movie={movie} />
    </MemoryRouter>,
  );
  expect(console.error).not.toHaveBeenCalled();
  // getting relative href, not the absolute path of http
  expect(getByTestId('movie-link').getAttribute('href')).toBe(`/${movie.id}`);
  expect(getByTestId('movie-img').src).toBe(`${POSTER_PATH}${movie.poster_path}`);
});
```



  ## Mocking fetch

  ## Mocking fetch async tests and working with data

  ## Testing loading states and more pitfalls

  ## Refactoring with tests

  ## Code coverage

  ## Where to go from here
