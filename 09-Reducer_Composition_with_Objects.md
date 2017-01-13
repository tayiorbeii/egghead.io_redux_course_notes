# 14. Reducer Composition with Objects
[Video Link](https://egghead.io/lessons/javascript-redux-reducer-composition-with-objects)

In the last section, we used reducer composition to manage Todos in an array.

While storing the application's state with just an array may work for small applications, we can use objects to store more information.

For example, we can add a visibility filter to our Todo application. The state of the visibility filter is a simple string representing the current filter. The filter is changed via the `SET_VISIBILITY_FILTER` action.
```JavaScript
const visibilityFilter = (
    state = 'SHOW_ALL',
    action
) => {
    switch (action.type) {
      case 'SET_VISIBILITY_FILTER':
        return action.filter;
      default:
        return state;
    }
};
```

**To store this new information, we don't need to change the existing reducers.**

We will use reducer composition to create a new reducer that calls existing reducers to manage their parts of the state, then combine the parts into a single state object.

```JavaScript
const todoApp = (state = {}, action) => {
  return {
     // Call the `todos()` reducer from last section
     todos: todos(
      state.todos,
      action
    ),
    visibilityFilter: visibilityFilter(
      state.visibilityFilter,
      action
    )
  };
};
```
Note that the first time it runs, it will pass `undefined` as the `state` to the child reducers because the initial state of the combined reducer is an empty object, so all its fields are undefined. Remember that when we call reducers with `undefined` that they return their initial states, thus populating the `state` for the first time.

When an action comes in, it calls the reducers with the parts of the state that they manage & the action then combines the results into the new `state` object.

Now that we have composed this new `todoApp` reducer, we will use it to create the store:
```JavaScript
// You've already imported Redux earlier in the app...
const store = createStore(todoApp);
```

This pattern helps to scale Redux development, since different team members can work on different reducers that work with the same actions, without stepping on each other's toes.


<p align="center">
<a href="./08-Reducer_Composition_with_Arrays.md"><- Prev</a>
<a href="./10-Reducer_Composition_with_combineReducers.md">Next -></a>
</p>
