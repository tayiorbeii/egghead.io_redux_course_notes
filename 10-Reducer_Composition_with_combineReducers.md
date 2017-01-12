# Reducer Composition with `combineReducers()`
[Video Link](https://egghead.io/lessons/javascript-redux-reducer-composition-with-combinereducers)

Since reducer composition is so common in Redux, there's a helper function `combineReducers()`

So now instead of this:
```JavaScript
const todoApp = (state = {}, action) => {
  return {
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

We can do this:

```JavaScript
const { combineReducers } = Redux; // CDN Redux import

const todoApp = combineReducers({
  todos: todos,
  visibilityFilter: visibilityFilter
});
```

This code generates our top level reducer. The only argument to `combineReducers()` is an object that specifies the mapping between the state field names and the reducers that manage them.

The return value of `combineReducers()` is a reducer function that is pretty much equivalent to what we wrote earlier.

**By convention, the state keys should be named after the reducers that manage them.** Because of this, we can use ES6 object literal shorthand notation to accomplish the same thing like this:

```JavaScript
const todoApp = combineReducers({
  todos,
  visibilityFilter
});
```


<p align="center">
<a href="./09-Reducer_Composition_with_Objects.md"><- Prev</a>
<a href="./11-Implementing_combineReducers_from_Scratch.md">Next -></a>
</p>
