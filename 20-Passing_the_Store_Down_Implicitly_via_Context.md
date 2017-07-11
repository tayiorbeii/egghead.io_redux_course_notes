# 25. Passing the Store Down Implicitly via Context
[Video Link](https://egghead.io/lessons/javascript-redux-passing-the-store-down-implicitly-via-context)

In the last section we refactored our code to pass `store` around as a prop. This requires a lot of boilerplate code.

However, there is an easier way: use React's context feature.

To illustrate, we'll start by creating a new `Provider` component. From its `render()` method, it just returns what its child is. This means we can wrap any component in a `Provider`, and it's going to render that component.

```JavaScript
class Provider extends Component {
  render() {
    return this.props.children;
  }
}
```

Now we need to change our `ReactDOM.render()` call to render `TodoApp` inside of our new `Provider`. We also now pass `state` as a prop to `Provider` instead of `TodoApp`.

```JavaScript
ReactDOM.render(
  <Provider store={createStore(todoApp)}>
    <TodoApp />
  </Provider>,
  document.getElementById('root')
);
```
Now we will update our `Provider` component to use React's context feature. This is what makes the store available to any component that it renders. Now the `store` will be available to any of the children and grandchildren of the components `Provider` renders (in our example, this is `TodoApp` and all of the other components and containers we've built inside of it!)

There is an important condition that must be added to make it work. We have to provide `childContextTypes` on the component that provides child context. This is similar to React's `PropTypes` definition that helps you when writing your app, except that in this case it is *required* in order for the child components to receive context.

```JavaScript
class Provider extends Component {
  getChildContext() {
    return {
      store: this.props.store // This corresponds to the `store` passed in as a prop
    };
  }
  render() {
    return this.props.children;
  }
}

Provider.childContextTypes = {
  store: React.PropTypes.object
}
```

#### Refactor Components to get `store` from `context` instead of `props`
In each of our components, we need to change how `state` is received.


```JavaScript
class VisibleTodoList extends Component {
  componentDidMount() {
    const { store } = this.context;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }
  .
  . // Rest of component as before to `render()` method
  .

  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();
    .
    .
    .
  }
}
```
**Note:** The `context` is opt-in for all components, so we have to specify `contextTypes`. If you don't specify this, the component won't receive the relevant context, so it is essential to declare them!

```JavaScript
VisibleTodoList.contextTypes = {
  store: React.PropTypes.object
}
```

#### Updating our Functional Components for `context`
Our functional components don't have `this`, so how will we give them `context`?

It turns out that they receive context as a second argument (after `props`). We also need to add `contextTypes` for the component that specifies which context we want to receive (in this case `store` from `Provider`). Context can be passed down to any level, so you can
think of it as creating a 'wormhole' to whatever component that uses it, however, you
must remember to opt-in by declaring the contextTypes

```JavaScript
const AddTodo = (props, { store }) => {
  .
  . // Rest of component as before
  .
}

AddTodo.contextTypes = {
  store: React.PropTypes.object
}
```

We must also convert `FilterLink` to accept the store from context and
provide the contextTypes

##### Updating `FilterLink`
```JavaScript
class FilterLink extends Component {
  componentDidMount() {
    const { store } = this.context;
    this.unsubscribe = store.subscribe(() =>
      this.forceUpdate()
    );
  }
  .
  . // Rest of component as before down to `render()`
  .
  render() {
    const props = this.props;
    const { store } = this.context;
    const state = store.getState();
    .
    . // Rest of component as before
    .
  }
}
FilterLink.contextTypes = {
  store: React.PropTypes.object
}
```

**Note:**  `Footer` and Individual `<FilterLink>` elements don't need `store` as a prop anymore as we don't need to pass it down, so we can remove that from the props:

```JavaScript
const Footer = () => {
  <p>
    <FilterLink
      filter='SHOW_ALL'
    >
      All
    </FilterLink>
    .
    . // Follow this pattern for the other `FilterLink` component references
    .
}
```

We can also get rid of the store prop for `TodoApp` because it no longer needs to be passed down to the containers.

```JavaScript
const TodoApp = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
)
```

#### Recap

Now we are implictly passing `store` down via context instead of having to explicitly do it via props.

[4:43 in the video link has the recap of what we just did.](https://egghead.io/lessons/javascript-redux-passing-the-store-down-implicitly-via-context)


Context is a powerful feature, but in a way it contradicts the React philosophy of having an explicit data flow. The context essentially allows global variables across the component tree. Global variables are usually a bad idea, and you shouldn't use the context feature in this way. You should only use context if you're using it for dependency injection (like in our case where we need to make a single object available to all components).

Finally, it is important to note that the `context` API is not stable in React. It has changed before, and it is likely to change again, so it is probably best to not rely on it too much.

<p align="center">
<a href="./19-Passing_the_Store_Down_Explicitly_via_Props.md"><- Prev</a>
<a href="./21-Passing_the_Store_Down_with_Provider_from_React_Redux.md">Next -></a>
</p>
