# Frontend Data Architecture

This post describes the architecture used throughout [frontend-scaffold](https://github.com/Code-4-Community/frontend-scaffold), Code4Community's shared frontend template used by all client projects. The scaffold is a React application created with Create React App using the TypeScript template. It uses Redux for application state management. 

## What is Redux? 

Redux is a predictable state container for JavaScript apps. When an application uses Redux properly, all state is contained within a singular Redux store, accessed via conventions that ensure application behavior is understandable and repeatable. 

It helps you write applications that behave consistently, run in different environments (client, server, and native), and are easy to test. On top of that, it provides a great developer experience, such as live code editing combined with a time traveling debugger.

Redux accomplishes this by establishing a set of core principles for accessing, fetching, and updating data in your application. A Redux store has one common *state* object, which you can think of as your "model", to use object-oriented terminology. It's really just a big JSON object. The store's data is never accessed or mutated directly; it doesn't have setters like a traditional Java model would. Consider the state of a simple todo list app: 
```js 
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

The only way to make updates to the store is by dispatching an *action*. Actions contain a type and a payload. The type is a constant identifier, and the payload can be any data type - string, number, boolean, JSON, etc. Some example actions in our todo list app could be for adding a todo task, marking one as complete, or setting a filter to show or hide completed tasks: 
```js
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```

Because each change is described as an action, we have a clear history of changes to application state - if something changed, we always know why. 

Actions and state are linked via functions called *reducers*. A reducer takes our state object and an action, and returns an updated state object depending on the action. They aren't super fancy, they're just functions. We typically write multiple reducers for different slices of the state of our application: 
```js
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter
  } else {
    return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([{ text: action.text, completed: false }])
    case 'TOGGLE_TODO':
      return state.map((todo, index) =>
        action.index === index
          ? { text: todo.text, completed: !todo.completed }
          : todo
      )
    default:
      return state
  }
}
``` 
Here, each of our fields defined in state has its own reducer. These functions are joined in our "root" reducer: 
```js
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  }
}
```

This section is largely an adaptation of [https://redux.js.org/introduction/core-concepts/](https://redux.js.org/introduction/core-concepts/)

For more general Redux resources, see [https://redux.js.org/introduction/learning-resources](https://redux.js.org/introduction/learning-resources)

## How do we use Redux? 

In frontend-scaffold, we follow a modular pattern of Redux usage commonly referred to as "Redux ducks". The manifesto for Ducks can be found here, read it: [https://github.com/erikras/ducks-modular-redux](https://github.com/erikras/ducks-modular-redux).

Ducks is a pattern which allows us to easily isolate Redux modules. Think of this isolation step as breaking the above components (state, action, reducers) into separate modules. Those modules are called "ducks" because it's the last syllable of "Redux" or something (it may also be a play on the concept of duck typing, depending on who you ask).

Each module is joined in the `store.js`, which is the high-level place where we configure our Redux store. 

The rules for a duck are that A module...

1. MUST `export default` a function called `reducer()`
2. MUST `export` its action creators as functions
3. MUST have action types in the form `npm-module-or-app/reducer/ACTION_TYPE`
4. MAY export its action types as `UPPER_SNAKE_CASE`, if an external reducer needs to listen for them, or if it is a published reusable library

The benefit of this is a separation of concerns in large applications. Rather than having one reducer, one state definition, one actions file, etc, we can split functionality into entirely isolated modules, and join them in the store. 

## Your first duck - `auth`

In frontend-scaffold, one of our ducks is responsible for managing user authentication. In [`src/auth/ducks`](https://github.com/Code-4-Community/frontend-scaffold/tree/master/src/auth/ducks), you will find everything that you need to allow a user to authenticate with `backend-scaffold`, and the tools to use that data throughout our app. 

To explain this duck, let's walk through what happens when a user signs in to the application. 

First, they enter their email and password, and click submit. Upon that click, our code dispatches a `login` *thunk*. Do not be alarmed by the new buzzword - a thunk is essentially an action which can have asynchronous side effects, for example, reaching out to `backend-scaffold` via an HTTP request to sign in the user. For a formal definition of `redux-thunk` see the source's [README](https://github.com/reduxjs/redux-thunk#why-do-i-need-this) - `redux-thunk` is a middleware for `redux` which extends Redux's abilities - see also [what the heck is a thunk](https://daveceddia.com/what-is-a-thunk/). 

The `login` thunk, and others related to auth, live in [`src/auth/ducks/thunks.ts`](https://github.com/Code-4-Community/frontend-scaffold/tree/master/src/auth/ducks/thunks.ts). A thunk returns a function which makes the async call, then dispatches another action depending on the result. Below is a simplified definition of our login thunk: 

```ts
export const login = (
  loginRequest: LoginRequest,
): UserAuthenticationThunkAction<void> => {
  return (dispatch, { authClient }): Promise<void> => {
    return authClient
      .login(loginRequest)
      .then((response: TokenPayload) => {
        // If the login is successful, we dispatch the authenticateUser loaded action with the response
        dispatch(authenticateUser.loaded(response));
      })
      .catch((error) => {
        // If the login is unsuccessful, we dispatch the authenticateUser failed action with the error 
        dispatch(authenticateUser.failed(error.response.data));
      });
  };
};
```

Throughout our application, we'll use a utility called `AsyncRequest`. This is a simple yet powerful tool for building asynchronous data into our Redux store. It's not super complicated, and it's not even a separate library - it's simply a file in [`src/utils/asyncRequest.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/utils/asyncRequest.ts), and it's only 246 lines. The motivation behind AsyncRequest is that all actions, reducers, and state around asynchronous requests are very similar, with the difference being the resulting data payload or error payload for each.

Our `UserAuthenticationReducerState` has one field, `tokens`, which is an AsyncRequest. It contains the result of a login or signup thunk. Using this utility, we're able to tell if the request is `NotStarted`, `Loading`, `Loaded`, or `Failed`. Based on this property, we can access the payload or error. 
```ts
export interface UserAuthenticationReducerState {
  readonly tokens: AsyncRequest<TokenPayload, ErrorType>;
}
```

In our login thunk, we call `authClient.login()`, which makes an API request to our backend and returns a JavaScript Promise. If the result is successful, we then dispatch an `authenticateUser.loaded(payload)` action. Our AsyncRequest utility gives us a function to generate actions for each possible state of the AsyncRequest (`Loading`, `Loaded`, `Failed`). 
```ts
export const authenticateUser = genericAsyncActions<TokenPayload, any>();
// this gives us: 
authenticateUser.loaded(payload);
authenticateUser.failed(error);
authenticateUser.loading();
```
These generate action objects like `{ type: ASYNC_REQUEST_LOADED, payload: accessToken }`, which are no more complicated than our todo actions (`{ type: 'ADD_TODO', text: 'Go to swimming pool' }`).

Our dispatched action is handled in the reducer, [`src/auth/ducks/reducers.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/auth/ducks/reducers.ts). Here, we again use an `AsyncRequest` utility: `generateAsyncRequestReducer`. This reducer will automatically update the `tokens` field of our state: 
```ts
const userAuthenticationRequestReducer = generateAsyncRequestReducer<
  UserAuthenticationReducerState,
  TokenPayload,
  void
>(authenticateUser.key);
```
This generates a reducer function, and is really no more complicated than the `visibilityFilter` or `todos` functions in the earlier example. In the same way, they are joined together in one common reducer (but in this case, we only have one reducer anyways): 
```ts
const reducers = (
  state: UserAuthenticationReducerState = initialUserState,
  action: C4CAction,
): UserAuthenticationReducerState => {
  switch (action.type) {
    case ASYNC_REQUEST_LOADING_ACTION:
    case ASYNC_REQUEST_LOADED_ACTION:
    case ASYNC_REQUEST_FAILED_ACTION:
      return {
        ...state,
        tokens: userAuthenticationRequestReducer(state.tokens, action),
      };
    default:
      return state;
  }
};
```


At this point, our state is updated. `tokens` contains an `AsyncRequest` of type `Completed`. Because it's completed, we're able to access the resulting payload, in this case a `TokenPayload` containing access and refresh JSON Web Tokens (JWTs). 

We use those tokens to make requests to the backend, but they also contain important information - the user's ID and privilege level. This leads us to our final destination on our journey, [`src/auth/ducks/selectors.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/auth/ducks/selectors.ts). Here, we have a *selector*, `getPrivilegeLevel`, which takes as a parameter our `tokens` AsyncRequest. It returns, depending on the state of the AsyncRequest and the resulting payload, the privilegeLevel of the user. The selector is just a function that takes state and returns some useful information: 
```ts
export const getPrivilegeLevel = (
  tokens: AsyncRequest<TokenPayload, any>,
): PrivilegeLevel => {
  if (tokens.kind === AsyncRequestKinds.Completed) {
    const payload = JSON.parse(atob(tokens.result.accessToken.split('.')[1]));
    return payload.privilegeLevel;
  }
  return PrivilegeLevel.NONE;
};

```

You can access this data in your React component with the `useSelector` hook:
```ts
const privilegeLevel: PrivilegeLevel = useSelector((state: C4CState) => {
    getPrivilegeLevel(state.authenticationState.tokens)
});
```

If any or all of this is confusing, reach out in Slack! It makes a lot more sense one you start writing and see it in action. 



