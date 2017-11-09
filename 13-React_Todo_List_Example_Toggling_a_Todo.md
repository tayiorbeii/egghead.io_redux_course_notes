# 18. React Todo List Example (Toggling a Todo)
[Video Link](https://egghead.io/lessons/javascript-redux-react-todo-list-example-toggling-a-todo)

Building onto what we've been working on, it's time to dispatch our `'TOGGLE_TODO'` action. We will do this by clicking on the individual todo items in our bulleted list.

Inside of our `TodoApp` component, we map each of the todo items into an `<li>`. We add a click handler so that when a user clicks on an item, we will dispatch an action to the store of type `'TOGGLE_TODO'` along with the `id` to be toggled (we get the `id` from the `todo` object.)

In the UI, we want the todo item to appear as crossed out if it has been completed, so we'll use the `textDecoration` style property.

```Javascript
.
. // TodoApp component stuff
.
<ul>
  {this.props.todos.map(todo =>
    <li key={todo.id}
        onClick={() => {
          store.dispatch({
            type: 'TOGGLE_TODO',
            id: todo.id
          });
        }}
        style={{
          textDecoration:
            todo.completed ?
              'line-through' :
              'none'
        }}>
      {todo.text}
    </li>
  )}
</ul>
.
. // More TodoApp component stuff
.
```

#### Recap of how toggling a todo item works
Inside the click handler, we dispatch the `'TOGGLE_TODO'` action with a type of `'TOGGLE_TODO'` and the `id` of the todo being rendered.

When an action is dispatched, the store will call the root reducer, which will call the `todos()` reducer with the array of todos & the action.

Since this action is of type `'TOGGLE_TODO'`, the `todos()` reducer delegates the handling of every todo item to the `todo()` reducer by using the `map()` function to call it for every todo item in `state`:

```Javascript
const todos = (state = [], action) => {
  switch (action.type) {
    // case 'ADD_TODO' stuff
    case 'TOGGLE_TODO':
      return state.map(t =>
        todo(t, action)
      );
    // default case stuff
  }
}
```

The `todo()` reducer receives the todo item as `state`, and 'TOGGLE_TODO' as `action`. For every todo item whose `id` doesn't match the `id` in the action (remember the action's `id` was supplied by clicking the `<li>`), we just return the previous state (i.e. the `todo` object as it was).

However, if the `id` of the todo matches the `id` of the action, we'll use ES6 notation to return a new object with all the properties of the original todo, but with the `completed` field toggled.
```JavaScript
const todo = (state, action) => {
  // case 'ADD_TODO' stuff
  case 'TOGGLE_TODO':
    if (state.id !== action.id) {
      return state;
    }

    return {
      ...state,
      completed: !state.completed
    };
  // default case stuff
};
```

The updated todo item will be included in the `todos` field under the new application `state`, and because the `render()` function subscribes to the store, it's going to get the next state of the application via `store.getState()` and pass the new version of the `todos` array to the `TodoApp` component to be mapped and rendered as a bulleted list (where completed items have a line through them).

```JavaScript
const render = () => {
  ReactDOM.render(
    <TodoApp
      todos={store.getState().todos}
    />,
    document.getElementById('root')
  );
};

store.subscribe(render);
render();
```
Thus, our cycle is complete again.

<p align="center">
<a href="./12-React_Todo_List_Example_Adding_a_Todo.md"><- Prev</a>
<a href="./14-React_Todo_List_Example_Filtering_Todos.md">Next -></a>
</p>
