# 13. Reducer Composition with Arrays
[Video Link](https://egghead.io/lessons/javascript-redux-reducer-composition-with-arrays)

We left off with code for a `todos` reducer.

The code is somewhat difficult to read because it mixes 2 different concerns: updating the todos array, as well as updating an individual todo item:

```JavaScript
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ];
    case 'TOGGLE_TODO':
      return state.map(todo => {
        if (todo.id !== action.id) {
          return todo;
        }

        return {
          ...todo,
          completed: !todo.completed
        };
      });
  }
}
```

Every time a function does too many things, it's best to break them up into other functions that each address only one concern.

In this case, "creating and updating a todo" is a separate task to undertake, so we'll bring this code into a new function that has two arguments: the current state, and the action being dispatched.

Note that in this new function that `state` refers to the individual todo, and not the list of todos.
```Javascript
const todo = (state, action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        id: action.id,
        text: action.text,
        completed: false
      };
    case 'TOGGLE_TODO':
      if (state.id !== action.id) {
        return state;
      }

      return {
        ...state,
        completed: !state.completed
      };
    default:
      return state;
  }
}
```

Now that we've extracted our `todo` reducer from our `todos` reducer, we have to call it for every todo item and assemble the results into an array:

```JavaScript
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        todo(undefined, action)
      ];
    case 'TOGGLE_TODO':
      return state.map(t => todo(t, action));
    default:
      return state;
  }
};
```
_Remember to have a `default` case where `state` is returned to avoid odd bugs in the future._

What we've just done is a common Redux practice called **reducer composition**. Different reducers specify how different parts of the state tree are updated in response to actions. Since reducers are normal JS functions, they can call other reducers to delegate & abstract away updates to the state.

Reducer composition can be applied many times. While there's a single top-level reducer managing the overall state of the app, it's encouraged to have reducers call each other as needed to manage the state tree.


<p align="center">
<a href="./07-Writing_a_Todo_List_Reducer.md"><- Prev</a>
<a href="./09-Reducer_Composition_with_Objects.md">Next -></a>
</p>
