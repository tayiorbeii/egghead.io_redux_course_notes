# 20. Extracting Presentational Components (Todo, Todolist)
[Video Link](https://egghead.io/lessons/javascript-redux-extracting-presentational-components-todo-todolist)

We have a working example of our app now, but our `TodoApp` component has the input, button, list of todos, and filter links all inside of it.

We are going to refactor this single component into separate pieces so that they can be tested and worked on individually from one another.

##### Current `TodoApp` Component Code:

```JavaScript
class TodoApp extends Component {
  render () {
    const {
      todos,
      visibilityFilter
    } = this.props;

    const visibleTodos = getVisibleTodos(
      todos,
      visibilityFilter
    );

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
          Add Todo
        </button>
        <ul>
          {visibleTodos.map(todo =>
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
                  }}
              >
                {todo.text}
              </li>
          )}
        </ul>
        <p>
          Show:
          {' '}
          <FilterLink
            filter="SHOW_ALL"
            currentFilter={visibilityFilter}
          >
            All
          </FilterLink>
          {', '}
          <FilterLink
            filter='SHOW_ACTIVE'
            currentFilter={visibilityFilter}
          >
            Active
          </FilterLink>
          {', '}
          <FilterLink
            filter='SHOW_COMPLETED'
            currentFilter={visibilityFilter}
          >
            Completed
          </FilterLink>
        </p>
      </div>
    );
  }
}
```
#### Refactoring for a Single Todo Item
First, we will extract the Todo component that renders a single list item.

We will declare the Todo item as a function, which is available in React 14. We can remove the `key` property, since it's only needed when we enumerate an array (we'll use it later when we have to enumerate many todos).

Previously we had hardcoded a click handler that dispatched `'TOGGLE_TODO'`. It's best practice with React to have several components that don't specify any behaviors, and only are concerned with how things are rendered (how they look). These are called **presentational components**.

Because we want our list to be a presentational component, we "promote" the `onClick` handler to become a prop.

We also want to be more explicit about what the data is that the component needs to render. Instead of passing a `todo` object, we will pass `completed` and `text` fields as separate props.

```JavaScript
const Todo = ({
  onClick,
  completed,
  text
}) => (
  <li
    onClick={onClick}
    style={{
      textDecoration:
        completed ?
          'line-through' :
          'none'
    }}
  >
    {text}
  </li>
);
```

Now our `Todo` component is purely presentational. It doesn't specify any behavior, but it knows how to render a single todo item.


#### Refactoring for the Todo List
The `TodoList` component will accept an array of todos, and will render them into a `<ul>` by using the `todos.map()` function to render a `Todo` component for each todo item.

We tell React to use each todo's `id` as the unique `key` for the elements, and we'll use the spread operator to send the `todo` object's `text` and `completed` properties are sent as props to the `Todo` component.

We need to specify what happens when a `Todo` is clicked. Since we want to keep this as a presentational component, instead of dispatching an action, we'll specify a function `onTodoClick()` and pass it `todo.id` so it can decide what needs to happen. We will also pass `onClick` (which calls `onTodoClick()`) as a prop to the `Todo` component.

```JavaScript
const TodoList = ({
  todos,
  onTodoClick
}) => (
  <ul>
    {todos.map(todo =>
      <Todo
        key={todo.id}
        {...todo}
        onClick={() => onTodoClick(todo.id)}
      />
    )}
  </ul>
)
```

#### Container Components (`TodoApp`)
While presentational components just display data, we need a way to actually pass data from the store.
This is where **container components** come in-- they can specify behavior and pass data.

In our example, `TodoApp` is our container component.

Now that we've created `TodoList` and `Todo` presentational components, we can put them into our `TodoApp` container component.

Our `TodoApp` will render our `TodoList` with `visibleTodos` as the `todos`, along with a callback that says when `onTodoClick` is called with a todo `id`, we should dispatch an action on the store of type `'TOGGLE_TODO'` along with the `id` of the todo.

```JavaScript
class TodoApp extends Component {
	render () {
    const {
      todos,
      visibilityFilter
    } = this.props;

    const visibleTodos = getVisibleTodos(
      todos,
      visibilityFilter
    );

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
          Add Todo
        </button>
        <TodoList
          todos={visibleTodos}
          onTodoClick={id =>
            store.dispatch({
              type: 'TOGGLE_TODO',
              id
            })
          } />
      .
      . // FilterLink stuff
      .
      </div>
    );
  }
}
```

#### To Recap...
The `TodoApp` component renders a `TodoList` and passes it a function that can dispatch an action.

The `TodoList` component renders the `Todo` component, and passes an `onClick` prop which calls `onTodoClick()`.

The `Todo` component uses the `onClick` prop it receives and binds it to the list item's `onClick`. This way when it's called, the `onTodoClick()` is called, which in turn dispatches the action, which in turn updates the visibile todos, since the action updates the store.

<p align="center">
<a href="./14-React_Todo_List_Example_Filtering_Todos.md"><- Prev</a>
<a href="./16-Extracting_Presentational_Components_AddTodo__Footer__FilterLink.md">Next -></a>
</p>
