# Frontend Data Architecture

This post describes the architecture used throughout [frontend-scaffold](https://github.com/Code-4-Community/frontend-scaffold), Code4Community's shared frontend template used by all client projects. The scaffold is a React application created with Create React App using the TypeScript template. It uses Redux for application state management. 

## What is Redux? 

Redux is a predictable state container for JavaScript apps.

It helps you write applications that behave consistently, run in different environments (client, server, and native), and are easy to test. On top of that, it provides a great developer experience, such as live code editing combined with a time traveling debugger.

Redux accomplishes this by establishing a set of core principles for accessing, fetching, and updating data in your application. A Redux store has one common *state* object, which you can think of as your "model", to use object-oriented terminology. This model has no "setters", to prevent different parts of the code from updating the data in different ways. 

Instead, the only way to make updates to the store is by dispatching an *action*. Actions contain a type and a payload. The type is a constant identifier, and the payload can be any data type - string, number, boolean, JSON, etc.

Actions and state are linked via functions called *reducers*. A reducer takes a state object and an action object, and returns an updated state object depending on the action. Typically, the reducer will perform a switch statement on different action types. 

For more information, please see [https://redux.js.org/introduction/core-concepts/](https://redux.js.org/introduction/core-concepts/)

## How do we use Redux? 

In frontend-scaffold, we follow a modular pattern of Redux usage commonly referred to as "Redux ducks". The manifesto for Ducks can be found here: [https://github.com/erikras/ducks-modular-redux](https://github.com/erikras/ducks-modular-redux).

Ducks is a pattern which allows us to easily isolate Redux modules. Think of this isolation step as breaking the above components (state, action, reducers) into separate modules. Those modules are called "ducks" because it's the last syllable of "Redux" or something (it may also be a play on the concept of duck typing, depending on who you ask).

Each module is joined in the `store.js`, which is the high-level place where we configure our Redux store. 

The rules for a duck are that A module...

1. MUST `export default` a function called `reducer()`
2. MUST `export` its action creators as functions
3. MUST have action types in the form `npm-module-or-app/reducer/ACTION_TYPE`
4. MAY export its action types as `UPPER_SNAKE_CASE`, if an external reducer needs to listen for them, or if it is a published reusable library


## Your first duck - `auth`

In frontend-scaffold, one of our ducks is responsible for managing user authentication. In [`src/auth/ducks`](https://github.com/Code-4-Community/frontend-scaffold/tree/master/src/auth/ducks), you will find everything that you need to allow a user to authenticate with `backend-scaffold`, and the tools to use that data throughout our app. 

To explain this duck, let's walk through what happens when a user signs in to the application. 

First, they enter their email and password, and click submit. Upon that click, our code dispatches a `login` *thunk*. Do not be alarmed by the new buzzword - a thunk is essentially an action which can have asynchronous side effects, for example, reaching out to `backend-scaffold` via an HTTP request to sign in the user. That `login` thunk, and others related to auth, live in [`src/auth/ducks/thunks.ts`](https://github.com/Code-4-Community/frontend-scaffold/tree/master/src/auth/ducks/thunks.ts). A thunk returns a function which makes the async call, then dispatches another action depending on the result. 

Throughout our application, we'll use a utility called `AsyncRequest`. This is a simple yet powerful tool for building asynchronous data into our Redux store. It's not super complicated, and it's not even a separate library - it's simply a file in [`src/utils/asyncRequest.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/utils/asyncRequest.ts), and it's only 246 lines. 

The motivation behind AsyncRequest is that all actions, reducers, and state around asynchronous requests are very similar, with the difference being the resulting data payload or error payload for each.]

So, our auth state has one `AsyncRequest` field, `tokens`. It contains the result of either a login or signup action. We store, in our state, whether the request is `NotStarted`, `Loading`, `Loaded`, or `Failed`. All `AsyncRequest`s have these same properties. And depending on those properties, we can access the resulting data payload or error payload. 

Let's get back to our story. In our login thunk, we call `authClient.login()`. If the result is successful, we then `dispatch(authenticateUser.loaded(response))`. Here, we're using part of `AsyncRequest`, specifically the `generateAsyncActions` utility which we use in `src/auth/ducks/actions.ts`. This utility gives us the `loaded` and `failed` actions we use depending on the result of the async request in our thunk. 

Following the dispatch of the loaded action, we'll then reach our reducer in [`src/auth/ducks/reducers.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/auth/ducks/reducers.ts). Here, we again use an `AsyncRequest` utility: `generateAsyncRequestReducer`. This reducer will automatically update the `tokens` field of our state, which is the `AsyncRequest` itself, depending on the result of the action. 

It's a bit magic, and I haven't done a perfect job of explaining it here. But, after this is all over, what's important is the data that we now have in our state. If the type of our AsyncRequest is completed, that implies that we have a resulting payload, in this case a `TokenPayload` containing access and refresh JSON Web Tokens (JWTs). 

We use those tokens to make requests to the backend, but they also contain important information - the user's ID and privilege level. This leads us to our final destination on our journey, [`src/auth/ducks/selectors.ts`](https://github.com/Code-4-Community/frontend-scaffold/blob/master/src/auth/ducks/selectors.ts). Here, we have a *selector*, `getPrivilegeLevel`, which takes as a parameter our `tokens` AsyncRequest. It returns, depending on the state of the AsyncRequest and the resulting payload, the privilegeLevel of the user. You can access this data in your React component with the `useSelector` hook:
```
const privilegeLevel: PrivilegeLevel = useSelector((state: C4CState) => {
    getPrivilegeLevel(state.authenticationState.tokens)
});
```



