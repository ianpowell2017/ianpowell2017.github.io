---
title: Adding Redux to a React app
author: ianpowell
date: 2022-06-10 18:32:00 +0100
categories: [Learning]
tags: [js,ReactJs]
---

## Why do we need Redux in a React app?

In a large application you will commonly have a large amount of state.  The solutions are either to have the state in the most parent component and pass the values down via props or each component has local state.

- Passing as props becomes problematic on pages with many controls.
- Components managing own state causes problem if needed to be modified at a different location in the page.

The solution therefore is to use `Redux` which defines specific **Actions** and **Reducers** to manage access to state.

## Getting started

- In Visual Studio Code, open command line to the root of the React project and run
  `npm install redux react-redux`
- Inside the src folder, create `store.js`

``` javascript
import { createStore, combineReducers } from 'redux';
import { todos } from './todos/reducers'; // Include reducers
const reducers = {
    todos,
};
const rootReducer = combineReducers(reducers);
export const configureStore = () => createStore(rootReducer);
```

Open `index.js` and add

``` javascript
import { Provider } from 'react-redux';
import { configureStore } from './store';
```

Wrap the `<App />`

``` javascript
ReactDOM.render(
    <Provider store={configureStore()} >
        <App />
    </Provider>,
    document.getElementById('root'),
);
```

[See more in the attended learning course examples -> CH03](assets/misc/Ex_Files_Building_Modern_Projects_React.zip)

## To maintain state across browser refreshes use `Redux Persist`

`npm install redux-persist`

Update `store.js`

``` javascript
import { persistReducer } from 'redux-persist';
import storage from 'redux-persist/lib/storage';
import autoMergeLevel2 from 'redux-persist/lib/stateReconciler/autoMergeLevel2';
```

``` javascript
const rootReducer = combineReducers(reducers);

const persistConfig = {
    key: 'root',
    storage, // Defaults to local storage in the browser
    stateReconciler: autoMergeLevel2,
};

const persistedReducer = persistReducer(persistConfig, rootReducer);

export const configureStore = () => createStore(persistedReducer);
```

Update `index.js`

``` javascript
import { persistStore } from 'redux-persist';
import { PersistGate } from 'redux-persist/lib/integration/react';
```

``` javascript
const store = configureStore();
const persistor = persistStore(store);

ReactDOM.render(
    <Provider store={store}>
        <PersistGate
            loading={<div>Loading...</div>}
            persistor={persistor}>
            <App />
        </PersistGate>
    </Provider>,
    document.getElementById('root'),
);
```

### `Redux DevTools`

Add this Chrome extension to make development with Redux easier!

Also update `store.js`

``` javascript
export const configureStore = () => 
    createStore(
        persistedReducer,
        window.__REDUX_DEVTOOLS_EXTENSION__ &&
        window.__REDUX_DEVTOOLS_EXTENSION__(), // This connects our app to the redux extension
    );
```

## Redux Best Practices

- Export the connected and unconnected versions of a component
  - `export const TodoList = ...` So we can test the component as is
  - `export default connect(mapStateToProps, mapDispatchToProps)(TodoList);` For the application
- Keep Redux actions and async operations out of your reducers
- Think carefully about connecting components - 'Connecting a component can, in practice, make it less reusable.'

---

## Why use a side-effect library

Move **data fetching or sending** outside of a component, components should only be responsible to displaying or taking user input, into a side effect library like *Redux Thunk* or *Redux Saga*. *Redux Thunk* is a simpler library to learn.

### Install and configure `Redux Thunk`

[See CH04 in the example bundle](assets/misc/Ex_Files_Building_Modern_Projects_React.zip)

`npm install redux-thunk redux-devtools-extension @babel/runtime`

`npm install --save-dev @babel/plugin-transform-runtime`

open `.babelrc` file

``` javascript
{
    "presets@: ["@babel/preset-env", "@babel/preset-react"],
    "plugins": ["@babel/plugin-transform-runtime"]
}
```

open `store.js`

``` javascript
import { createStore, combineReducers, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import { composeWithDevTools } from 'redux-devtools-extension';
```

Update following section

``` javascript
export const configureStore = () => 
    createStore(
        persistedReducer,
        composeWithDevTools(
            applyMiddleware(thunk)
        )
    );
```

### Add first simple thunk

Create `thunk.js`

``` javascript
export const displayAlert = () = () => {
    alert("Hello!");
}
```

Open a React component and add

``` javascript
import { displayAlert } from './thunks';

const mapStateToProps = state = ({
    todos: state.todos,
});

const mapDispatchToProps = dispatch => ({
    onRemovePressed: text => dispatch(removeTodo(text)),
    onCompletedPressed: text => dispatch(markTodoAsCompleted(text)),
    onDisplayAlertClicked: () => dispatch(displayAlert()),
})
```

### Async thunk

Add to `thunk.js`

``` javascript
import { loadTodosInProgress, loadTodosSuccess, loadTodosFailure} from './actions';


export const loadTodos = () => async (dispatch, getState) => {
    try
    {
        dispatch(loadTodosInProgress());
        const response = await fetch('http://localhost:8080/todos');
        const todos = await response.json();

        dispatch(loadTodosSuccess(todos));
    } catch (e) {
        dispatch(loadTodosFailure());
        dispatch(displayAlert(e));
    }    
}

export const displayAlert = text => () => {
    alert(text);
}
```

---

## Selectors

Selectors give us a place to put logic for combining, filtering, transforming storing data. This way if/when the way the data is stored in state changes, you only have to update the `selectors.js` file

[See CH05 in the example bundle](assets/misc/Ex_Files_Building_Modern_Projects_React.zip)

Create `selectors.js`

``` javascript
export const getToDos = state => state.todos;
export const getToDosLoading = state => state.isLoading;
```

Replace

``` javascript

const mapStateToProps = state = ({
    isLoading: state.isLoading,
    todos: state.todos,
});
```

with

``` javascript
import { getToDos, getToDosLoading } from './selectors';

const mapStateToProps = state = ({
    isLoading: getToDosLoading(state),
    todos: getToDos(state),
});
```

---

## Reselect

Enables you to create more complicated *selectors*, additionally it has the extra benefit not re-computing the selector *IF* the data in state hasn't changed, it gives the previously computed value.

### Install

`npm install reselect`

``` javascript
import { createSelector } from 'reselect';

export const getToDos = state => state.todos.data;
export const getToDosLoading = state => state.todos.isLoading;

export const getIncompleteTodos = createSelector(
    getTodos,
    (todos) => todos.filter(todo => !todo.isCompleted),
)
```

The final parameter in the *createSelector* method gets all the previous values as arguments into the function specified.

``` javascript
import { createSelector } from 'reselect';

export const getToDos = state => state.todos.data;
export const getToDosLoading = state => state.todos.isLoading;

export const getIncompleteTodos = createSelector(
    getTodos,
    getTodosLoading,
    (todos, isLoading) => todos.filter(todo => !todo.isCompleted),
)
```

---

## Styled components

Allow us to define styles inside our JavaScript files

[See CH06 in the example bundle](assets/misc/Ex_Files_Building_Modern_Projects_React.zip)

`npm install styled-components`

``` javascript
import styled from 'styled-components';

const BigRedText = styled.div`
    font-size: 48px;
    color: #ff0000;
`;
```

Can now be used like any other component

``` javascript
<BigRedText>I'm a Styled-Component</BigRedText>
```

In addition to *styled.div* can also use

- styled.h1
- styled.button
- styled.(and any other html element name here)

This now means that we can pass props into *BigRedText* eg.

``` javascript
<BigRedText createdDays={todo.numberOfDays}></BigRedText>
```

Now the style changes based on the *numberOfDays*

``` javascript
const BigRedText = styled.div`
    font-size: ${props => (props.createdDays > 4
    ? '48px' : '96px')};
    color: #ff0000;
`;
```

You can also **Inherit** styles

``` javascript
const BigRedTextWithWarning = styled(BigRedText)`
    color: #ff0000;
    font-size: x-large;
`;
```

---

## Testing

[See CH07 in the example bundle](assets/misc/Ex_Files_Building_Modern_Projects_React.zip)

### Setup

`npm install --save-dev mocha chai`

`npm install --save-dev @babel/register`

`npm install --save-dev sinon node-fetch fetch-mock`

Modify `package.json`

``` javascript
"test": "mocha \"src/**/*.test.js\" --require @babel/register --recursive"
```

`npm run test` to run test suite.

### Testing Reducers

Create `tests` folder, Create `reducers.test.js`

``` javascript
import { expect } from 'chai';
import { todos } from '../reducers';

describe('The todos reducer', () => {

    it('Adds a new todo where CREATE_TODO action is received', () => {
        const fakeTodo = { text: 'hello', isCompleted: false };
        const fakeAction = {
            type: 'CREATE_TODO',
            payload: {
                todo: fakeTodo,
            },
        };

        const originalState = { isLoading: false, data: [] };

        const expected = {
            isLoading: false,
            data: [fakeTodo],
        };

        const actual = todos(originalState, fakeAction);

        expect(actual).to.deep.equal(expected);
    });
});
```

### Testing Redux Thunks

In `tests` folder, Create `thunks.test.js`

``` javascript
import 'node-fetch';
import 'fetchMock' from 'fetch-mock';
import { expect } from 'chai';
import sinon from 'sinon';
import { loadTodos } from '../thunks';

describe('The loadTodos thunk', () => {

    it('Dispatches the correct actions in the success scenario', async () => {
        const fakeDispatch = sinon.spy();

        const fakeTodos = [{ text: '1'}, { text: '2' }];
        fetchMock.get('http://localhost:8080/todos', fakeTodos);

        const expectedFirstAction = { type: 'LOAD_TODOS_IN_PROGRESS' };
        const expectedSecondAction = { 
            type: 'LOAD_TODOS_SUCCESS', 
            payload: {
                todos: fakeTodos,
            }
        };

        await loadTodos()(fakeDispatch);

        expect(fakeDispatch.getCall(0).args[0]).to.deep.equal(expectedFirstAction);
        expect(fakeDispatch.getCall(1).args[0]).to.deep.equal(expectedSecondAction);

        fetchMock.reset(); // Restore to original state
    });

});
```

### Testing Selectors

In `tests` folder, Create `selectors.test.js`

``` javascript
import { expect } from 'chai';
import { getCompletedTodos } from '../selectors';

describe('The getCompletedTodos selector', () => {

    it('Returns only completed todos', () => {
        const fakeTodos = [{
            text: 'Say hello',
            isCompleted: true
        }, {
            text: 'Say Goodbye',
            isCompleted: false
        }, {
            text: 'Climb Mount Everest',
            isCompleted: false
        }];

        const expected = [{
            text: 'Say hello',
            isCompleted: true
        }];
        
        /// Note the .resultFunc to get the last function which you're testing
        const actual = getCompletedTodos.resultFunc(fakeTodos);

        expect(actual).to.deep.equal(expected);
    });

});
```

### Testing Styled Components

We won't be testing the style output, what we can test is the function which determines the style output.

Just extract into an export function and test that.

``` javascript
const BigRedText = styled.div`
    font-size: ${props => (props.createdDays > 4
    ? '48px' : '96px')};
    color: #ff0000;
`;
```

Convert to 

``` javascript

export const getStyleForCreated = (days) => (days > 4 ? '48px' : '96px');

const BigRedText = styled.div`
    font-size: ${props => getStyleForCreated(props.createdDays)};
    color: #ff0000;
`;
```

In `tests` folder, Create `COMPONENT-NAME.test.js`

``` javascript
import { expect } from 'chai';
import { getStyleForCreated } from '../COMPONENT-NAME';

describe('getStyleForCreated', () => {

    it('Returns 48px when days > 4', () => {
        const actual = getStyleForCreated(10);
        expect(actual).to.equal('48px');
    });

    it('Returns 96px when days <= 4', () => {
        const actual = getStyleForCreated(3);
        expect(actual).to.equal('96px');
    });

});
```
