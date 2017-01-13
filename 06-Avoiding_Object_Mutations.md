# 10. Avoiding Object Mutations with `Object.assign()` and `...spread`
[Video Link](https://egghead.io/lessons/javascript-redux-avoiding-object-mutations-with-object-assign-and-spread)

Like the previous example, this code uses the Expect and DeepFreeze libraries.

We are going to test a function `toggleTodo()` that takes a Todo item and toggles its "completed" field.

```Javascript
const toggleTodo = (todo) => {
  // Mutated version:
  todo.completed = !todo.completed
  return todo;
}

const testToggleTodo = () => {
  const todoBefore = {
    id: 0,
    text: 'Learn Redux',
    completed: false
  };
  const todoAfter = {
    id: 0,
    text: 'Learn Redux',
    completed: true
  };

  deepFreeze(todoBefore);

  expect(
    toggleTodo(todoBefore)
  ).toEqual(todoAfter);
};

testToggleTodo();
console.log('All tests passed.');
```

One way to do this without mutation is to copy the object with the "completed" value flipped:
```Javascript
const toggleTodo = (todo) => {
  return {
      id: todo.id,
      text: todo.text,
      completed: !todo.completed
  };
}
```

However, if we add new properties later, we may forget to update this piece of code. This is why we should use ES6's `Object.assign()`. This lets you assign properties of several objects onto the target object.

```Javascript
const toggleTodo = (todo) => {
  return Object.assign({}, todo, {
    completed: !todo.completed
  });
};
```
_Note how the argument order to `Object.assign()` corresponds to the JavaScript assignment operator order._

The left argument is the one whose properties are going to be assigned, so we pass in an empty object (`{}`) because it will be mutated (remember, we don't want to mutate any existing data).

Every further argument to `Object.assign()` is considered a source object whose properties will be copied to the target (in this case, the target is our blank object provided as the first argument).

If there are multiple occurrences of the same property/properties, the last occurrence "wins". In this case, the `{ completed: !todo.completed }` we specify in the third overwrites the `completed` contained within the `todo` in the second argument.

_Remember that ES6 need to be transpiled (at least for the time being)..._

Another option to do the same thing is with the `spread` operator proposed for ES7:
```Javascript
const toggleTodo = (todo) => {
  return {
    ...todo,
      completed: !todo.completed
  };
};
```


<p align="center">
<a href="./05-Avoiding_Array_Mutations.md"><- Prev</a>
<a href="./07-Writing_a_Todo_List_Reducer.md">Next -></a>
</p>
