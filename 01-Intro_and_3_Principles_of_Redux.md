# 01. The Single Immutable State Tree
[Video Link](https://egghead.io/lessons/javascript-redux-the-single-immutable-state-tree?series=getting-started-with-redux)

The first principle of Redux (no matter the complexity):

**The entire state of the application will be represented by one JavaScript object.**

All mutations and changes to the state in Redux are explicit.

Everything that changes in the application, including the data and the UI state, is contained in a single object called the **state** or **state tree**.

Since the entire state is represented in a single object, we are able to keep track of changes over time

**State in Todo App**
```
"current_state:"
[object Object] {
  todos: [[object Object] {
    completed: true,
    id: 0,
    text: "hey",
  }, [object Object] {
    completed: false,
    id: 1,
    text: "ho",
  }],
  visibilityFilter: "SHOW_ACTIVE"
}
```

# 02. Describing State Changes with Actions
[Video Link](https://egghead.io/lessons/javascript-redux-describing-state-changes-with-actions?series=getting-started-with-redux)

The second principle of Redux is that **the *state tree* is read only**.
Any time you want to change the state, you have to dispatch an **action**. An action is a plain JS object describing the change. Just like the state is the minimal representation of the data, the action is the minimal representation of the change to that data.

The only requirement for an action is that it has a type property (conventionally a String).

For example, in a counter app, there are `INCREMENT` and `DECREMENT` actions. In the case of a ToDo app, the display components don't know how an item was added to the list-- all they know is that an `ADD_TODO` action was dispatched, with `text` content "hey" and a sequential `id`.

The overall principle here is that the state is read only, and can only be modified by dispatching actions.

# 03. Pure and Impure Functions
[Video Link](https://egghead.io/lessons/javascript-redux-pure-and-impure-functions)

Before learning more about Redux, it's important to know the difference between "Pure" and "Impure" functions.

**Pure:**
```JavaScript
function square(x) {
  return x * x;
}
function squareAll(items) {
  return items.map(square);
}
```
Pure functions are those whose return values depend only upon the values of their arguments. Pure functions don't have side effects like network or database calls. Pure functions also do not override the values of anything. In the above example, a new array is returned instead of modifying the `items` that was passed in.

**Impure:**
```JavaScript
function square(x) {
  updateXInDatabase(x);
  return x * x;
}
function squareAll(items) {
  for (let i = 0; i < items.length; i++) {
    items[i] = square(items[i]);
  }
}
```
Contrast the "Impure" function. A database is called, and values passed in are being overwritten.

This distinction is important to understand, since Redux requires that certain functions are pure.

# 04. The Reducer Function
[Video Link](https://egghead.io/lessons/javascript-redux-the-reducer-function)

React introduced the idea that the UI layer is most predictable when it is described as a pure function of the application's state.

Redux complements this approach by requiring that state mutations in your app need to be described by a pure function that takes the previous state and the action being dispatched, and returns the next state of your application.

**Inside a Redux application there is one particular function that takes the previous state and the action being dispatched, and returns the next state of the whole application**. It is important that the function is pure (i.e. the state being given to it isn't modified) because it has to return the new object representing the application's new state.

Even in large applications, there is still just a single function that calculates the new state of the application. It doesn't have to be slow-- if certain parts of the state haven't changed, their references can stay as-is. In the ToDo app example, when changing the visibility between "All/Completed/Active" the actual items themselves haven't changed, so the reference to the previous version of the `todos` array is left intact.

This is the 3rd and final principle of Redux: to describe state mutations you have to write a function that takes the previous state of the app and the action being dispatched, then returns the next state of the app. This function is called the **Reducer**.


<p align="center">
<a href="./02-Reducer_and_Store.md">Next -></a>
</p>
