
# REACT
[http://www.developer-cheatsheets.com/react](http://www.developer-cheatsheets.com/react)

## create-react-app

```js
$ create-react-app YOUR_APP_NAME
```

## Stateless component

```js
import React from 'react'
	
const YourComponent = () => <div>aaa</div>
	
export default YourComponent
```

## Class component

```js
import React from 'react'

class YourComponent extends React.Component {
  render() {
    return <div>aaa</div>
  }
}

export default YourComponent
```

## Properties in stateless component

```js
const YourComponent = ({ propExample1, example2 }) => (
  <div>
    <h1>properties from parent component:</h1>
    <ul>
      <li>{propExample1}</li>
      <li>{example2}</li>
    </ul>
  </div>
)

// <YourComponent propExample1="aaa" example2="bbb" />
```

## Properties in class component

```js
class YourComponent extends React.Component {
  render() {
    return (
      <div>
        <h1>
          properties from parent component:
        </h1>
        <ul>
          <li>{this.props.propExample1}</li>
          <li>{this.props.example2}</li>
        </ul>
      </div>
    )
  }
}
```

## Children

```js
const Component1 = (props) => (
  <div>{props.children}</div>
)

const Component2 = () => (
  <Component1>
    <h1>Component 1</h1>
  </Component1>
)
```

## Architecture

```js
(UI) >>> In event handlers update the state >>> (STATE)

(UI) <<< React detects state change and re-renders the relevant components <<< (STATE)
```

## State

```js
class CountClicks extends React.Component {
  state = {
    clicks: 0
  }

  onButtonClick = () => {
    this.setState(prevState => ({
      clicks: prevState.clicks + 1
    }))
  }

  render() {
    return (
      <div>
        <button onClick={this.onButtonClick}>
          Click me
        </button>
        <span>
          The button clicked 
          {this.state.clicks} times.
        </span>
      </div>
    )
  }
}
```

## useRef hook

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` points to the mounted text input element
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
// Author: Jonathan Gilyadov
```

## useState hook

```js
import React, { useState } from 'react'

function Example() {
  const [count, setCount] = useState(0)

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click here to increase the counter.
      </button>
    </div>
  )
}
```

## Ref

```js
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focus();
  }

  render() {
    return (
      <input ref={this.textInput} />
    );
  }
}
```

## Higher Order Component

```js
import React from 'react'
import Loading from '../components/Loading'

const withLoading = WrappedComponent => {
  return (props = {}) => {
    if (props.isLoading) {
      return <Loading />
    }
    return <WrappedComponent {...props} />
  }
}

export default withLoading

// --------

const MyComponent = ({}) => <div /> // ...
const WithLoadingComponent = withLoading(MyComponent)
```

## Render Props

```js
import React from 'react'

const MOBILE_VIEW_WIDTH_THRESHOLD = 600

class MediaQuery {
  state = {
    shouldShowMobileView: false
  }

  componentDidMount() {
    addEventListener('resize', this.updateResizeStatus)
  }

  componentWillUnmount() {
    removeEventListener('resize', this.updateResizeStatus)
  }

  updateResizeStatus() {
    if (screen.width <= MOBILE_VIEW_WIDTH_THRESHOLD) {
      this.setState({
        shouldShowMobileView: true
      })
    }
  }

  render() {
    return this.props.children(
      this.state.shouldShowMobileView
    )
  }
}

export default MediaQuery

// --------

import React from 'react'
import MobileView from '../components/MobileView'
import DefaultView from '../components/DefaultView'

const Screen = () => (
  <MediaQuery>
    {shouldShowMobileView =>
      shouldShowMobileView ? (
        <MobileView />
      ) : (
        <DefaultView />
      )
    }
  </MediaQuery>
)
```

------

# REDUX
[Redux Cheat Sheet](http://www.developer-cheatsheets.com/redux)

## action types

```js
const ADD_TODO = 'ADD_TODO'
const REMOVE_TODO = 'REMOVE_TODO'
const UPDATE_TODO = 'UPDATE_TODO'
```

## action creators

```js
const addTodo = (text) => ({
  type: ADD_TODO,
  text
})
const removeTodo = (id) => ({
  type: REMOVE_TODO,
  id
})
const updateTodo = (id, text) => ({
  type: UPDATE_TODO
  id, 
  text
})
```

## reducers

```js
const initialState = {
  todos: []
}

function todosReducer(state = initialState, action) {
  switch (action.type) {
    case UPDATE_TODO:
      const newState = deepClone(state)
      const todo = newState.todos.find(
        todo => todo.id === action.id
      )
      todo.text = action.text
      return newState
  }
}

function deepClone(obj) {
  return JSON.parse(JSON.stringify(obj))
}
```

## store

```js
import { 
  createStore, 
  combineReducers 
} from 'redux'
import todos from './todosReducer'
import counter from './counterReducer'

const rootReducer = combineReducers({ 
  todos,
  counter
})

const store = createStore(rootReducer)

export default store
```

## react-redux provider


```js
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'
​
const store = createStore(todoApp)
​
render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```js


## react-redux connect

```js
import { connect } from 'react-redux'
​
YourComponent = connect(
  mapStateToProps,
  mapDispatchToProps
)(YourComponent)
​
export default YourComponent
```

## redux middleware

```js
const logger = store => next => action => {
  console.log('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  return result
}

// -----------

import { 
  createStore, 
  applyMiddleware 
} from 'redux'
​
const store = createStore(
  rootReducer,
  applyMiddleware(logger)
)
```

## advanced reducers

```js
// Use this as a helper function
export const createReducer = (initialState, actionsMap) => (
  state = initialState,
  action
) => {
  const reduceFn = actionsMap[action.type]
  if (reduceFn) {
    return reduceFn(state, action)
  }
  return state
}

// In your reducer file
const actionMap = {
  [actionTypes.increment]: (state, action) => {
    return {
      ...state,
      count: state.count + 1
    }
  },
  [actionTypes.decrement]: (state, action) => {
    return {
      ...state,
      count: state.count - 1
    }
  },
  [actionTypes.setCount]: (state, action) => {
    return {
      ...state,
      count: action.count
    }
  }
}

const initialState = {
  count: 0
}

const reducer = createReducer(initialState, actionsMap)
//Author: Layton Gilbraith
```

-----

# REACT ROUTER

[React Router Cheat Sheet](http://www.developer-cheatsheets.com/react-router)

## install dependency

```js
yarn add react-router-dom 

//or: npm i --save react-router-dom
```

## Hello World!

```js
import { 
  BrowserRouter, 
  Route 
} from 'react-router-dom'

const Hello = () => <h1>Hello world!</h1>

const App = () => (
  <BrowserRouter>
    <div>
      <Route path="/hello" component={Hello}/>
    </div>
  </BrowserRouter>
)

// open: http://localhost:3000/hello
```

## Switch

```js
//Renders the first <Route> that matches the location.

import  { 
  BrowserRouter, 
  Switch, 
  Route 
} from 'react-router'

const YourComponent = () => (
  <BrowserRouter>
    <Switch>
      <Route exact path="/" component={Home}/>
      <Route path="/about" component={About}/>
      <Route path="/:user" component={User}/>
      <Route component={NoMatch}/>
    </Switch>
  </BrowserRouter>
);

export default YourComponent
```

## Link

```js
import { Link } from 'react-router-dom'

const Links = ({ match }) => (
  <nav>
    <Link to="/">Home</Link>
    <Link to={{ pathname: '/dashboard' }}>
      Dashboard
    </Link>
    <Link replace to="/about">About</Link>
  </nav>
)

export default Links
```

## NavLink

```js
import { NavLink } from 'react-router-dom'

const Links = ({ match }) => (
  <nav>
    <NavLink activeClassName="active" to="/">
      Home
    </NavLink>
    <NavLink activeClassName="active" to="/dashboard">
      Dashboard
    </NavLink>
    <NavLink activeClassName="active" to="/about">
      About
    </NavLink>
  </nav>
)

export default Links
```

## match

```js
const Dashboard = ({ match }) => (
  <ul>
    <li>params: {JSON.stringify(match.params)}</li>
    <li>isExact: {match.isExact.toString()}</li>
    <li>path: {match.path}</li>
    <li>url: {match.url}</li>
  </ul>
)

<Route path="/dashboard" component={Dashboard}/>
//Copyright © Leon Gilyadov 2018 - 2020
```
