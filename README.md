<h1 id="react-redux-form"><span>react</span> <span>redux</span> <span>form</span></h1>

<iframe src="https://ghbtns.com/github-btn.html?user=davidkpiano&repo=react-redux-form&type=star&count=true" frameborder="0" scrolling="0" width="100px" height="20px"></iframe>
<iframe src="https://ghbtns.com/github-btn.html?user=davidkpiano&repo=react-redux-form&type=fork&count=true" frameborder="0" scrolling="0" width="100px" height="20px"></iframe>

React Redux Form is **a collection of action creators and reducer creators** that makes building complex and custom forms with React and Redux simple.

**If you know React and Redux, you know React-Redux-Form.**

It also provides the helpful `<Field model="..." />` component for mapping controls to form and model changes quickly.

Instead of answering "How would I do this with this library?", React Redux Form answers "How would I do this in Redux?", and provides a thin layer to help abstract the pain points of creating complex, dynamic forms with Redux and React.


```js
import { Field } from 'react-redux-form';

// in your component's render() method
<Field model="user.name">
  <input type="text" />
</Field>
```

React Redux Form:

- handles model value changes for _any_ object/array
- provides utility actions for manipulating state
- handles control updates, such as focus, blur, pristine, etc.
- keeps track of validity on _any part_ of your model
- allows for completely dynamic and deep forms
- **keeps your model state intact**, which allows you to have full control of your model reducer

**Getting Started**

1. Install the prerequisites:
  - `npm install react redux react-redux --save`
  - (recommended) `npm install redux-thunk --save`
1. `npm install react-redux-form --save`

**Full Example**

```js
// app.js

import React from 'react';
import { combineReducers, createStore } from 'redux';
import { Provider } from 'react-redux';
import { modelReducer, formReducer } from 'react-redux-form';

import LoginForm from './forms/login-form';

const store = createStore(combineReducers({
  user: modelReducer('user'),
  userForm: formReducer('user')
}));

export default class App extends React.Component {
  render() {
    return (
      <Provider store={ store }>
        <LoginForm />
      </Provider>
    )
  }
}
```

```js
// forms/login-form.js

import React from 'react';
import { connect } from 'react-redux';
import { Field } from 'react-redux-form';

class LoginForm extends React.Component {
  render() {
    let { user } = this.props;

    return (
      <form>
        <Field model="user.username">
          <label>Username</label>
          <input type="text" />
        </Field>

        <Field model="user.password">
          <label>Password</label>
          <input type="password" />
        </Field>

        <button>
          Log in as { user.username }
        </button>
      </form>
    )
  }
}

const selector = (state) => ({ user: state.user });

export default connect(selector)(LoginForm);
```