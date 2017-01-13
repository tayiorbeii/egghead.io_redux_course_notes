# 29. Generating Containers with `connect()` from React Redux (`FooterLink`)
[Video Link](https://egghead.io/lessons/javascript-redux-generating-containers-with-connect-from-react-redux-footerlink)

Now let's use `connect()` on our `FooterLink` component.

Recall that our `FilterLink` component renders a `Link` with an `active` prop and an `onClick` handler.

#### `mapStateToProps`

We'll start by writing our `mapStateToProps` function which we will call `mapStateToLinkProps` because everything is in a single file, remember? It will accept the state of the Redux store, and return the props that should be passed to the `Link` component.

The only prop in `Link` is `active`, which determines the styling based on the `visibilityFilter.` We remove the definition from `Link`'s `active` prop, and move it into `mapStateToLinkProps`.

```JavaScript
const mapStateToLinkProps = (
  state
) => {
  return {
    active:
      props.filter ===
      state.visibilityFilter
  }
};
```
Notice that `active` now references the `filter` prop of the `FilterLink` component. In order to tell if a `Link` is active or not, we need to compare this prop with the `visibilityFilter` in the Redux store's state.

It's common to use the container props when calculating the child props, so we pass them in as a second argument to `mapStateToProps`. In this case, we'll rename it to `ownProps` to make it more clear that we are talking about the container component's _own_ props, and not the props that are passed to the child, which is the return value of `mapStateToProps`.

```JavaScript
const mapStateToLinkProps = (
  state,
  ownProps
) => {
  return {
    active:
      ownProps.filter ===
      state.visibilityFilter
  }
};
```

#### `mapDispatchToProps`
Again, we will rename this function to `mapDispatchToLinkProps` to avoid name collisions.

Initially we know our first argument is the `dispatch()` function. To see what other arguments we need, we will to look at the container component to see what props depend on the `dispatch` function.

In this case, we only have the `onClick()` where we dispatch the action of type `'SET_VISIBILITY_FILTER'` along with the `filter` type. Since there is another reference to `props`, we will add `ownProps` as our second argument to `mapDispatchToLinkProps`.

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


#### `connect()` it Up

```JavaScript
const FilterLink = connect(
  mapStateToLinkProps,
  mapDispatchToLinkProps
)(Link);
```

Now that we've used `react-redux`'s `connect()`, we can remove our old `FilterLink` implementation, including the `contextTypes`.

[2:32 has the recap.](https://egghead.io/lessons/javascript-redux-generating-containers-with-connect-from-react-redux-footerlink)

<p align="center">
<a href="./23-Generating_Containers_with_connect_from_React_Redux_AddTodo.md"><- Prev</a>
<a href="./25-Extracting_Action_Creators.md">Next -></a>
</p>
