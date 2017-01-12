# 19. React Todo List Example (Filtering Todos)
[Video Link](https://egghead.io/lessons/javascript-redux-react-todo-list-example-filtering-todos)

We've created a user interface for our Todo list that lets us add and toggle todo items. Now we will implement the visibility filter to show only the items that the user wants to see.

We will start by creating a new `FilterLink` component that the user will click to switch the current visible items. This component accepts the `filter` prop (just a string) & `children` (the contents of the link).

The component will be a simple `<a>` tag that when clicked will dispatch an action of type `'SET_VISIBILITY_FILTER'` along with a `filter` prop so the reducer knows which filter is being clicked.

We pass the `children` down to the `<a>` tag so the text of the link can be specified.

```JavaScript
.
. // Reducer code, etc.
.
const FilterLink = ({
  filter,
  children
}) => {
  return (
    <a href='#'
       onClick={e => {
         e.preventDefault();
         store.dispatch({
           type: 'SET_VISIBILITY_FILTER',
           filter
         });
       }}
    >
      {children}
    </a>
  )
}
.
.
.
```

Now that we have created `FilterLink`, we can use it in our `TodoApp` component:
```JavaScript
.
. // TodoApp component stuff including the the <ul> of todo items...
. // This <p> tag is to be rendered below the list.
<p>
  Show:
  {' '}
  <FilterLink
    filter='SHOW_ALL'
  >
    All
  </FilterLink>
  {' '}
  <FilterLink
    filter='SHOW_ACTIVE'
  >
    Active
  </FilterLink>
  {' '}
  <FilterLink
    filter='SHOW_COMPLETED'
  >
    Completed
  </FilterLink>
</p>
.
. // close the containing `<div>` and the TodoComponent
.

```
Now we've got some links to select the filter, but they don't have a visible effect yet because we don't interpret the value of the visibility filter.

We need to create a `getVisibleTodos()` function that will help us filter the todos according to the filter value.

`getVisibleTodos()` will take two arguments: The list of `todos` and the `filter`. Inside will be a switch statement that operates based on the current filter value.


```JavaScript
.
. // FilterLink component creation
.

const getVisibleTodos = (
  todos,
  filter
) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos;
    case 'SHOW_COMPLETED':
      // Use the `Array.filter()` method
      return todos.filter(
        t => t.completed
      );
    case 'SHOW_ACTIVE':
      return todos.filter(
        t => !t.completed
      );
  }
}
```

Now we need to call `getVisibleTodos()` from our `TodoApp` component before we render the list.

Inside the `render()` function of the `TodoApp` component, we get the visible todos by calling `getVisibleTodos()` with the list of `todos` and the `visibilityFilter` from our props.

We will now use `visibleTodos` instead of `this.props.todos` when rendering our list.
```JavaScript
.
.
.
class TodoApp extends Component {
  render() {
    const visibleTodos = getVisibleTodos(
      this.props.todos,
      this.props.visibilityFilter
    );
    .
    . // Input and Button stuff
    .
    <ul>
      {visibleTodos.map(todo =>
        .
        . // `<li>` click handling and item rendering
        .
      )}
    </ul>
    .
    . // `<p>` filter selection stuff
    .
  }
}
```

In order to use the `visibilityFilter` inside of our `TodoApp` component, we must pass it as a prop inside of our `render()` function. We could do this explicitly, but it's faster to spread over all of the straight fields inside the `state` object and pass all of them as props to the `TodoApp` component.

```JavaScript
const render = () => {
  ReactDOM.render(
    <TodoApp
      {...store.getState()}
    />,
    document.getElementById('root')
  );
};

store.subscribe(render);
render();
```

Now when we add some todo items and then "complete" them, we can show the list according to our selected visibility filter.

However, it would be nice to differentiate between our filter links by showing which one we have selected.

We'll start by using ES6 destructuring inside of the `TodoApp` component to extract `todos` and `visibilityFilter` from the props. Now we can access them directly instead of having to type "`this.props.`" every time.

```JavaScript
class TodoApp extends Component {
  render() {
    const {
      todos,
      visibilityFilter
    } = this.props;
    const visibleTodos = getVisibleTodos(
      todos,
      visibilityFilter
    );
    return (
     .
     . // input, button, and list stuff
     .
```
Now we'll include the current `visibilityFilter` with our `FilterLink`s so it can know which is the current one and apply different styles accordingly.

```JavaScript
<p>
  Show:
  {' '}
  <FilterLink
    filter='SHOW_ALL'
    currentFilter={visibilityFilter}
  >
    All
  </FilterLink>
  {' '}
  <FilterLink
    filter='SHOW_ACTIVE'
    currentFilter={visibilityFilter}
  >
    Active
  </FilterLink>
  {' '}
  <FilterLink
    filter='SHOW_COMPLETED'
    currentFilter={visibilityFilter}
  >
    Completed
  </FilterLink>
</p>
```

Now that we've included the `visibilityFilter`, we go back to our `FilterLink` declaration to add `currentFilter` as a prop along with a condition that says when the filter is the current filter, it will become static text.

```JavaScript
const FilterLink = ({
  filter,
  currentFilter,
  children
}) => {
  if (filter === currentFilter) {
    return <span>{children}</span>
  }
  .
  . // rest of `FilterLink` as defined earlier
  .
}
```

#### To review how changing a visibility filter works...
Clicking one of the filter types dispatches an action of type `'SET_VISIBILITY_FILTER'` and passes `filter` which is a prop to the `FilterLink` component (every one of the 3 links will have a different `filter` prop).

The `store.dispatch()` function calls our `todoApp()` root reducer with the state and the action, which in turn calls the `visibilityFilter()` reducer with the part of the state and the action.

Note that when the action is of type `'SET_VISIBILITY_FILTER'`, it doesn't care about the previous state. It just returns `action.filter` as the next state of the `visibilityFilter()` reducer. The root reducer will use this new field as part of its new `state` object.

Because the `render()` function is subscribed to changes in `store`, it's going to get the new `state` object and pass all its keys as props to the `TodoApp` component.

The `TodoApp` component receives all the `todos` as well as the newly updated `visibilityFilter` as its props, which are then passed to the `getVisibleTodos()` function The currently visible `todos` are calculated based on the current visibilty filter (`'SHOW_ALL'`, `'SHOW_COMPLETED'`, or `'SHOW_ACTIVE'`).

Depending on which filter is selected, `getVisibleTodos()` may return a brand new array of `todos` containing only the appropriate items. This returned array is then enumerated and rendered inside of `TodoApp`'s `render()` function.

The `visibilityFilter` field is also used by the `FilterLinks` as the `currentFilter` because the `FilterLink` wants to know if its filter is the current one so it can render the style appropriately (i.e. the current filter is active, so the user shouldn't be able to click it).

...thus the cycle is complete.

<p align="center">
<a href="./13-React_Todo_List_Example_Toggling_a_Todo.md"><- Prev</a>
<a href="./15-Extracting_Presentational_Components_Todo__TodoList.md">Next -></a>
</p>
