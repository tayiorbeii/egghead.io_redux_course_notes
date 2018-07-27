# 07. Implementing Store from Scratch
[Video Link](https://egghead.io/lessons/javascript-redux-implementing-store-from-scratch)

We just used the `createStore()` function, but in order to understand it better, let's write it from scratch!


We know that we need to pass in the `reducer` function, and that `getState()` will return the current value of `state`. We also know that we need to be able to dispatch actions, and subscribe.

Because the `subscribe()` function can be called many times, we need to keep track of all the change listeners, so we create an array of listeners; everytime `subscribe()` is invoked we will push in the new listener to the array.

Dispatching an action is the only way to change the internal state, so we calculate the new state as the result of calling `reducer()` with the current `state` and the `action` being dispatched. After the `state` is updated, we notify every change listener by calling them.

In order to unsubscribe an event listener, we'll return a function from the `subscribe()` method that removes the listener from the listeners array.

By the time the store is returned, we want the initial state to be populated. We're going to dispatch a dummy action just to get the reducer to return the initial value.

```Javascript
const createStore = (reducer) => {
  let state;
  let listeners = [];

  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(l => l !== listener);
    }
  };

  dispatch({}); // dummy dispatch

  return { getState, dispatch, subscribe };

};
```

That's pretty much it!

<p align="center">
<a href="./02-Reducer_and_Store.md"><- Prev</a>
<a href="./04-React_Counter_Example.md">Next -></a>
</p>
