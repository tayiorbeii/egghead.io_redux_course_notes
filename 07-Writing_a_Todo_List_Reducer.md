# 11. Writing a Todo List Reducer (Adding a Todo)
[Video Link](https://egghead.io/lessons/javascript-redux-writing-a-todo-list-reducer-adding-a-todo)

We are again using DeepFreeze and Expect.

In this section we are going to write a reducer for our Todo List application.

```JavaScript
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ];
    default:
      return state;
  }
};

const testAddTodo = () => {
  const stateBefore = [];
  const action = {
      type: 'ADD_TODO',
      id: 0,
      text: 'Learn Redux'
  };
  const stateAfter = [{
      id: 0,
      text: 'Learn Redux',
      completed: false
  }];

  deepFreeze(stateBefore);
  deepFreeze(action);

  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testAddTodo();
console.log('All tests passed')
```

#### Data Flow:
First, we create an empty state array (`stateBefore`) and our `action` object inside the test function.

Next, they are passed into our `todos` reducer function, which notices that our action type of `'ADD_TODO'` is recognized.

The reducer returns a new array containing the same items as the old array, as well as a new item representing the Todo we just added. Note that since we passed in an empty array (`stateBefore = []`) we are returned a single item array.

Finally, our new array is compared with our expected array with our single Todo item.


# 12. Writing a Todo List Reducer (Toggling a Todo)
[Video Link](https://egghead.io/lessons/javascript-redux-writing-a-todo-list-reducer-toggling-a-todo)

Now we will handle toggling Todos by adding to our above code.

```JavaScript
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      // ... ADD_TODO logic as above
    case 'TOGGLE_TODO':
      return state.map(todo => {
        if (todo.id !== action.id) {
          return todo;
        } else {
// for the todo that matches the action id return all other information the same
// but change the completed property to the opposite of what it was previously
          return {
            ...todo,
            completed: !todo.completed
          };
        }
      });
    default:
      return state;
  }
};

const testToggleTodo = () => {
  const stateBefore = [
    {
      id: 0,
      text: 'Learn Redux',
      completed: false
    },
    {
      id: 1,
      text: 'Go Shopping',
      completed: false
    }
  ];
  const action = {
      type: 'TOGGLE_TODO',
      id: 1
  };

  const stateAfter = [
    {
      id: 0,
      text: 'Learn Redux',
      completed: false
    },
    {
      id: 1,
      text: 'Go Shopping',
      completed: true
    }
  ];

  deepFreeze(stateBefore);
  deepFreeze(action);

  expect(
    todos(stateBefore, action)
  ).toEqual(stateAfter);
};

testAddTodo();
testToggleTodo();
console.log('All tests passed.');
```

Note that inside our `'TOGGLE_TODO'` reducer that we return the existing todo item if the `id` doesn't match the `id` of the todo we are toggling. If the `id` does match, we use the spread operator to return a new object with all the properties of the existing `todo` object, along with an inverted `completed` value.


<p align="center">
<a href="./06-Avoiding_Object_Mutations.md"><- Prev</a>
<a href="./08-Reducer_Composition_with_Arrays.md">Next -></a>
</p>
