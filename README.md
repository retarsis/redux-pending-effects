# Redux Pending Effects

🦋 A redux toolkit that tracks your asynchronous [redux](http://redux.js.org) [actions](https://redux.js.org/basics/actions) (effects) and informs about the pending state using the selector function
<br/>

List of supported libraries that process redux effects:

- [redux-toolkit](https://github.com/reduxjs/redux-toolkit)
- [redux-saga](https://github.com/redux-saga/redux-saga)
- [redux-thunk](https://github.com/reduxjs/redux-thunk) / [redux-promise-middleware](https://github.com/pburtchaell/redux-promise-middleware)

It's worth mention that `redux-pending-effects` allows you to code simultaneously with all libraries above.

<br/>

## Problem it solves

Have you ever been in a situation where you need to add a global loader/spinner to any side effect that your application is processing? Perhaps you are using Redux and some third-party library for asynchronous processing, for example, redux-saga / redux-thunk / promise middleware? Great, then it should be interesting to you.

Why not handle the pending state manually for each action?

- It is very unpleasant to create separately for this state and add start and end actions for these actions to each request.
- This is an open place to make mistakes because it's very easy to forget to add or remove these actions.
- It needs to be supported and somehow live with it.

Well, `redux-pending-effects` does this from scratch:

- tracks your asynchronous code
- collects them in a bunch
- efficiently calculates active pending effects
- provides a selector for information about the current state of application loading
- available for debugging in redux-devtools
- independent of a particular asynchronous processing solution. Can be used simultaneously with `redux-saga` and `redux-toolkit`
- replaces `redux-thunk` in the matters of side effects (not actions chaining) and `redux-promise-middleware` (essentially uses it out of the box)

## Quickstart

### Installation

```shell script
npm install redux-pending-effects
```

### Extend reducers

`redux-pending-effects` provides its own state for storing active effects (pending promise phase).

```javascript
import { combineReducers } from 'redux';
import { insertPending } from 'redux-pending-effects';

import { planetReducer as planet } from './planetReducer';
import { universeReducer as universe } from './universeReducer';

export const rootReducer = combineReducers(
  insertPending({
    planet,
    universe
  })
);
```

### Configuration

### `configurePendingEffects(options)`

Creates an object with two properties:  
1) `middlewares` -  array of required redux middlewares.
2) `sagaOptions` - options for `createSagaMiddleware` from saga package.

- #### options: Object - a list of options, all of them are optional:
    - `promise: boolean` - enable/disable tracking of asynchronous effects which you pass as promise to the payload. 
    Yes, if option is enabled, you can pass promise to the payload, that is the way redux-promise-middleware does.
    For details, you can go to read the documentation of [redux-promise-middleware](https://github.com/pburtchaell/redux-promise-middleware) 
    about how this works.
    - `toolkit: boolean` - enable/disable tracking of asynchronous effects produced by [redux-toolkit](https://github.com/reduxjs/redux-toolkit).
    - `saga: boolean` - enable/disable tracking of asynchronous effects produced by [redux-saga](https://github.com/redux-saga/redux-saga).
    - `ignoredActionTypes: string[]` - list of action types, tracking of which will be ignored.
    
### Example

Below we will provide an example with all options enabled.

```javascript
import { configurePendingEffects } from 'redux-pending-effects';
import { configureStore, getDefaultMiddleware } from '@reduxjs/toolkit';
import createSagaMiddleware from '@redux-saga/core';

import { rootReducer as reducer } from './root.reducer';
import { rootSaga } from './root.saga';

const { middlewares, sagaOptions } = configurePendingEffects({
  promise: true,
  toolkit: true,
  saga: true,
  ignoredActionTypes: ['IGNORED_ACTION_1', 'IGNORED_ACTION_2']
});
const sagaMiddleware = createSagaMiddleware(sagaOptions);
const middleware = [
  ...middlewares,
  sagaMiddleware,
  ...getDefaultMiddleware()
];

export const store = configureStore({ reducer, middleware });

sagaMiddleware.run(rootSaga);
```

<br/>

### Selector

Just a regular usage of redux selectors

```javascript
import React from 'react';
import { useSelector } from 'react-redux';
import { selectIsPending } from 'redux-pending-effects';

import { YourApplication } from './YourApplication';
import { AppLoader } from './App.loader';

export const App = () => {
  const isPending = useSelector(selectIsPending);

  return isPending ? <AppLoader /> : <YourApplication />;
};
```

<br/>

### Notes

At the moment, this library completely replaces `redux-promise-middleware`. 
In the plans, through the collaboration, expand the API of `redux-promise-middleware` to reuse their internal API.

Also, right now this package can't track actions, created with `redux-thunk`.

<br/>

### Contributing

Contributions are welcome. For significant changes, please open an issue first to discuss what you would like to change.

If you made a PR, make sure to update tests as appropriate and keep the examples consistent.

<br/>

### Contact

Please reach me out if you have any questions or comments.

- [GitHub](https://github.com/retarsis)
- [Twitter](https://twitter.com/retarsis)

<br/>

### References

I find these packages useful and similar to this one. So, it's important to mention them here.

- [redux-pending](https://www.npmjs.com/package/redux-pending)
- [redux-pender](https://www.npmjs.com/package/redux-pender)
- [redux-promise-middleware](https://www.npmjs.com/package/redux-promise-middleware)

The main reason why I didn't choose them: they do one thing, and it's impossible to add something second to them.

<br/>

### License

This project is [MIT](https://choosealicense.com/licenses/mit/) licensed.

<br/>
