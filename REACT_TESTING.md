# Testing in React

Here's a list of stuff I don't want to forget before I come back to testing again.

## Libraries
@testing-library/react
@testing-library/jest-dom
@testing-library/user-event

## Using queries
### screen.getBy...
For components that render synchronously. Throws error if 0 or more than one element(s) found.

### screen.getAllBy...
For components that render synchronously. Returns array of elements. Throws if nothing found

### screen.queryBy...
Synchronous. Returns null if no elements match. Throws if more than one match found

### screen.queryAllBy...
Synchronous. Returns array if match, empty array if not.

### screen.findBy...
For components that only render fully after an asynchronous operation completes - e.g. a network request. Returns a promise rejected if 0 or more than 1 elements found after default delay of 1000ms.

### screen.findAllBy...
Asynchronous. Promise rejected if 0 elements found after default 1000ms delay.


## Useful functions
screen.debug()

logTestingPlaygroundURL(\<Element>);

## Useful articles
[3-ways-to-mock-axios-in-jest](https://vhudyma-blog.eu/3-ways-to-mock-axios-in-jest/)
