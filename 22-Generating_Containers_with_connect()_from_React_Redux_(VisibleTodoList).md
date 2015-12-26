# 27. Generating Containers with `connect()` from React Redux (`VisibleTodoList`)
[Video Link](https://egghead.io/lessons/javascript-redux-generating-containers-with-connect-from-react-redux-visibletodolist)

Now that we have `react-redux`, we are using its `Provider` component to pass `store` down via context.

Our container components are similar: they need to re-render when the store's state changes, they need to unsubscribe from the store when they unmount, and they take the current state from the Redux `store` and use it to render the presentational components with some props that they calculate.

They also need to specify the `contextTypes` to get the store from the context.

We're going to write `VisibleTodoList` in a different way now.

We'll start by writing a function `mapStateToProps` which takes the Redux store's state, and returns the props that we need to pass to the presentational `TodoList` component so it can be rendered with the current `state`.

In this case, `TodoList` only takes a single prop called `todos`, so we can move the expression into our `mapStateToProps` function. It returns the props that depend on the current state of the Redux store, which in this case is just the `todos`. 

```JavaScript
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos (
      state.todos,
      state.visibilityFilter
    )
  };
}
```

Now we'll create another function called `mapDispatchToProps` that accepts the `dispatch()` from `store` as its only argument. It returns the props that should be passed to the `TodoList` component that depend on the `dispatch()` method. In the case of `TodoList`, the only prop that `TodoList` took that requires `store.dispatch()` is `onTodoClick`. So we will remove `onTodoClick` from `TodoList` and put it into `mapDispatchToProps`'s `return`. Note that we don't need the reference to `store` anymore, and can just use `dispatch`.

```JavaScript
const mapDispatchToProps = (dispatch) => {
  return {
    onTodoClick: id => {
      dispatch({
        type: 'TOGGLE_TODO',
        id
      })
    }
  };
}
```

##### Review of what we just did...
We created a function `mapStateToProps` that maps the Redux store's state to the props of the `TodoList` component that are related to the data from the Redux store.

We also created a function `mapDispatchToProps` that maps the `dispatch()` method of the store to the callback props of our `TodoList` component. It specifies the behavior of which callback prop dispatches which action. 

##### The `connect()` function
Together, these two new functions describe a container component so well that instead of writing it we can generate it by using the `connect()` function provided by `react-redux`.

Now instead of creating a `VisibleTodoList` class, we can declare a variable and use the `connect()` method to obtain it. We'll pass in `mapStateToProps` as the first argument, and `mapDispatchToProps` as the second.

This is a [curried function](https://medium.com/@kbrainwave/currying-in-javascript-ce6da2d324fe#.ytohz3iob), so we call it again with the presentational component that we want it to wrap and pass the props to (in this case, `TodoApp`).


```JavaScript
const { connect } = ReactRedux;
// import { connect } from 'react-redux';

const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatchToProps
)(TodoList)

```

The `connect()` function will generate the component just like the one we wrote by hand, so we don't have to write the code to subscribe to the store or to specify the context types manually. 

[3:41 in the video has the recap of what we've just done.](https://egghead.io/lessons/javascript-redux-generating-containers-with-connect-from-react-redux-visibletodolist)


