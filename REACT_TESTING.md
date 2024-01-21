# Testing in React

Here's a list of stuff I don't want to forget before I come back to testing again. The thinking is that creating this cheatsheet will be like chiselling the information directly onto my brain.

## Libraries
@testing-library/react

@testing-library/jest-dom

@testing-library/user-event


## Using queries
#### screen.getBy...
For components that render synchronously. Throws error if 0 or more than one element(s) found.

#### screen.getAllBy...
For components that render synchronously. Returns array of elements. Throws if nothing found

#### screen.queryBy...
Synchronous. Returns null if no elements match. Throws if more than one match found

#### screen.queryAllBy...
Synchronous. Returns array if match, empty array if not.

#### screen.findBy...
For components that only render fully after an asynchronous operation completes - e.g. a network request. Returns a promise rejected if 0 or more than 1 elements found after default delay of 1000ms.

#### screen.findAllBy...
Asynchronous. Promise rejected if 0 elements found after default 1000ms delay.

### Docs
https://testing-library.com/docs/queries/about

## Queries
- ByRole
    - Here's a list of [aria roles](https://www.w3.org/TR/html-aria/#docconformance). Accessible name can be included in an options object - e.g. `getByRole('button', {name: 'back button'})`<br> if multiple elements share a role. Other options are [possible](https://testing-library.com/docs/queries/byrole).
- ByLabelText
-  ByPlaceholderText
- ByText
    - Searches for any element that has textContent matching the string parameter
- ByAltText
- ByTitle
- ByTestId
    - Can be used for elements without roles (divs and spans), or for elements whose content changes dynamically. Elements should have the `data-testid` attribute. Not ideal, but better than selecting by class or style attribute. [Here's](https://github.com/coderas/babel-plugin-jsx-remove-data-test-id) a package to strip them from production code, if you think it's necessary.

- [within](https://testing-library.com/docs/dom-testing-library/api-within)
    - `import {within} from "@testing-library/dom`. Select an element nested within a given element.

## Matchers
[Custom jest-dom Matchers](https://github.com/testing-library/jest-dom?tab=readme-ov-file)

[Standard Jest Matchers](https://jestjs.io/docs/expect)

### Note on Links
Any component making use of a \<Link> component (from react-router-dom) needs to be rendered wrapped in a \<MemoryRouter> or \<BrowserRouter> component.

## Mocking functions

Here are the [jest docs](https://jestjs.io/docs/mock-function-api). And here's an example - 
```
beforeEach(() => {
    jest.spyOn(axiosReq, 'get').mockResolvedValue({
        data: {
            "id": 13,
            "created_on": "2023-12-19T19:53:45.768902Z",
        }
    })
})
```

This is considerably easier than setting up [msw](https://mswjs.io/).

## Mocking modules

If a subcomponent of the component being tested is being awkward (making its own network requests or whatever), it can be mocked out.

```
jest.mock(PATH_TO_COMPONENT, () => {
    return () => {
        return 'STRING THAT REPLACES RENDERED SUBCOMPONENT';
    }
});`
```



## Useful functions
screen.debug()

logTestingPlaygroundURL(\<Element>);

test.only('Only run this test', () => test())

test.skip('Skip me', () => test())

const delay = (delay) => new Promise(resolve => setTimeout(resolve, delay));

## Useful articles
[3-ways-to-mock-axios-in-jest](https://vhudyma-blog.eu/3-ways-to-mock-axios-in-jest/)
