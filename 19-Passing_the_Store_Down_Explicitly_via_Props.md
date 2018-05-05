# 24. Passing the Store Down Explicitly via Props

[Video Link](https://egghead.io/lessons/javascript-redux-passing-the-store-down-explicitly-via-props)

So far we've been creating a variable called `store` by passing our combined `todoApp` reducers into Redux's `createStore()` function:

```JavaScript
const { combineReducers, createStore } = Redux;

const todoApp = combineReducers({
  todos,
  visibilityFilter
});

const store = createStore(todoApp);
```

Once the store has been created, our container components get data from it by calling `store.getState()`, subscribe to changes by calling `store.subscribe()`, and dispatch actions by calling `store.dispatch()`.

Having all of our code in a single file works well for a simple example, but it doesn't scale very well.

First, it makes our components harder to test because they'll reference a specific store, but we may want to provide a different mock store in the tests.

Second, it makes it hard to implement Universal applications that are rendered on the server, because on the server we'll want to supply a different `store` instance for every request, because different requests have different data.

To start fixing this, we'll move the code related to creating the store to the bottom of the file, and pass the store we create as a prop to `TodoApp`.

###### Before:
```JavaScript
const store = createStore(todoApp);

ReactDOM.render(
  <TodoApp />,
  document.getElementById('root')
);
```

##### After:
```JavaScript
ReactDOM.render(
  <TodoApp store={createStore(todoApp)} />,
  document.getElementById('root')
);
```
The `store` is now injected into `TodoApp`.


#### Refactoring `TodoApp` to accept `store`
Every container component needs a reference to `store`, and unfortunately the only way to make this happen (for now!) is by passing it down to every component as a prop. However, this is less effort than passing different data to every component, but not as good as what we'll have later.


```JavaScript
const TodoApp = ({ store }) => (
  <div>
    <AddTodo store={store} />
    <VisibleTodoList store={store} />
    <Footer store={store} />
  </div>
);
```

The problem with doing it this way is that the container components need to have the `store` instance to get `state` from it, dispatch actions, and subscribe to changes.

Now inside of each of the components inside of `TodoApp` we need to adjust our container components to take the `store` from the `props` in both `componentDidMount()`, and `render()`:

```JavaScript
class VisibleTodoList extends Component {
  componentDidMount() {
    const { store } = this.props;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }
  .
  . // Rest of component as before to `render()` method
  .

  render() {
    const props = this.props;
    const { store } = props;
    const state = store.getState();
    .
    .
    .
  }
}
```

```JavaScript
const AddTodo = ({ store }) => {
  .
  . // Rest of component as before
  .
}
```

Though `Footer` itself does not need the store, we must give it to `Footer` so we
can pass it to `FilterLink`

```JavaScript
const Footer = ({ store }) => (
  <p>
    <FilterLink
      filter='SHOW_ALL'
      store={store}
    >
      All
    </FilterLink>
    .
    . // Follow this pattern for the other `FilterLink` component references
    .
)
```

```JavaScript
class FilterLink extends Component {
  componentDidMount() {
    const { store } = this.props;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }
  .
  . // Rest of component as before down to `render()`
  .
  render() {
    const props = this.props;
    const { store } = props
    const state = store.getState()
    .
    . // Rest of component as before
    .
  }
}
```

Now all of our components are receiving `state` via their props instead of relying on a top level variable.

Note that this change did not change the behavior of the data flow of the application. The containers subscribe to `store` and update like they did before. What changed is _how_ they get the store.

Soon we will see how to pass `store` to the container components implicitly, but for now, this is where we're at.

<p align="center">
<a href="./18-Extracting_Container_Components_VisibileTodoList__AddTodo.md"><- Prev</a>
<a href="./20-Passing_the_Store_Down_Implicitly_via_Context.md">Next -></a>
</p>
