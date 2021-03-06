# Full-stack React

- React, client-side routing, Redux, GraphQL, Relay (glue between GraphQL and React), React Native.

## Part 1

- Semantic UI: framework like Bootstrap.
- Components: Hierarchy of one parent component and many child components.
- Is there a pre-processor/.
- `.render()` is the only required method for a React component. React uses the return value from this method to determine what to render to the page.
- ES6 classes: syntactical sugar over JavaScript's prototype-based inheritance model.
- To declare React components:
   - ES6 classes.

```
class HelloWorld extends React.Component {
  render() { return(<p>Hello, world!</p> }
}

// It's the same as this:

import createReactClass from 'create-react-class';

const HelloWorld = createReactClass({
  render() { return(<p>Hello, world!</p> }
});
```

- JSX: Javascript Extension Syntax. Using JSX enables us to write the markup for our component views in a familiar, HTML-like syntax.
  - The `render()` method of a component needs to describe how the view should be represented as HTML.

### Babel

- Babel is a JS transpiler. It turns ES6 into ES5 code. It also understands JSX a.

### To actually render shit:

- Need to include `react-dom.js`.
- `ReactDOM.render([what], [where]);`
- `ReactDOM.render(<ProductList/>, document.getElementById('content'));`

- We cannot use any reserved JavaScript words in JSX.
- The JSX components return is NOT actually the HTML that gets rendered, but is the representation that we want React to render in the DOM.
- `this.props` → Can't see the contents of props?

### Your app's first interaction

- While the child can read its props, it can't modify them. A child does not own its props. The parent component owns the props given to Product.
- One-way data flow, changes come from the top of the app and are propagated downwards through its various components.
- We can pass down functions as props too. We can have the `ProductList` component give each `Product` component a function to call when the upvote is clicked. Functions passed down through props are the canonical manner in which children communicate events with their parent components.

### Passing shit downward:

- Inside `render()`, we say that `this` is "bound" to the component. Put another way, `this` references the component.
- For the `render()` function, React binds `this` to the component for us. Same with `componentDidMount()` and other methods. But when we create new methods, we have to manually bind `this` to the component ourselves.

### Using state

- Whereas props are immutable and owned by a component's parent, state is owned by the component. `this.state` is private to the component and can be updated by `this.setState()`.
- Every React component is rendered as a function of `this.props` and `this.state`. The rendering is deterministic. This means that give a set of props and a set of state, a React component will always render a certain way.
- Because we are mutating the data for our products, we should consider this data to be stateful.
  - `ProductList` will be the owner of this state. It will pass the state down as props to `Product`.
  - At the moment, `ProductList` is reading directly from `Seed` inside `render()` to grab the products.
  - Initial state: constructor.

```
class ProductList extends React.Component {
  // Defining an
  handleProductUp(productId, productName) {
    // There's no such thing as
    // stuff = [productId]
    // You have to do const stuff or var stuff
    var stuff = [productId, productName, 'was voted'];

    console.log(stuff.join(' '));
  }

  render() {
    // Sorting: This actually mutates the array
    const sortedProducts = Seed.products.sort((a, b) => ( b.votes - a.votes ));

    const productComponents =  sortedProducts.map(
      (product) => (
        // You need to pass in the 'key' just for React to internally know which Product is which
        // There is also no method called this.props.key inside the Product
        <Product
          key={'product' + product.id }
          id={product.id }
          title={product.title}
          description={product.description}
          url={product.url}
          votes={product.votes}
          submittedAvatarUrl={product.submittedAvatarUrl}
          productImageUrl={product.productImageUrl}
          onVote={this.handleProductUp}
        />
      )
    );

    // Remember, put everything before the return
    return (
      <div className='ui unstackable items'>
        {productComponents}
      </div>
    );
  }
}

class Product extends React.Component {
  // JS special constructor method.
  constructor(props) {
    super(props);

    // Remember your best friend, the JS "this"? It's back.
    // For the React lifecycle methods like `render`, `this` is automatically bound to the component that we are using.
    // For created methods like `handleUp`, we have to explicitly set the `this` to be the handler.
    // Why this type of syntax? I have no idea. F JS.
    this.handleUp = this.handleUp.bind(this);
  }

  // Defined own method, that calls the method above it, to update things below?
  handleUp() {
    this.props.onVote(this.props.id, "pants");
  }

  // You always need a return from the render function.
  // If you want to render nothing, then render null;
  render()  {
    return (
      <div className='item'>
        <div className='image'>
          <img src={ this.props.productImageUrl }/>
          <p></p>
        </div>
        <div className='middle aligned content'>
          <div className='header'>
            <a onClick={this.handleUp}>
              <i className='large caret up icon'/>
            </a>
          </div>
          <div className='description'>
            <a>{ this.props.title }</a>
            <p>{ this.props.description }</p>
            <a>{ this.props.votes }</a>
          </div>
          <div className='extra'>
            <span>Submitted by:</span>
            <img
              className='ui avatar image'
              src='images/avatars/daniel.jpg'
            />
            <p>
              Hello
            </p>
            props
          </div>
        </div>
      </div>
    );
  }
}

ReactDOM.render(
  <ProductList />,
  document.getElementById('content')
);
```

### Components

- `server.js`: Last time, we used a pre-built Node package called `live-server` to serve our assets.
- Components:
  - `TimersDashboard`
    - `TimersList`
      - `Timer`
    - `ToggleableTimerForm`: The plus sign. This transmutes into a form. When the form is closed, the widget transmutes back into a "+" button.
- We want the `ToggleableTimerForm` to either render a `TimerForm` or a "+" button.
- Displaying a timer and editing a timer are also two distinct UI elements. These should be two distinct React components.
  - `EditableTimer`: Can either be a `Timer`, or a `TimerForm`.

- For the `ProductList` in the last chapter, we can probably hoist sort responsibility up to a parent component and
- ***Ultimately, the top-level component will communicate with a server. The server will be the initial source of state, and React will render itself according to the data the server provides. Our app will also send updates to the server, like when a timer is started.***

- The framework:
  - Break the app into components.
  - Build a static version of the app.
  - Determine what should be stateful.
  - Determine in which component each piece of state should live.
  - Hard-code initial states.
  - Add inverse data flow.
  - Add server communication.
- You can do something like not pass something into the `TimerForm` because sometimes, the C and the U forms are the same, but not really.

### Components & Servers

- State management of timers takes place in the top-level compound `TimersDashboard`.

```
// Wrong, getTimers() does not return the list of timers.
const timers = client.getTimers();

// Do this:
// getTimers uses the Fetch API.
// When `getTimers()` is invoked, it fires off the request to the server and then returns control flow immediately. The execution of our program does not wait for the server's response. This is why `getTimers()` is called an asynchronous function.
client.getTimers((serverTimers) => (

));
```

```
class TimersDashboard extends React.Component {
  state = {
    timers: [],
  };


  componentDidMount() {
    this.loadTimesFromServer();
    // Constantly poll?
    setInterval(this.loadTimesFromServer, 5000);
  }

  loadTimersFromServer = () => {
    client.getTimers((serverTimers) => (this.setState({ timers: serverTimers })))

  };
};
```

Timeline:

- **Before initial render.** React initializes the component. `state` is set to an object with the property `timers`, a blank array is returned.
- **The initial render.** React then calls `render()` on `TimersDashboard`. In order for the render to complete, its children must be rendered.
- **Children are rendered.** At first, there will be a blank `timers` div.
- **Initial render is finished.** With its children rendered, the initial render is finished and the HTML is written to the DOM.
- **`componentDidMount` is invoked.** This calls `loadTimersFromServer`.
- We use `setInterval()` to ensure `loadTimersFromServer()` is called very 5 seconds.

- Fetch API:
  - Include the library just in case.

```
}).then(checkStatus) // defined inside of client.js, just logs errors to the console
  .then(parseJSON)   // Takes a response object emitted by fetch() and returns a JS object.
  .then(success);    // This is the function we pass as an argument to getTimers(). getTimers() will invoke this function if the server successfully returned a response.
```

- Fetch returns a promise. We can then chain `.then()` statements.
  - Fetch the timers from `/api/timers`, then check the status code returned by the server, then extract the JS object from the response, then pass that object to the success function.


```
// Timer requesting from the API code
function startTimer(data) {
  return fetch('/api/timers/start', {
    method: 'post',
    body: JSON.stringify(data),
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    }
  }).then(checkStatus);
}
```

```
// Timer actually reflecting UI changes code

startTimer = (timerId) => {
  const now = Date.now();

  this.setState({
    timers: this.state.timers.map((timer) => {
      if (timer.id === timerId) {
        return Object.assign({}, timer, {
          runningSince: now,
        });
      } else {
        return timer;
      }
    });
  });

  // Optimistic updating. Optimistic that it's a success.
  client.startTimer(
    { id: timerId, start: now }
  );
};
```

### JSX and the Virtual DOM

- Virtual DOM: a tree of JS objects that represents the actual DOM. When using the Virtual DOM, we code as if we're creating the entire DOM on every update.
- (Shadow DOM: A form of encapsulation of elements, ex: video tag is actually a lot of things.)
- Pieces: a tree of `ReactElements`.
  - `ReactElement` is a representation of a DOM element in the Virtual DOM.

- `React.createElement()` was created with these arguments:
  - The DOM element type.
  - The element props.
  - The children of the element.
    - Must be: a `ReactElement`, and array of `ReactNodes`, or a string or number.

- `ReactDOM` constructor third argument: a callback argument that is executed after the component is rendered/updated. `ReactDOM.render(boldElement, mountElement, function() {});`
- JSX. => Skip

### Advanced Component Configuration with `props`, `state`, and `children`

- A `ReactComponent` is a JS object that, at minimum, has a `render()` function. `render()` is expected to return a `ReactElement`.
- The goal of a `ReactComponent` is to:
  - `render()` a `ReactElement`.
  - Attack functionality to this section of the page.

### Forms

### Using Webpack with Create React App (260)

### Unit Testing

### Routings (391)

- **Think of the external URL as an external keeper of state.**
- Routing involves:
  - Two primary pieces of functionality: Modifying the location of the app (the URL) and determining what React components to render at a given location.
- Core components:
  - For modifying the location of an app, we use links and redirects.
  - For determining what to render, we use the components `Route` and `Switch`.

- At the start:
  - **The app doesn't care about the state of the pathname.**
  - No matter what path the browser requests from our server, the server will return the same index.html with the same JS bundle.
  - **We want this. We want our browser to load React in the same way in each location and defer to React on what to do at each location.**
  - **In the React Router, `Route` is a component that determines whether or not to render a specified component based on the app's location.**

- To use `Route`, we need two arguments:
  - The path to match against the location
  - The component to render when the location matches the `path`.
- Route is a component.

- Between the `Link` and `Route` components, we're getting an understanding of how we can use a component-driven routing paradigm to make updates to the browser's location and have our app respond to this state change.

#### Building `Router`

- Need to:
  - Supply its children with context for both `location` and `history`.
  - Re-render the app whenever the history changes.

#### Building `Redirect`

- The `Redirect` component is a cousin of `Link`. `Redirect` will immediately modify the location.
- To `Redirect`, just render a `Redirect` component.
- `componentWillUnmount()` clears the Interval
- Ternary inside JSX is common in React. We can't embed multi-line statements like an `if/else` clause inside our JSX.

#### Code without react-router

``` js
import React from 'react';

import createHistory from 'history/createBrowserHistory';
import PropTypes from 'prop-types';

// If we're initializing `history` inside of `Router`, we can remove this declaration
// const history = createHistory();

class Router extends React.Component {
  // JavaScript classes: static
  // this keyword allows us to define a property on the class Router itself, as opposed to instances of Router
  // Same as Router.childContextTypes = { ... }
  static childContextTypes = {
    history: PropTypes.object,
    location: PropTypes.object
  };

  // Subscribe to history
  constructor(props) {
    super(props);

    this.history = createHistory();
    this.history.listen(() => this.forceUpdate());
  }

  getChildContext() {
    return {
      history: this.history,
      location: window.location
    };
  }

  // This actually just means that you do literally nothing except the routing, but you also render the children underneath
  render() {
    return this.props.children;
  }
}

// Use ES6 destructuring syntax to extract out two props, path and component, from the arguments
// This is triggered twice, once for Atlantic and once for Pacific
const Route = ({ path, component }, { location }) => {
  // Grab the location from the window
  const pathname = location.pathname;
  if (pathname.match(path)) {
    // Since dynamic variable, you can use the React.createElement() vs JSX.
    return React.createElement(component)
  } else {
    return null;
  }
};

Route.contextTypes = {
  location: PropTypes.object,
}

// Cool link implementation
// We have history in the parent Router element. So we need to access that and we use JS destructuring. Must explicitly include history in the contextTypes
const Link = ({ to, children }, { history }) => (
  <a
    onClick={(e) => {
      // We add the preventDefault because we have a href at the bottom
      e.preventDefault();
      // Trigger the history API to push the noe location onto the browser's history stack.
      history.push(to);
      // We still set up the href so the user can hover over the links and see where they lead, also to open new tabs
    }}
    href = {to}
  >
    {children}
  </a>
);

Link.contextTypes = {
  history: PropTypes.object,
}

class App extends React.Component {
  // While Link is updating the location of the browser, our React app is not alerted of the change. So we trigger a re-render whenever the location changes.
  componentDidMount() {
    // history.listen(() => this.forceUpdate());
  }

  render() {
    return (
      <Router>
        <div
          className='ui text container'
        >
          <h2 className='ui dividing header'>
            Which body of water?
          </h2>

          <ul>
            <li>
              {/* A href here means that you actually go to the server. This is unneeded because we are using a SPA. */}
              {/*<a href='/atlantic'>*/}
                <Link to='/atlantic'>
                  <code>/atlantic</code>
                </Link>
              </li>
              <li>
                <Link to='/pacific'>
                  <code>/pacific</code>
                </Link>
              </li>
            </ul>

            <hr />

            <Route path='/atlantic' component={Atlantic} />
            <Route path='/pacific' component={Pacific} />
          </div>
        </Router>
    );
  }
}

const Atlantic = () => (
  <div>
    <h3>Atlantic Ocean</h3>
    <p>
      The Atlantic Ocean covers approximately 1/5th of the
      surface of the earth.
    </p>
  </div>
);

const Pacific = () => (
  <div>
    <h3>Pacific Ocean</h3>
    <p>
      Ferdinand Magellan, a Portuguese explorer, named the ocean
      'mar pacifico' in 1521, which means peaceful sea.
    </p>
  </div>
);

export default App;
```

#### Using `react-router`

``` js

import {
  BrowserRouter as Router, // Used to separate from routers included in other environments
  Route,
  Link,
  Redirect,
} from 'react-router-dom'

```

- The router: Any number of components might match a given location and they will all render.
- `<Route exact={true} path='/' render ...>` is the same as `<Route exact />`
- `Switch`?

#### Music App

- Go back to this Later
