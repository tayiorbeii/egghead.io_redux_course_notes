# 30. Extracting Action Creators
[Video Link](https://egghead.io/lessons/javascript-redux-extracting-action-creators)

So far we've covered container components, presentational components, reducers, and the store... but we haven't covered *action creators*.

In our current `AddTodo` component, we dispatch an action of type `'ADD_TODO'` when the "Add Todo" button is clicked. However, it references the `nextTodoId` variable which is declared alongside the component. Normally it would be local, but what if another component wants to be able to dispatch `'ADD_TODO'`? The component would need to have access to `nextTodoId`.


##### Existing `AddTodo` Code
```JavaScript
let nextTodoId = 0;
let AddTodo = ({ dispatch }) => {
  let input;

  return (
    <div>
      <input ref={node => {
        input = node;
      }} />
      <button onClick={() => {
        dispatch({
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
In order to allow other components to dispatch the `'ADD_TODO'` action, it would be best if `'ADD_TODO'` didn't have to worry about specifying the `id`. The only information that really is passed is the `text` of the todo being added. We don't want to generate the `id` inside of the reducer because that would make it [non-deterministic](https://en.wikipedia.org/wiki/Nondeterministic_algorithm).


#### Action Creator

Our first action creator will be `addTodo`. This will be a function that takes the `text` of the todo and constructs an action object representing the `'ADD_TODO'` action.

Replace the code inside the `dispatch()` call inside of the `AddTodo` component with a call to `addTodo()`.
```JavaScript
// inside `AddTodo` component
<button onClick={() => {
  dispatch(addTodo(input.value))
  input.value = '';
}}>
.
.
.
```

Implement the `addTodo` action creator:

```JavaScript
let nextTodoId = 0;
const addTodo = (text) => {
  return {
    type: 'ADD_TODO',
    id: nextTodoId++,
    text
  };
};
```

Action creators are typically kept separate from components and reducers in order to help with maintainability.In this case, we'll put all of our action creators together near the top of the file.

We have other actions we can extract from our components:

#### `'SET_VISIBILITY_FILTER'` inside of `mapDispatchToLinkProps`:

Before:
```JavaScript
const mapDispatchToLinkProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch({
        type: 'SET_VISIBILITY_FILTER',
        filter: ownProps.filter
      });
    }
  };
}
```

After:
```JavaScript
const setVisibilityFilter = (filter) => {
  return {
    type: 'SET_VISIBILITY_FILTER',
    filter
  };
};

.
. // Further down the file...
.
const mapDispatchToLinkProps = (
  dispatch,
  ownProps
) => {
  return {
    onClick: () => {
      dispatch(
        setVisibilityFilter(ownProps.filter)
      );
    }
  };
}
```

#### `'TOGGLE_TODO'` inside of `mapDispatchToTodoListProps`:
After:
```JavaScript
const toggleTodo = (id) => {
  return {
    type: 'TOGGLE_TODO',
    id
  };
};
.
. // Further down the file...
.
const mapDispatchToTodoListProps = (
  dispatch
) => {
  return {
    onTodoClick: (id) => {
      dispatch(toggleTodo(id));
    }
  };
}
```

#### Recap
Keeping all of our action creators together in one place helps to inform others who look at our code what actions are capable of taking place. They can also be used by different components, as well as in tests.

Whether you use action creators or not, the data flow is the same.

<p align="center">
<a href="./24-Generating_Containers_with_connect_from_Readct_Redux_FooterLink.md"><- Prev</a>
</p>
