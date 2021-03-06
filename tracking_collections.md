# Tracking Collections

Let's say you have a collection of data in a model that looks like this:

```js
const goats = [
  { id: 101, name: 'Australian Dwarf Goat' },
  { id: 202, name: 'Booted Goat' },
  { id: 303, name: 'Pygmy Goat' }
];
```

and you want to do any of the following:
- **Insert** an item into the collection at any index
- **Delete** an item from the collection
- **Sort** the collection
- **Shuffle** the items in the collection
- ...etc.

A naïve approach to creating fields for each item is by index:

```js
// in connected component's render()
const { goats } = this.props;

return (
  <div>
  { goats.map((goat, index) =>
    <Field model={`goats[${index}].name`} key={index}>
      <input type="text" />
    </Field>
  }
  </div>
);
```

However, if we do any of the above actions that might change the index of an item, despite that item remaining the same, you _will_ get unexpected results.

For example, if `goats[2].name === 'Pygmy Goat'` and we `dispatch(actions.remove('goats', 1))`, then `goats[2].name === 'Booted Goat'`, which is not a desirable result.

So what do we do?

## Model Getters

Model getter functions answer the question, "What is the path to this entity?" especially if the path may change over time. It a function that:

- takes one argument: `state` _(Object)_, which is the entire state tree of the store
- and returns: `model` _(String)_ - the string representation of the path to the model.

Here is an example model function that will always return the path to the Pygmy Goat (with an ID of 303):

```js
function pygmyGoatNameModel(state) {
  const goatIndex = state.goats.findIndex((goat) => {
    // hardcoded index; let's fix this
    return goat.id === 303;
  });
  
  return `goats[${goatIndex}].name`;
}
```

And you can use this model getter in any component that takes a `model={...}` prop:

```js
<Field model={pygmyGoatNameModel}>
  <input type="text" />
</Field>
```

That way, the model will always point to the correct entity (goat) even if the array indexes change.

However, this is a bit verbose. There's a simpler way of creating model getters that track model paths in collections...

## The `track()` Function

You can create model getters using the `track()` function, which takes two arguments:

- `model` _(String)_ - the model that represents the collection and property you want to track
- `predicate` _(Function | Any)_ - a function or [Lodash iteratee](https://lodash.com/docs#iteratee) that finds the correct model to track.

For example, I can track the pygmy goat name by ID like so:

```js
// With a predicate function
<Field model={track('goats[].name', (goat) => goat.id === 303)}>
  <input type="text" />
</Field>

// With a Lodash iteratee (shortand)
<Field model={track('goats[].name', { id: 303 })}>
  <input type="text" />
</Field>
```

The `track()` function was released in React Redux Form v0.10: https://github.com/davidkpiano/react-redux-form/releases/tag/v0.10.0
