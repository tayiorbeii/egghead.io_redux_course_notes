# 26. Passing the Store Down with `<Provider>` from React Redux
[Video Link](https://egghead.io/lessons/javascript-redux-passing-the-store-down-with-provider-from-react-redux)

In the last section, we implemented a `Provider` component to pass `store` implicitly with React's `context` feature. It was really convenient.

In fact, it was so convenient that we don't need to write `Provider` ourselves-- we can import it from the `react-redux` library that gives React bindings to the Redux library.

Start by importing `Provider` from `'react-redux'`

```JavaScript
// CDN style
const { Provider } = ReactRedux;
// npm style
import { Provider } from 'react-redux';
```

Just like the `Provider` we wrote before, the `Provider` that comes with `react-redux` exposes the `store` as a prop on the context. 







