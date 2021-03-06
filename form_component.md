# Form Component

## `<Form>...</Form>`

The `<Form>` component is simply a decorated `<form>` component with a few helpful props. It is useful for blocking `onSubmit` handlers when the form is invalid. Validation is specified by the `validators` and/or `errors` prop.

```js
import { Form, Field } from 'react-redux-form';
import { isEmail, isNull } from 'validator';

const required = isNull;

const passwordsMatch = ({ password, confirmPassword }) => {
  return password === confirmPassword;
};

// in render() return block:
<Form model="user"
  validators={{
    '': { passwordsMatch },
    email: { required, isEmail },
    password: { required },
    confirmPassword: { required }
  }}
  onSubmit={...}>
  <Field model="user.email">
    <input type="email">
  </Field>
  
  <Field model="user.password">
    <input type="password">
  </Field>
  
  <Field model="user.confirmPassword">
    <input type="password">
  </Field>
</Form>
```

# Properties

## `model` prop
(Required) The string representing the model of the form in the store.

Typically, the `<Field>` components nested inside `<Form>` would be _members_ of the form model; e.g. `user.email` and `user.password` are members of the `user` model.

## `validators` prop
An object representing the validators for the fields inside the form, where:

- the **keys** are the field model names (e.g. `'email'` for `user.email`)
- the **values** are validator(s) for the field model. They can be:
  - a validator function, which receives the field model value, or
  - a validator object, with validation keys and validator functions as values, also receiving the field model value.

If the key is the empty string (`'': {...}`), then the validator will belong to the form model itself.

Validation will occur on _any field model change_ by default, and only the validators for the fields that have changed will be run (as a performance enhancement)!

**Tips**
- Specifying validators on the form is usually sufficient - you don't need to put validators on the `<Field>` for most use cases.
- If you need validators to run on submit, this is the place to put them.

## `validateOn` prop
A string that indicates when `validators` or `errors` (for error validators) should run.

By default, validators will only run whenever a field changes, and
- only for the field that has changed, and
- always for any form-wide validators.

The possible values are:
- `"change"` (Default): run validation whenever a field model value changes
- `"submit"`: run validation only when submitting the form.

**Tips**
- Keep in mind, validation will always run initially, when the form is loaded.
- If you want better performance, you can use `validateOn="submit"`, depending on your use-case.

## `onSubmit` prop
The handler function called when the form is submitted. This works almost exactly like a normal `<form onSubmit={...}>` handler, with a few differences:

- The submit event's default action is prevented by default, using `event.preventDefault()`.
- The `onSubmit` handler _will not execute_ if the form is invalid.
- The `onSubmit` handler receives the form model data, not the event.

**Example**
```js
import React from 'react';
import { connect } from 'react-redux';
import { Form, Field, actions } from 'react-redux-form';

class MyForm extends React.Component {
  handleSubmit(user) {
    const { dispatch } = this.props;
    let userPromise = fetch('...', {
      method: 'post',
      body: user
    })
    .then((res) => res.json())
    .then((res) => {
      // ...
    });
    
    dispatch(actions.submit('user', userPromise));
  }
  
  render() {
    return (
      <Form model="user"
        validators={{...}}
        onSubmit={ (user) => this.handleSubmit(user) }>
        <Field model="user.email">
          <input type="email" />
        </Field>
      </Form>
    );
  }
}

export default connect(s => s)(MyForm);
```
- Here, `handleSubmit()` will _not_ be called if any of the `validators` (or `errors`, if specified) are not valid.
- `handleSubmit(user)` receives the `user` model value, since `model="user"` on the `<Form>` component.

**Tips**
- You can do anything in `onSubmit`; including firing off custom actions or handling (async) validation yourself.

## `component` prop
  