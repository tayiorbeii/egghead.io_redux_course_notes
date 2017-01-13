# 09. Avoiding Array Mutations
[Video Link](https://egghead.io/lessons/javascript-redux-avoiding-array-mutations-with-concat-slice-and-spread)

_Note: This code uses Expect and Deep-Freeze libraries for testing and mutation checking respectively._

Say we want to implement a counter list application. We will need to write a few functions to operate on its state, which is an array of numbers representing the individual counters.

```Javascript
const addCounter = (list) => {
  list.push(0);
  return list;
};

const testAddCounter = () => {
  const listBefore = [];
  const listAfter = [0];

  deepFreeze(listBefore);

  expect(
    addCounter(listBefore)
  ).toEqual(listAfter);
};

testAddCounter();
console.log('All tests passed')
```

As this code stands now, the test fails because we can't push 0 onto a frozen object.

Instead, we need to use **concat**, because it doesn't modify the original object:
```Javascript
const addCounter = (list) => {
  // return list.concat([0]); // old way
  return [...list, 0]; // ES6 way
};
```

In this application we also want to be able to remove counters:

```Javascript
const removeCounter = (list, index) => {
  list.splice(index, 1);
  return list;
}
.
.
.
const testRemoveCounter = () => {
  const listBefore = [0, 10, 20];
  const listAfter = [0, 20];

  expect (
    removeCounter(listBefore, 1)
  ).toEqual(listAfter);
};
```
This works, but `splice` is also a mutating method.
We need to use `slice` instead:

```Javascript
const removeCounter = (list, index) => {
  // Old way:
  //return list
  //  .slice(0, index)
  //  .concat(list.slice(index + 1));

  // ES6 way:
  return [
    ...list.slice(0, index),
    ...list.slice(index + 1)
  ];
};
```

Now let's implement incrementing the counter. The function will take in the array and the index of the counter that we are incrementing.
```Javascript
const incrementCounter = (list, index) => {
  list[index]++;
  return list;
};

const testIncrementCounter = () => {
  const listBefore = [0, 10, 20];
  const listAfter = [0, 11, 20];

  deepFreeze(listBefore);

  expect(
    incrementCounter(listBefore, 1)
  ).toEqual(listAfter);
};
```

This fails because we are mutating. The correct approach is similar to how we removed an item-- we will slice up to the item we want to increment, concat with a single item that we have incremented, then concat the rest of the original array.
```Javascript
const incrementCounter = (list, index) => {
  // Old way:
  // return list
  //  .slice(0, index)
  //  .concat([list[index] + 1])
  //  .concat(list.slice(index + 1));

  // ES6 way:
  return [
    ...list.slice(0, index),
    list[index] + 1,
    ...list.slice(index + 1)
  ];
};
```

<p align="center">
<a href="./04-React_Counter_Example.md"><- Prev</a>
<a href="./06-Avoiding_Object_Mutations.md">Next -></a>
</p>
