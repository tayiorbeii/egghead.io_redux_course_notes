# 21. Extracting Presentational Components (AddTodo, Footer, FilterLink)
[video link](https://egghead.io/lessons/javascript-redux-extracting-presentational-components-addtodo-footer-filterlink)

We've refactored `TodoApp` to use a `TodoList` component.

Let's keep working on separating the looks from the behavior.


#### Current `TodoApp` Code:
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

#### Extracting the Input and the Button into `AddTodo`
We will combine the input and the button into one new component called `AddTodo`.

Functional components don't have instances, so instead of using `this`, we will use a variable called `input` that we will close over so we can write to it inside of the function.

Since we want `AddTodo` to be a presentational component, we will have the button call an `onAddClick()` function with `input`'s value as its parameter. We also make `onAddClick` a prop so that the component that uses `AddTodo` can specify what happens when the "Add Todo" button is clicked.

```JavaScript
const AddTodo = ({
  onAddClick
}) => {
  let input;

  return (
    <div>
      <input ref={node => {
        input = node;
      }} />
      <button onClick={() => {
        onAddClick(input.value);
        input.value = '';
      }}>
        Add Todo
      </button>
    </div>
  );
};
```

Now we need to update the `TodoApp` container component by replacing the `<input>` and `<button>` entries with
our new `AddTodo` component.

We will also specify our `onAddClick` function to dispatch an action of type `'ADD_TODO'` along with the corresponding `text` and next `id`.

```JavaScript
.
. // inside `TodoApp`'s `render` method
.
return (
  <div>
    <AddTodo
      onAddClick={text =>
        store.dispatch({
          type: 'ADD_TODO',
          id: nextTodoId++,
          text  
        })
      }
    />
.
.
.
```

#### Extracting the `FilterLink` Footer Elements
Now we will create a new functional component called `Footer`. Since each `FilterLink` needs to know the `visibilityFilter`, we will make that a prop.

We want the `Footer` and `FilterLink` to be presentational components, but in its current implementation each of the `FilterLink`s contain a `store.dispatch()` call. This call will be replaced by an `onClick` call that will take a single parameter with the filter. We also add `onClick` to `FilterLink`'s props.

Since `onClick` is now a prop for `FilterLink`, we need to specify it every time that `FilterLink` is used in our `Footer`. Adding `onClick={onFilterClick}` makes sure that `onClick` makes it to `FilterLink` as a prop.

```JavaScript
// FilterLink was built in a previous section
const FilterLink = ({
  filter,
  currentFilter,
  children,
  onClick
}) => {
  if (filter === currentFilter) {
    return <span>{children}</span>
  }

  return (
    <a href='#'
      onClick={e => {
        e.preventDefault();
        onClick(filter);
      }}
    >
      {children}
    </a>
  );
};

const Footer = ({
  visibilityFilter,
  onFilterClick
}) => (
  <p>
    <FilterLink
      filter='SHOW_ALL'
      currentFilter={visibilityFilter}
      onClick={onFilterClick}
    >
      All
    </FilterLink>
    {', '}
    <FilterLink
      filter='SHOW_ACTIVE'
      currentFilter={visibilityFilter}
      onClick={onFilterClick}
    >
      Active
    </FilterLink>
      {', '}
    <FilterLink
      filter='SHOW_COMPLETED'
      currentFilter={visibilityFilter}
      onClick={onFilterClick}
    >
      Completed
    </FilterLink>
  </p>
);
```

#### Adding `Footer` to `TodoApp`
When adding the `Footer` component into `TodoApp`, we need to pass two props. First, `visibilityFilter` to highlight the active link. The second prop is `onFilterClick`, which will dispatch an action of type `'SET_VISIBILITY_FILTER'` along with the `filter` being clicked.

```JavaScript
.
. // inside `TodoApp`'s `render` method
.
return (
  <div>
    // `<AddTodo>` component
    // `<TodoList>` component
    <Footer
      visibilityFilter={visibilityFilter}
      onFilterClick={filter =>
        store.dispatch({
          type: 'SET_VISIBILITY_FILTER',
          filter
        })
      }
    />
  </div>
.
.
.
```

#### Changing `TodoApp` into a function
It is possible to change `TodoApp` from a class into a function.

This allows us to eliminate the destructuring of `todos` and `visibilityFilter` from `this.props` inside the `render` function. Instead, we can do this inside the argument to the `TodoApp` function.

We can also do away with the `render()` declaration.

Since the `visibleTodos` are only used in a single place, we can move its declaration into the `TodoList` `todos` prop declaration.

```JavaScript
const TodoApp = ({
  todos,
  visibilityFilter
}) => {
  return (
    <div>
      <AddTodo
        onAddClick={text =>
          store.dispatch({
            type: 'ADD_TODO',
            id: nextTodoId++,
            text
          })
        }
      />
      <TodoList
        todos={
          getVisibleTodos(
            todos,
            visibilityFilter
          )
        }
        onTodoClick={id =>
          store.dispatch({
            type: 'TOGGLE_TODO',
            id
          })
        }
      />
      <Footer
        visibilityFilter={visibilityFilter}
        onFilterClick={filter =>
          store.dispatch({
            type: 'SET_VISIBILITY_FILTER',
            filter
          })
        }
      />
    </div>
  );
}
```

#### Recap of the Data Flow
We've now finished the initial refactor of our application into a single container component with many presentational components inside of it.

[At 4:10 in the video, Dan walks us through the current code & data flow in the application.](https://egghead.io/lessons/javascript-redux-extracting-presentational-components-addtodo-footer-filterlink)

Separation of presentational components isn't required in Redux, but it's a good pattern to follow because it decouples our rendering from Redux. This way, if we choose to move to another framework like Relay, we can keep our presentation components as-is.

One of the downsides to having separate presentational components is that we have to move around a lot of props, including the callbacks. However, we can easily solve this problem by introducing many intermediate container components, which we will start on in the next section.

<p align="center">
<a href="./15-Extracting_Presentational_Components_Todo__TodoList.md"><- Prev</a>
<a href="./17-Extracting_Container_Components_FilterLink.md">Next -></a>
</p>
