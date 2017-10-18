# 16. Implementing `combineReducers()` from Scratch
[Video Link](https://egghead.io/lessons/javascript-redux-implementing-combinereducers-from-scratch)

Now that we know how to use `combineReducers()` to save us some time, let's implement it from scratch in order to understand it deeper.

Recall that the only argument to `combineReducers()` is the mapping between the state keys and their corresponding reducers, so we'll start by writing a function that accepts a parameter we'll call "reducers".

Since `combineReducers()` returns a reducer function, it must have the signature of a reducer function (the state and an action).

Inside of our return reducer, we call the `Object.keys()` function to get all the keys from our `reducers` object (in our example, this is `todos` and `visibilityFilter`).

We then run `reduce()` on the keys because we want to produce a single value that represents the next state. We do this by accumulating over every key and running its associated reducer.

Since each reducer that is run through `combineReducers()` is responsible for only part of the application's overall state, we say that the next state for a given key can be calculated by calling the corresponding reducer for the given key with the current state (for the given key) & the action.

The array reduce wants us to return the next accumulated value from the callback (i.e. `nextState`). We also specify an empty object as the initial next state before all the keys are processed.

```Javascript
const combineReducers = reducers => {
  return (state = {}, action) => {

    // Reduce all the keys for reducers from `todos` and `visibilityFilter`
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        // Call the corresponding reducer function for a given key
        nextState[key] = reducers[key] (
          state[key],
          action
        );
        return nextState;
      },
      {} // The `reduce` on our keys gradually fills this empty object until it is returned.
    );
  };
};
```

Call combineReducers with an object whose values are the reducer functions and keys are state fields they manage.

```JavaScript
const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```


It's okay that we are mutating the empty object representing `nextState`, because it was created within the `combineReducers()` function and not passed in from the outside. Thus, our function remains pure.

It's important to understand functional programming-- functions can take other functions as arguments, and return other functions. Knowing this will increase productivity with Redux in the long term.


<p align="center">
<a href="./10-Reducer_Composition_with_combineReducers.md"><- Prev</a>
<a href="./12-React_Todo_List_Example_Adding_a_Todo.md">Next -></a>
</p>
