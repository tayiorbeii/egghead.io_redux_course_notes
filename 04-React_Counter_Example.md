# 08. React Counter Example
[Video Link](https://egghead.io/lessons/javascript-redux-react-counter-example)

In the simple example, we were updating the document body every time data in the store's state changed. Of course, this doesn't scale. So we'll use React.

_Note that React has been included from CDN in the video example. We will also be rendering to `<div id='root'></div>`._

With React, we refactor our code a bit. We create a `Counter` component and render it to the `root div`.

Note that `store.getState()` is passed as a prop to the `Counter` element as the render function will be called
any time the store's state changes.

This also allows the Counter to be refactored to a simple function (supported in react 0.14)

```Javascript
// ... store created with `counter` reducer ...

const Counter = ({ value }) => (
  <h1>{value}</h1>
);

const render = () => {
  ReactDOM.render(
    <Counter value={store.getState()}/>,
    document.getElementById('root')
  );
};

store.subscribe(render);
render();
```

We can add "Increment" and "Decrement" buttons to the `Counter` without re-introducing the Redux dependency,
so the onIncrement/onDecrement callbacks may instead be passed as props to the button(s).

```Javascript
const Counter = ({
  value,
  onIncrement,
  onDecrement
}) => (
  <div>
    <h1>{value}</h1>
    <button onClick={onIncrement}>+</button>
    <button onClick={onDecrement}>-</button>
  </div>
);

const render = () => {
  ReactDOM.render(
    <Counter
      value={store.getState()}
      onIncrement={() =>
        store.dispatch({
          type: 'INCREMENT'
        })
      }
      onDecrement={() =>
        store.dispatch({
          type: 'DECREMENT'
        })
      }
    />,
    document.getElementById('root')
  );
}
```


The `Counter` component is a "dumb" component. It doesn't contain any business logic. A dumb component only specifies how the current state is rendered into output, and how the callbacks passed via props are bound to the event handlers.

#### To recap how it works...

When the `Counter` is rendered, we specify that its value should be taken from the Redux store's current state. When the user presses a button, the corresponding action is dispatched to the Redux store.

The reducer specifies how the next state is calculated based on the current state and the action being dispatched.

Finally, we subscribe to the Redux store so our `render()` function runs any time the state changes so our `Counter` gets the current state.

<p align="center">
<a href="./03-Implementing_Store_from_Scratch.md"><- Prev</a>
<a href="./05-Avoiding_Array_Mutations.md">Next -></a>
</p>
