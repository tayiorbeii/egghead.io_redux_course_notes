# 23. Extracting Container Components (VisibleTodoList, AddTodo)
[Video Link](https://egghead.io/lessons/javascript-redux-extracting-container-components-visibletodolist-addtodo)

Now let's work with the `TodoList` component. We want to keep it as a presentational component, but we want to
encapsulate reading the currently visible todos into a separate container component that connects the `TodoList` to the Redux store.

This component will be called `VisibleTodoList`. Just like when we created the `FilterLink` component, the data for `VisibleTodoList` will be calculated by using the current `state`. We will use the `getVisibleTodos()` function to go through all of the `todos` in the Redux store and determine which ones should be shown according to the `visibilityFilter`.

We also will specify the behavior of `onTodoClick()` to dispatch an action of type `'TOGGLE_TODO'` along with the `id`.

The same subscription logic we used in our `FilterLink` component needs to be included as well.

```JavaScript
class VisibleTodoList extends Component {
  componentDidMount() {
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }

  componentWillUnmount() {
    this.unsubscribe();
  }

  render() {
    const props = this.props;
    const state = store.getState();

    return (
      <TodoList
        todos={
          getVisibleTodos(
            state.todos,
            state.visibilityFilter
          )
        }
        onTodoClick={id =>
          store.dispatch({
            type: 'TOGGLE_TODO',
            id
          })
        }
      />
    );
  }
}
```
Remember, the job of all container components is similar-- connect a presentational component to the Redux store, and specify the data and behavior that it needs.

Now we can replace `TodoList` in our `TodoApp` with our newly created `VisibleTodoList`.


#### Changing `AddTodo` to a Container
In the last section we made `AddTodo` into a presentational component. Now we will backtrack on this.

We start by moving the `onClick` handler from `TodoApp` into the `AddTodo` component. We're doing this because there isn't a lot of presentaion or behavior here, and it's easier to keep them together until we figure out how to split the presentation from the behavior. For example, in the future we may decide to have a Form component

```JavaScript
const AddTodo = () => {
  let input;

  return (
    <div>
      <input ref={node => {
        input = node;
      }} />
      <button onClick={() => {
          store.dispatch({
            type: 'ADD_TODO',
            id: nextTodoId++,
            text: input.value
          })
        input.value = '';
      }}>
        Add Todo
      </button>
    </div>
  );
};
```



#### Refactored `TodoApp`
Now that we've refactored our components, it's become clear that none of the containers need props from `TodoApp`! We can also get rid of `TodoApp`'s `render()` function that rendered the current state of the store.

We can get rid of the `render()` function because the container components inside of `TodoApp` are now set up to get state and update themselves as needed, therefore, we
only need to render `TodoApp` once on initialization.

```JavaScript
const TodoApp = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
);

// Note this render does not belong to `TodoApp`
ReactDOM.render(
  <TodoApp />,
  document.getElementById('root')
);
```

#### Recap of Data Flow
[3:33 in the video](https://egghead.io/lessons/javascript-redux-extracting-container-components-visibletodolist-addtodo)

<p align="center">
<a href="./17-Extracting_Container_Components_FilterLink.md"><- Prev</a>
<a href="./19-Passing_the_Store_Down_Explicitly_via_Props.md">Next -></a>
</p>
