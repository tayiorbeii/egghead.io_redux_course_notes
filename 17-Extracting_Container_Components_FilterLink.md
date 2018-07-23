# 22. Extracting Container Components (FilterLink)
[Video Link](https://egghead.io/lessons/javascript-redux-extracting-container-components-filterlink)

In the previous section, we separated presentational components from our main container component. `TodoApp` specifies the behaviors when buttons are clicked, items are added, and filters are applied. The individual presentational components, such as AddTodos, Footer, TodoList, etc don't dispatch actions, but instead call their callback functions in the props. Therefore, they are only responsible for the looks, not the behavior.

The downside of this approach is that lots of props must be passed down the tree even when intermediate components don't really use them.

For example, the `FilterLink` needs to know the current filter so it can change its appearance when it's active. However, in order for it to receive the current filter, it has to be passed down from the top. This is why `Footer` has to be given `visibilityFilter` so it can be passed to a `FilterLink`.

In a way this breaks encapsulation because the parent components need to know too much about what data the child components need. To fix this, we are going to extract some more container components.

#### Extracting the `Footer` Component

Currently the `Footer` component accepts the `visibilityFilter` and `onFilterClick()` callback as its props, but it _doesn't actually use either of them_. It just passes down to the `FilterLink`. This is a good opportunity for simplification by removing the props `visibilityFilter` and `onFilterClick()`. We can only do this because we know that the `Footer` component doesn't care about the values of its props, as they only exist to pass down to `FilterLink`.

We start by removing the props definition from the `Footer` component, and removing them from the `FilterLink`s as well.

```JavaScript
const Footer = () => (
  <p>
    Show:
    {' '}
    <FilterLink
      filter='SHOW_ALL'
    >
      All
    </FilterLink>
    {', '}
    <FilterLink
      filter='SHOW_ACTIVE'
    >
      Active
    </FilterLink>
    {', '}
    <FilterLink
      filter='SHOW_COMPLETED'
    >
      Completed
    </FilterLink>
  </p>
)
```

#### Refactoring `FilterLink`
Inside of the `FilterLink` definition, we don't currently specify behavior for clicking on the link. It also needs to know the current `filter` so it can render the item appropriately. Because of this, we can't say `FilterLink` is presentational, because it is inseparable from its behavior. The only reasonable behavior is to dispatch an action (`SET_VISIBILITY_FILTER`) upon clicking. This is an opportunity to split this into a more concise presentational component, with a wrapping container component to manage the logic, with the presentational component being used for rendering.

Therefore, we will start by converting our current `FilterLink` into a presentational component called `Link`.

The new `Link` presentational component doesn't know anything about the filter-- it only accepts the `active` prop, and calls its `onClick` handler. `Link` is only concerned with rendering.

```JavaScript
const Link = ({
  active,
  children,
  onClick
}) => {
  if (active) {
    return <span>{children}</span>
  }

  return (
    <a href='#'
      onClick={e => {
        e.preventDefault();
        onClick();
      }}
    >
      {children}
    </a>
  );
};
```

#### The New `FilterLink`
The new `FilterLink` will be a class that renders the `Link` with the current data from the `store`. It's going to read the component `props` and read the `state`. **Note:** this doesn't mean React's state, but instead the Redux store's state that it gets by calling `store.getState()`.

As a container component, `FilterLink` doesn't have its own markup, and it delegates rendering to the `Link` presentational component. In this case, it calculates its `active` prop by comparing its own `filter` prop with the `visibilityFilter` in the Redux store's `state`.

The `filter` prop is the one that is passed to the `FilterLink` from the `Footer`. The `visibilityFilter` corresponds to the current chosen visibility filter that is held in Redux store's `state`. If they match, we want the link to appear active.

The container component also needs to specify the behavior. In this case, the `FilterLink` specifies that when a particular `Link` is clicked, we should dispatch an action of the type `'SET_VISIBILITY_FILTER'` along with the `filter` value that we take from the props.

The `FilterLink` may accept children, which will be used as the contents of the `Link`. So we are going to pass the children down to the `Link` component, which is going to render them inside of the `<a>` tag.

```JavaScript
class FilterLink extends Component {
  render () {
    const props = this.props;
    // this just reads the store, is not listening
    // for change messages from the store updating
    const state = store.getState();

    return (
      <Link
        active={
          props.filter ===
          state.visibilityFilter
        }
        onClick={() =>
          store.dispatch({
            type: 'SET_VISIBILITY_FILTER',
            filter: props.filter
          })
        }
      >
        {props.children}
      </Link>
    );
  }
}
```

#### Problems with `FilterLink`
There is a small problem with this implementation of `FilterLink`. Inside the `render()` method it reads the current `state` of the Redux store, however _it does not subscribe_ to the store. So if the parent component doesn't update when the store is updated, the correct value won't be rendered.

Also, we currently re-render the entire application when the state changes, which isn't very efficient. In the future, we will move subscription to the store to the lifecycle methods of the container components.

React provides a special `forceUpdate()` method on the Component instances to force them to re-render. We can use it in combination with the `store.subscribe()` method so that any time the store changes we force the container component to update.

We can start by implementing this inside `FilterLink`:

```JavaScript
class FilterLink extends Component {
  componentDidMount() {
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }

  // Since the subscription happens in `componentDidMount`,
  // it's important to unsubscribe in `componentWillUnmount`.
  componentWillUnmount() {
    this.unsubscribe(); // return value of `store.subscribe()`
  }
.
. // `render()` method as above...
.
```

#### Recap
[5:54 in the video has a walkthrough of what we've done.](https://egghead.io/lessons/javascript-redux-extracting-container-components-filterlink)

<p align="center">
<a href="./16-Extracting_Presentational_Components_AddTodo__Footer__FilterLink.md"><- Prev</a>
<a href="./18-Extracting_Container_Components_VisibileTodoList__AddTodo.md">Next -></a>
</p>
