# 17. React Todo List Example (Adding a Todo)
[Video Link](https://egghead.io/lessons/javascript-redux-react-todo-list-example-adding-a-todo)

_WARNING: There's a lot going on here. It may be useful to watch the video as you go._

Now that we are managing our store and our actions, let's implement a view layer using React.

Existing code:
```JavaScript
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
};

const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        todo(undefined, action)
      ];
    case 'TOGGLE_TODO':
      return state.map(t =>
        todo(t, action)
      );
    default:
      return state;
  }
};

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

const { combineReducers } = Redux;
const todoApp = combineReducers({
  todos,
  visibilityFilter
});

const { createStore } = Redux;
const store = createStore(todoApp);
```


 _Hopefully you already know a bit  about React, JSX, props, etc._

 **React-specific JS:**
```Javascript
const { Component } = React;

let nextTodoId = 0;
class TodoApp extends Component {
  render() {
    return (
      <div>
        <button onClick={() => {
            store.dispatch({
              type: 'ADD_TODO',
              text: 'Test',
              id: nextTodoId++
            });
          }}>
          Add Todo
        </button>
        <ul>
          {this.props.todos.map(todo =>
            <li key={todo.id}>
              {todo.text}
            </li>
          )}
        </ul>
      </div>
    )
  };
}

// See Section 8 for earlier `render()` example
const render = () => {
  ReactDOM.render(
    // Render the TodoApp Component to the <div> with id 'root'
    <TodoApp
      todos={store.getState().todos}
    />,
    document.getElementById('root')

  )
};

store.subscribe(render);
render();
```

HTML:
```HTML
<!DOCTYPE html>
<html>
<head><!-- ... CDN imports for Redux and React ... --></head>

<body>
  <!-- This is where our React application will be rendered -->
  <div id='root'></div>
</body>
</html>
```

### Now that all that code is written...
With the code above, every time you click the "Add Todo" button, a new Todo item with the text "Test" is added to the bulleted list.

Let's add an `<input>` to our TodoApp component's return. We'll use React's callback `ref()` API.
`ref()` is a function that gets the node corresponding to the ref that we'll save with the name `this.input`.

From there, we can reference the value in `this.input` inside our button click handler, then reset the value after the `'ADD_TODO'` action has been dispatched.


```JavaScript
.
.
.
class TodoApp extends Component {
  render() {
    return (
      <div>
        <input ref={node => {
          this.input = node;
        }} />
        <button onClick={() => {
          store.dispatch({
            type: 'ADD_TODO',
            text: this.input.value,
            id: nextTodoId++
          });
          this.input.value = '';
        }}>
        .
        . // rest of TodoApp component code
        .
    );
  }
}

```

#### Recap of how the application works:

We start with the `TodoApp` component. This component isn't aware of how todos are being added, but what it can do is express its desire to mutate the state by dispatching an action with a type of `'ADD_TODO'`.

The `text` field for the todo item to be added is taken from the input box, along with an incrementing `id` for the todo item's id.

It is common for React components to dispatch actions in Redux apps, however it's equally important to be able to render the current state.

The `TodoApp` component assumes that it will receive `todos` as a prop, and it maps the items to display a list, using the `id` as a key (see the `<ul>` section in `TodoApp`).

We render the `TodoApp` component inside the `render()` function that runs any time the state changes (as well as when the app is initialized.) The `render()` function reads the current state of the store and passes the array of todos to the TodoApp component as a prop via the line `<TodoApp todos={store.getState().todos} />`.

The `render()` function is called every time there is a change to the store, so the `todos` prop is always up to date.

#### Recap of how mutations work in Redux:
Any change to state is caused by a `store.dispatch()` call somewhere in the component.

When an action is dispatched, the `store` calls the reducer it was created with with the current state & the action being dispatched. In the case of this example, this is the `todoApp` reducer that we obtained by `const todoApp = combineReducers({todos, visibilityFilter})`.

Continuing with our example, the `'ADD_TODO'` action type is matched in the switch statement inside the `todos()` reducer, so the child `todo()` reducer is called. The `todo()` child reducer is passed `undefined` (because there is no `state` for a new todo) and the action `'ADD_TODO'`

Inside of the `todo()` child reducer, we have a similar switch statement. Since `'ADD_TODO'` is matched, the reducer returns the initial state of the todo item (the `id` from `nextTodoId++` and `text` from the input box inside the `TodoApp` component, along with `completed: false`).

The `todos()` reducer that just called the child `todo()` reducer will then return a new array built from the existing items along with the newly created item added to the end (remember, this array is built using ES6's `...` spread operator).

Now our combined reducer `todoApp` will use this new `todos` array as the new value for the `todos` field in the global state object. So, it's going to return a new `state` object where the `todos` field corresponds to the array with the newly added todo item.

The `todoApp` reducer is the **root reducer** in this application. It is the one the store was created with, so its next state is the next state of the Redux store, and all the listeners are notified.

The `render()` function is subscribed to the store's changes, so it is called again and gets the fresh state with `store.getState()` and passes the updated `todos` as a prop to the `TodoApp` component.

Now the cycle can be repeated.


<p align="center">
<a href="./11-Implementing_combineReducers_from_Scratch.md"><- Prev</a>
<a href="./13-React_Todo_List_Example_Toggling_a_Todo.md">Next -></a>
</p>
