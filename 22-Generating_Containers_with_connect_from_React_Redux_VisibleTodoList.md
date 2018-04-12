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

Now we'll create another function called `mapDispatchToProps` that accepts the `dispatch()` from `store` as its only argument. It returns the props that should be passed to the `TodoList` component that depend on the `dispatch()` method. In the case of `TodoList`, the only prop that `TodoList` took that requires `store.dispatch()` is `onTodoClick`. So we will remove `onTodoClick` from `TodoList` and put it into `mapDispatchToProps`'s `return`. Note that we don't need the reference to `store` anymore, and can just change `store.dispatch()` to just `dispatch()`, which is provided as an argument to `mapDispatchToProps`.

```JavaScript
const mapDispatchToProps = (dispatch) => {
  return {
    onTodoClick: (id) => {
      dispatch({
        type: 'TOGGLE_TODO',
        id
      })
    }
  };
};
```

##### Review of what we just did...

We now have two functions:

A function `mapStateToProps` that maps the Redux store's state to the props of the `TodoList` component that are related to the data from the Redux store. These props
will be updated any time the state changes

We also created a function `mapDispatchToProps` that maps the store's `dispatch()` method and returns the props that use the dispatch method to dispatch actions. So it returns the callback props needed by the presentational component. It specifies the behavior of which callback prop dispatches which action.

##### The `connect()` function
Together, these two new functions describe a container component so well that instead of writing it we can generate it by using the `connect()` function provided by `react-redux`.

Now instead of creating a `VisibleTodoList` class, we can declare a variable and use the `connect()` method to obtain it. We'll pass in `mapStateToProps` as the first argument, and `mapDispatchToProps` as the second. As this is a [curried function](https://medium.com/@kbrainwave/currying-in-javascript-ce6da2d324fe#.ytohz3iob), we must call it again, passing in the presentational component that we want it to wrap and pass the props, thereby connecting to the redux store (in this case, `TodoList`).

The result of the connect call is the container component that is going to render the
presentational component. It will calculate the props to pass to the
presentational component by merging the objects returned from `mapStateToProps`,
`mapDispatchToProps`, and its own props

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

<p align="center">
<a href="./21-Passing_the_Store_Down_with_Provider_from_React_Redux.md"><- Prev</a>
<a href="./23-Generating_Containers_with_connect_from_React_Redux_AddTodo.md">Next -></a>
</p>
