
[https://kdakan.github.io/Tutorial-On-React-React-Router-and-Redux/](https://kdakan.github.io/Tutorial-On-React-React-Router-and-Redux/)

# Tutorial on React, React Router, and Redux
This is an extensive tutorial on React v.16, React Router v.4, and Redux. This can also serve as a mini-reference. This tutorial will take you from beginner level to intermediate/advanced level and covers enough breadth and depth to get you going with most of your projects. 

In the final section [18. Hands-on project - CRUD app](#18-hands-on-project---crud-app), we will be building an app which demonstrates most of the concepts. 

Before beginning, make sure you have some knowledge of ES6. I also have a tutorial on ES6 [here](https://kdakan.github.io/ES6-Tutorial).

Unlike Angular, React only handles the view part of the MV* architecture. There is no controller, service, or dependency injection. Programmers mainly utilize components with props and local (temporary) state, and use a global state manager like ```Redux``` or ```Mobx``` to simplify sharing state between components across the app.

## Table of contents
* [ 0. Setup](#0-setup)
* [ 1. Creating and running an app](#1-creating-and-running-an-app)
* [ 2. Components](#2-components)
* [ 3. Immutable objects and arrays](#3-immutable-objects-and-arrays)
* [ 4. Importing components, CSS, and images](#4-importing-components-css-and-images)
* [ 5. Binding this to event handlers](#5-binding-this-to-event-handlers)
* [ 6. Lifecycle methods](#6-lifecycle-methods)
* [ 7. Working with forms, inputs, and refs](#7-working-with-forms-inputs-and-refs)
* [ 8. Type-checking on props](#8-type-checking-on-props)
* [ 9. Routing](#9-routing)
* [10. Context api](#10-context-api)
* [11. State management](#11-state-management)
* [12. State management with MobX](#12-state-management-with-mobx)
* [13. State management with Redux](#13-state-management-with-redux)
* [14. Calling an async api with Redux and redux-promise](#14-calling-an-async-api-with-redux-and-redux-promise)
* [15. Chaining async calls with Redux Thunk](#15-chaining-async-calls-with-redux-thunk)
* [16. Integrating React Router with Redux](#16-integrating-react-router-with-redux)
* [17. React-bootstrap components](#17-react-bootstrap-components)
* [18. Hands-on project - CRUD app](#18-hands-on-project---crud-app)

## 0. Setup:
* We can use the official CLI ```create-react-app``` to start developing in React, without getting lost in ```webpack``` and ```babel``` tool configuration.
* ```npm install -g npm@latest```
* ```cd c:\repos```
* ```npx create-react-app MyReactApp``` (npx installs create-react-app if missing, you can ```npm install -g create-react-app``` and ```create-react-app MyReactApp``` instead)
* ```cd MyReactApp```
* ```npm install --save bootstrap``` (if using bootstrap.css for styling)
* Note that with npm ver. 5, there is no need to give the ```--save``` option, ```npm install somePackage``` works the same way as ```npm install --save somePackage```, so that it adds ```somePackage``` to the ```dependencies``` section in the ```packages.json``` file.
* ```npm install --save prop-types``` (if using ```PropTypes``` for type checking on props)
* ```npm install --save react-router-dom``` (if using the router)
* ```npm install --save redux redux-thunk``` (if using ```redux``` for state management)
* In ```VSCode``` editor, install ```Debugger for Chrome``` extension, and change the port to 3000 inside launch.json file, to debug inside the editor. Without this extension, you can still debug inside the ```Chrome``` browser.
* In ```Chrome```, install ```React Developer Tools``` extension, to see React component hierarchy, props, and state inside Chrome F12 React tab.

## 1. Creating and running an app:
* ```npm start``` (starts the app in development mode, with hot reload, so that you can see the changes without restarting the app)
* ```npm test``` (runs ```jest``` tests in the app)
* ```npm build``` (builds the app for xcopy deployment to production)
* ```npm eject``` (ejects the app source from ```create-react-app```, use this if you need to change ```webpack``` config, after ejecting, you cannot use create-react-app again for the same app source)

## 2. Components:
* A React app is a tree of components, which communicate with each other via their props, which can be of any type, including primitives, objects, arrays, sets, and callback functions.
* There is a root ```App``` component, which hosts all other components, and is rendered on a ```div``` with ```id=app``` inside   ```index.html```, using 
  ```jsx 
  ReactDOM.render(<App>, document.getElementById("app"))
  ```
  (```App``` and ```app``` naming is conventional, can be any name you want)
* Preferably, we define each component in its own file that exports it as an ES6 module, and we can import other components or ES6 modules.
* Component names should begin with an uppercase character, else React may confuse it with regular HTML tags, like ```<Button>``` vs     ```<button>```.
* Props and state changes trigger the ```render()``` method of a component.
* Functional components have a props argument and return the rendered result in JSX syntax, like 
  ```jsx
  const MyComponent = (props) => {
    return (
      ...some JSX...
    );
  };
  ``` 
  or with an event handler like 
  ```jsx
  const MyComponent = (props) => {
    const handleClick = (e) => {
      ...
    };
    return (
      ...some JSX...
    );
  };
  ```
  Note that we should wrap multiple lines of ```JSX``` code in a ```return``` statement with parenthesis. Because in javascript ```return``` with no expression on the same line returns immediately (will return ```undefined``` instead of the ```JSX``` code underneath). There is no need to wrap single line ```JSX``` code with parenthesis.
* Functional components do not use this keyword, do not have lifecycle methods, and cannot access refs.
* Functional components do not have state, they are presentational components which use only their props, and only render result in JSX syntax.
* Functional components that have inner functions (like event handler callback functions) can cause performance issues, so it is better to use class components if we need event handlers.
* Class components extend ```React.Component```, have a ```render()``` function which returns the rendered result in JSX syntax. If you return null, it generates no HTML.
* Class components can have a ```constructor(props)```, should first call ```super(props)```, and later can initialize component's   state, like 
  ```jsx
  this.state = {someState: ..., someOtherState: ...}
  ```
* Class components can initialize their state even without a constructor, in shorter class field syntax, omitting this, like 
  ```jsx
  state = {someState: ..., someOtherState: ...}
  ```
* Components which hold state and which delegate rendering much of JSX markup to presentational components, are also called container components. Container components can render other container components and presentational components. Presentational components don't have state and just render JSX with props passed to them and call the callback functions (actions) again passed through their props. Container components are also called smart components, stateful components, or controller views. Presentational components are also called dumb components, stateless components, or views. 
* Container components are usually connected to a state management library like ```Redux```, but presentational components are not. A React app is composed of a low number of containers and a high number of presentational components. Because presentational components are decoupled from state, they are more reusable and also simpler to use and understand.
* React uses virtual DOM, so ```render()``` method does not update all the HTML for the component, but only updates the changed parts. React diff's the current props and state with the next props and state and renders if there is a change.
* Even when using virtual DOM, unnecessary render calls cost some performance because of the diff'ing of the current virtual DOM and next virtual DOM. Therefore it is more performant to check inside ```shouldComponentUpdate()```if the current props and state are different than next props and state and return false if not. Extending component from ```React.PureComponent``` instead of ```React.Component``` will do this by shallow comparison of props and state.
* Components can use functions that return the partial rendered result in JSX syntax, can also store this JSX in a variable, and use them to compose into a larger rendered JSX result, if null is returned, no HTML is generated.
* Components cannot change their props, props are immutable.
* Components use 
  ```jsx
  this.setState({someState: ...})
  ```
  to change their state, they do not assign values to the state directly.
* React can detect changes faster when an array state member is not mutated but assigned a new or cloned array, so instead of   
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.push(item)})
  ```
  it is better to use 
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.concat([item])})
  ```
  Refer to the section "Immutable objects and arrays" for more details.
* State can be set in multiple steps, so that all state object members do not have to be given, like 
  ```jsx
  this.setState({someState: ...})
  ``` 
  and later 
  ```jsx
  this.setState({someOtherState: ...})
  ```
* JSX is transpiled to javascript by ```Babel```, like 
  ```jsx
  React.createElement(...)
  ```
* We can render items in an array by using ```Array.map()``` function and map items to JSX elements. Items rendered this way should have a unique key property. Do not use array index as key because as items are inserted or deleted in the middle of the array, indexes of existing items change and this confuses React and causes rendering issues.
* State is private to each component, to update the state of a parent component, the child component should use the callback function passed to it via props.
* Components can change their state or their parent components' state by calling callback functions via props, on events like button click, link click, form submit, timer tick, ajax requests, etc.
* CSS class attribute is used as ```className``` in JSX and HTML label attribute for is used as ```htmlFor```, because JSX is compiled to javascript, and class and for are reserved words in javascript.
* We can bind simple javascript statements inside ```{}```, we can references props object by ```{this.props.someProp}```, state objects  by ```{this.state.someState}```, callback functions on props by 
  ```jsx
  onClick={(e) => this.props.someParentFunction(e)}
  ```
* There are alternative ways to bind callback functions, see the ```Binding this``` section.
* UI container components such as panel or dialog components, can render whatever is passed inside the opening and closing tags, using   ```this.props.children```, like 
  ```jsx
  <Dialog>
    <input .../>
    <button>OK</button>
  </Dialog>
  ``` 
  and ```Dialog``` component's render method 
  ```jsx
  render() {
    return (
      <div className="dialog-frame">{this.props.children}</div>
    )
  }
  ```
* When structuring an app into components, it is easier to start with components without state, DOM events, and callback functions, and visualize a static page, and later add state, DOM events, and callback functions later one by one.
* It is better to place functions that do not use props or state or this, outside the component function or class.

## 3. Immutable objects and arrays:
* In javascript, strings, booleans, numbers are immutable, but objects, arrays, and functions are not. When you assign a new value to an item of an array, or assign a new value to a property on an object, the array's or the object's reference (memory location) does not change. This is why arrays and objects are not immutable. To change arrays and objects in an immutable way, we need to clone it first.
* To clone an array, ```someArray.slice()```, ```[].concat(someArray)```, or spread operator ```[...someArray]``` can be used. To clone an array and add an item, we can use ```someArray.concat([item])``` or ```[...someArray, item]```. To remove an item from an array, we would normally use ```someArray.splice(index, 1)```, but to clone and remove an item we use ```someArray.slice(0, index).concat(someArray.slice(index + 1))```, which clones elements up to index and clones elements from index + 1 up to end and concatenates them. This is the same as ```[...someArray.slice(0, index), ...someArray.slice(index + 1)]```. To increment an item in an array without mutating it, we can similarly use ```someArray.slice(0, index).concat(someArray[index] + 1).concat(someArray.slice(index + 1))```, or ```[...someArray.slice(0, index), someArray[index] + 1, ...someArray.slice(index + 1)]```. To do more complicated manipulations on a cloned array, we can use ```someArray.map(item => ...code that returns a cloned and modified element...)```.
* To clone an object (shallow copy, meaning that members are copied but not cloned), we can use ```Object.assign({}, someObject)``` or ```{...someObject}```. To clone an object and change a field, we can use ```Object.assign({}, someObject, { someField: someValue })``` or ```{...someObject, someField: someValue}```.
* We can only do shallow copy using ```Object.assign()``` and spread operator ```...```, but if some part of the data is not changed, it does not have to go through deep copying.

## 4. Importing components, CSS, and images:
* If you're not comfortable with the ES6 module syntax, refer to the "ES6 modules" section in my "ES6 Tutorial" [here](https://kdakan.github.io/ES6-Tutorial#14-es6-modules).
* We can import React components, like 
  ```import MyComponent from './components/myComponent'``` 
  This code loads the ```MyComponent``` which is ```default export``` either from the ```./components/myComponent.js``` file or from the ```index.js``` file inside the ```./components/myComponent``` folder. 
* It is common to use features as parent folder names or container names as parent folder names and to place other related components inside subfolders. For example ```./components/search``` folder can have ```./components/search/filter/search-filter.js```, ```./components/search/panel/search-panel.js```, ```./components/search/panel/search-container.js```, ```./components/search/list/search-list.js```, ```./components/search/list/search-list-item.js``` components. There can also be a ```./components/search/index.js``` where each of the components are re-exported for ease of importing from the single location ```./components/search```, like
  ```jsx
  //inside ./components/search/index.js
  export SearchFilter from './filter/search-filter.js'
  export SearchPanel from './panel/search-panel.js'
  export SearchContainer from './panel/search-container.js'
  export { SearchList } from './list/search-list.js'
  export { SearchListItem } from './list/search-list-item.js'
  ```
  and ```SearchFilter``` default export inside ```./filter/search-filter.js```, like
  ```jsx
  //inside ./components/search/filter/search-filter.js
  export default class SearchFilter {
    ...
  }
  ```
  and similarly ```SearchPanel``` and ```SearchContainer``` default export inside their own .js files.
  With ```SearchList``` named export inside ```./list/search-list.js```, like
  ```jsx
  //inside ./components/search/list/search-list.js
  export class SearchList {
    ...
  }
  ```
  and similarly ```SearchList``` named export inside its own .js file.
  We can then import ```SearchFilter``` and ```SearchList``` inside ```./components/app.js```, like
  ```jsx
  //inside ./components/app.js
  import { SearchFilter, SearchList } from './search'
  ```
  or like
  ```jsx
  //inside ./components/app.js
  import SearchFilter from './search/filter/search-filter.js'
  import { SearchList } from './search/list/search-list.js'
  ```
* If we don't want to bundle CSS files, we can manually add a link to those CSS files inside the main site ```index.html```, like 
  ```jsx
  <link rel="stylesheet" href="site.css">
  <link rel="stylesheet" href="bootstrap.min.css">
  ```
* If we want to bundle CSS files, we should import those CSS files so that ```webpack``` will bundle these CSS files and automatically add a link to the bundled CSS inside main site ```index.html```
* We can import a component specific CSS file into the component, like
  ```jsx
  //inside ./components/myComponent.js
  import './myComponent.css'
  
  export default class MyComponent {
    ...
  }
  ```
* We can import site-wide used CSS files ```site.css``` and ```Bootstrap.css``` into the app startup ```index.js```, like 
  ```jsx
  //inside ./index.js
  import './site.css'
  import '../node_modules/bootstrap/dist/css/bootstrap.min.css'
  ```
* We can import an image file, like 
  ```jsx
  import myImage './myImage.png'
  ``` 
  and use it, like 
  ```jsx
  <img src={myImage} />
  ```
 
## 5. Binding this to event handlers:
* When using a class component, we need to bind ```this``` to DOM event handler callback functions, in order to access component members such as ```this.props``` or ```this.state```.
* For performance reasons, and to prevent memory leaks, we should either bind ```this``` explicitly inside the constructor, like
  ```jsx
  constructor(props) {
    ...
    this.handleChange = this.handleChange.bind(this);
    ...
  }
  
  handleChange(e) {...}
  ```
  or define the handler as a class property arrow function, like 
  ```jsx
  handleChange = (e) {...}
  ```
  In either case, we can use it inside ```render()```, like
  ```jsx
  render() {
    return (
      <input onChange={this.handleChange} />
    );
  }
  ```
* Note that the following examples create a new function on each render, and cause memory leaks! Do not use them:
  ```jsx
  //BAD!!! CAUSES MEMORY LEAKS!!!
  render() {
    return (
      <input onChange={this.handleChange.bind(this)} />
    );
  }
  
  //BAD!!! CAUSES MEMORY LEAKS!!!
  render() {
    return (
      <input onChange={e => this.handleChange(e)} />
    );
  }
  ``` 

## 6. Lifecycle methods:
* Components are mounted when they are first time inserted into the DOM. Later they are updated when their props or state changes. Finally, they are unmounted when they are removed from the DOM.
* In each of these phases, class components invoke specific methods in a specific order, these are called the lifecycle methods.
* When mounting, the following methods are invoked in this order:
  1. ```constructor(props)```: Here we can initialize state and bind callback functions.
  2. ```static getDerivedStateFromProps(props, state)```: This is rarely used, for transitions.
  3. ```render()```: Here we render the JSX markup, we should not set state here.
  4. ```componentDidMount()```: Here we can make AJAX requests and set state, and also mount a jQuery plugin.
* When updating, the following methods are invoked in this order: 
  1. ```static getDerivedStateFromProps(props, state)```: Usage is not recommended. We can return an object as the new state, or return null if we don't want to change state here. Derived data should normally be computed from a pure function inside ```render()```, not here.
  2. ```shouldComponentUpdate(nextProps, nextState)```: This is rarely used, for performance optimization, and later may become obsolete. We can return false to prevent updating of a component. If we use ```PureComponent``` instead of ```Component```, this is automatically implemented and returns false if the ```props``` and ```state``` did not mutate (```this.props===nextProps && this.state===nextState```), so that if we do not mutate state and derive our component from ```PureComponent```, we can prevent unnecessary renders and improve performance automatically.
  3. ```render()```: Here we render the JSX markup, we should not set state here.
  4. ```getSnapshotBeforeUpdate(prevProps, prevState)```: This is rarely used, for example, to set the scroll position.
  5. ```componentDidUpdate(prevProps, prevState, snapshot)```: Here we can make AJAX requests and set state, but we should compare ```this.props``` fields to ```prevProps``` fields and set state only if they are different, else there will be an infinite loop.
* When unmounting, ```componentWillUnmount()``` is invoked. Here we can unmount/destroy a jQuery plugin,  we should not set state here.
* OBSOLETE!!! Note that ```componentWillMount()``` and ```componentWillReceieProps()``` methods are obsolete/unsafe and should not be used anymore.
* To use async/await pattern to call an async API (async API meaning the API returns a ```Promise``` instead of data result), we can use ```async```   before ```componentDidMount()``` and use ```await``` before the API call, like 
  ```jsx
  async componentDidMount() { 
    let data = await api.fetchData(); 
    this.setState({data: data});
  }
  ```
* If we need to manipulate the DOM, or set a timer to poll periodically for new data, we can use ```componentDidMount()```, like
  ```jsx
  componentDidMount(){
    let intervalId = setInterval(this.fetchData(...), 1000)
    this.setState({ intervalId: intervalId })
  }
  ```
  And later we should do cleanup, like
  ```jsx
  componentWillUnmount(){
    clearInterval(this.state.intervalId)
  }
  ```
* To catch all kind of errors inside the app, then display and log them using an API, we can wrap the root ```App``` component with another component (conventionally named ```ErrorBoundary```), we can use its ```componentDidCatch(error, info)``` and ```getDerivedStateFromError(error)``` lifecycle methods, like
  ```jsx
  class ErrorBoundary extends React.Component {
    constructor(props) {
      super(props);
      this.state = { hasError: false };
    }

    static getDerivedStateFromError(error) {
      return { hasError: true };
    }

    componentDidCatch(error, info) {
      logComponentStackToMyService(info.componentStack);
    }

    render() {
      if (this.state.hasError) {
        return <h1>Something went wrong!</h1>;
      }

      return this.props.children; 
    }
  }
  ```

## 7. Working with forms, inputs, and refs:
* React DOM events are synthetic events, which wrap DOM events in a cross-browser way.
* We need to explicitly call ```e.preventDefault()``` or ```e.stopPropogating()``` inside a DOM event handler like submitting a form or navigating to a link, to prevent normal event actions and bubbling. This is unlike the case where jquery implicitly calls both ```e.preventDefault()``` and ```e.stopPropogating()``` if you return in an event handler.
* When we bind a form input's value to a state property, this is called a controlled component, like 
  ```jsx
  <input id="name" value={this.state.name} onChange={this.handleNameChange}
  ```
  and 
  ```jsx
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  ```
* Instead of binding an input's value to a state property, we can also use the input value through its ref, by creating a ref field, assigning this ref field to the input and use the input value through this ref field.
* To use a rendered DOM element inside an event handler function, we can create a ref in the constructor, like
  ```jsx
  this.nameInput = React.createRef();
  ```
  and then assign it to the input, like
  ```jsx
  <input ref={this.nameInput} />
  ```
  and use it inside a form submit handler function, like 
  ```jsx
  let newName = this.nameInput.current.value;
  ```
 * Another way of using a ref is, instead of creating the ref in the constructor, we can create and assign a ref property to the element inside ```render()```, like 
  ```jsx
  <input ref={element => this.nameInput = element} />
  ```
  and use it inside a form submit handler function, like 
  ```jsx
  let newName = this.nameInput.current.value;
  ```
* We can also use a ```jQuery```, ```GoogleMaps``` or similar plugin, using the DOM element ref of an empty ```<div>```, and mount it inside the ```componentDidMount()``` method and unmount/destroy it inside the ```componentWillUnmount()``` method of our React component.
* It is recommended to use controlled inputs instead of refs. Refs should be used mostly to manage non-React plugins, or manage focus and text selection.
* It is recommended to use a ```<form>``` with a submit button (```<button type="submit">```), when we have inputs and a button to take action on the inputs. Because this way, enter key acts as clicking the submit button by default. However, we also need to have an ```onSubmit``` DOM event handler which calls ```e.preventDefault()``` to prevent server-side posting/reloading of the page.
* We can do form validation such as a required field, like 
  ```jsx
  <form>
    <label htmlFor:"name">Name:</label>
    <input id="name" value={this.state.name} onChange={this.handleNameChange} />
    <button disabled={this.state.name.length === 0} onSubmit={this.handleSubmit}>Submit Form</button>
  </form>
  ``` 
  and 
  ```jsx
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  handleSubmit = (e) => {
    e.preventDefault();
    let newName = this.state.name;
    ...
  }
  ``` 

## 8. Type-checking on props:
* To check for missing or mis-typed props passed at development runtime, we add a ```propTypes``` member on the component class or function, with prop fields and their types, like
  ```jsx
  const MyComponent = (props) => {
    return (...props.name...props.age...)
  };
  MyComponent.propTypes = {
    name: PropTypes.string.isRequired, 
    age: PropTypes.number 
  };
  export default MyComponent;
  ```
* PropTypes can be ```string```, ```number```, ```bool```, ```array```, ```object```, ```func```, ```symbol```, ```node```, ```element```, ```instanceOf(someJSClass)```, ```oneOf(someArrayOfVales)```, ```oneOfType(someArrayOfPropTypes)```, ```shape({ name: PropTypes.string.isRequired, age: PropTypes.number })```, etc.

## 9. Routing:
* We need routing for the browser back/forward buttons to work properly and also allow deep linking via URLs, without reloading the ```index.html``` from the server and losing all the state.
* We need server-side routing/rendering to serve public pages for better SEO, and we need client routing/rendering to serve authorized content for better user experience (for faster transitions without loading the full page).
* We need at least one route on the server, to serve the ```index.html``` and its js and css files (the React app), like
  ```javascript
  app.all('*', express.static('public/index.html'));
  ```
* On this node.js express server app, the first request for any URL in the site domain will first load the React app via ```index.html```,  and later requests will be handled by ```react-router-dom``` and React components.
* On a site which does not serve public pages via server-side routing/rendering, the first page served will be the login/signup page, which can either be rendered on the server or the client. Authorized content can also be routed to a different subdomain like ```app.mysite.com```.
* We can declare routes either statically at the startup of the app, before rendering starts (similar to how routes are declared in ```express js``` before the server starts listening to the port), or we can declare routes dynamically during rendering of each component.
* We do nesting of routes (and thus components associated with those routes) differently in static vs. dynamic routing.
* ```react-router-dom``` ver.3 only allows static routing, dynamic routing requires ```react-router-dom``` ver.4, and dynamic routing is the preferred way, the examples given below apply for dynamic routing.
* We should wrap the ```App``` component's JSX with ```<BrowserRouter>``` or ```<HashRouter>``` inside the ```render()``` method. We use ```BrowserRouter``` for modern browsers with pushstate, and ```HashRouter``` for IE9 and below using hash (#) addresses, like 
  ```jsx
    render() {
      return (
        <BrowserRouter>...some JSX...</BrowserRouter>
      )
   }
   ```
   or we can wrap the ```App``` component with ```<BrowserRouter>``` inside the main ```ReactDOM.render()``` call, like
   ```jsx
   ReactDOM.render(<BrowserRouter><App/></BrowserRouter>, document.getElementById("app"));
   ```
* We use the ```Link``` component for anchor links, like 
  ```jsx
  <Link to='/'>Home</Link>
  <Link to='/search'>Search</Link>
  ```
* ```NavLink``` component is similar to the ```Link``` component, but with the additional ```activeClassName``` and ```activeStyle``` props, which overrides the ```className``` and ```style``` props values when a user follows this link, like
  ```jsx
  <NavLink to='/' className="normalLink" activeClassName="activeLink">Home</NavLink>
  ```
  or
  ```jsx
  const normalLinkStyle = { fontWeight: "normal", color: "black" };
  ...
  <NavLink to='/search' style={normalLinkStyle} activeStyle={{ fontWeight: "bold", color: "red" }}>Search</NavLink>
  ```
  Here, both the ```style``` and ```activeStyle``` props accept an object as a value. The outer ```{}``` here, is for props binding syntax, and the inner ```{}``` is for object literal syntax.
* We can declare a route and associate it with a component to render, like 
  ```jsx
  <Route exact path='/' component={Home} />
  <Route path='/search' component={Search} />
  ```
* ```Route``` component renders the associated component if the path matches the URL, else renders ```null``` (renders no output).
* ```Redirect``` component declares a client-side redirection, so ```Route``` and ```Redirect``` are simply placeholders for their associated components, like
  ```jsx
  const App = () => (
    <div>
      <nav>
	    <Link to="/" activeClassName="active">Home</Link>{" | "}
	    <Link to="/list" activeClassName="active">List</Link>{" | "}
	    <Link to="/search" activeClassName="active">Search</Link>
      </nav>
      <div>
        <Route exact path='/' component={Home} />
        <Route path='/list' component={List} />
        <Route path='/search' component={Search} />
        <Redirect from="/home" to="/" />
      </div>
    </div>
  );
  ```
* A ```Route``` applies for the host component and its descendants, so ```Link``` components use those routes from their ancestor components.
* Multiple ```Route``` components can match a URL path, like routes with both ```/``` and ```/list``` paths will match the URL www.mysite.com/list. But we can force a single exact match by using the ```exact``` keyword in the ```Route``` component.
* We can also wrap ```Route``` components with ```<Switch>``` so that first match route is rendered, without the need of ```exact``` keyword, like
  ```jsx
  <Switch>
    <Route path='/' component={Home} />
    <Route path='/list' component={List} />
    <Route path='/search' component={Search} />
  </Switch>
  ```
   Here, normally all three routes would match the path ```/```, but ```<Switch>``` will force the first match, which is ```<Route path='/' component={Home} />```. This is similar to a ```switch/case``` statement with ```break```, where the first matching ```case``` statement is executed. 
   
* A route without a path will always be triggered, so if we place it on the last spot inside a ```<Switch>```, it will effectively work as a ```404 Page not found``` route, like
  ```jsx
  <Switch>
    <Route exact path='/' component={Home} />
    <Route path='/list' component={List} />
    <Route path='/search' component={Search} />
    <Route component={PageNotFound404} />
  </Switch>
  ```
* Components with props can only be triggered (rendered) by a ```Route``` component by using the ```render``` property of ```Route```, like
  ```jsx
   <Route path='/list' render={()=>{return <List items={myList}/>}} />
  ```
  and the rendered ```List``` component has to be wrapped with ```withRouter``` function to receive ```match```, ```location``` and ```history``` props from router, like
  ```jsx
  const List = (props) => {...}
   
  export default withRouter(List);
  ```
* Components triggered (rendered) by a ```Route``` component receive extra ```props```, which are ```match```, ```location```, and ```history```
* ```props.match``` fields:

  ```params```: (object) Key/value pairs parsed from the URL corresponding to the dynamic segments of the path
  
  ```isExact```: (boolean) true if the entire URL was matched (no trailing characters)
  
  ```path```: (string) The path pattern used to match. Useful for building nested ```<Route>```s
	
  ```url```: (string) The matched portion of the URL. Useful for building nested ```<Link>```s
* Route ```params``` can be anywhere in the path and can be multiple, and defined, like
  ```jsx
    <Route path='/item/:id' component={Item} />
    <Route path='/person/:firstname/:lastname/bio' component={Person} />
  ```
  and acccessed inside ```Item``` and ```Person``` components as ```props.match.params.firstname```, ```props.match.params.lastname```, and ```props.match.params.id```, like 
  ```jsx
  const Item = (props) => {... props.match.params.id ...}
  
  const Person = (props) => {... props.match.params.firstname ... props.match.params.lastname ...}
  ```
* ```props.location``` fields: ```hash```, ```key```, ```pathname```, ```search```
* Query string such as ```?name=Joe&age=23``` from a link like
  ```jsx
  <Link to="/detail?name=Joe&age=23">Detail</Link>
  ```
  can be parsed inside the associated component using ```URLSearchParams```, like
  ```jsx
  const values = URLSearchParams(props.location.search);
  const name = values.get('name');
  const age = values.get('age');
  ```
  or using ```query-string``` library for backward browser compabality, like
  ```jsx
  const values = querystring.parse(props.location.search);
  const name = values.name;
  const age = values.age;
  ```
* ```props.history``` is the ```window.history```, it also has a ```location``` field but because it is mutable, ```props.location``` should be used instead
* Routes can be nested, like
  ```jsx
  const App = () => (
    <BrowserRouter>
      <div>
        <Route path="/master" component={Master} />
      </div>
    </BrowserRouter>
  );

  const Master = ({ match }) => (
    <div>
      <Route path={match.url + "/detail"} component={Detail} />
    </div>
  );
  ```
  Here, ```/master``` matches the ```Master``` component, and ```/master/detail``` matches the ```Detail``` component
   
  ```const Master = ({ match }) => (...)``` means to use the ```match``` field inside the ```props``` parameter, so it could also be coded like below
  ```jsx
  const Master = (props) => (
    <div>
      <Route path={props.match.url + "/detail"} component={Detail} />
    </div>
  );
  ```
* ```Prompt``` component comes to effect when changing a route, before navigation happens, and displays a javascript ```confirm("...")``` box, so that it can be used to check if a form is complete before leaving the form, like
  ```jsx
  render() {
    <Prompt when={this.unsavedChanges} message="There are unsaved changes! Are you sure you want to leave this form?"
    <form>
      <label htmlFor:"name">Name:</label>
      <input id="name" value={this.state.name} onChange={this.handleNameChange} />
      <button disabled={this.state.name.length === 0} onSubmit={this.handleSubmit}>Save Form</button>
    </form>
  }  
  ```
  and 
  ```jsx
  unsavedChanges() {
    return this.state.name.length !== 0 && !this.state.isSubmitted;
  }
  
  handleNameChange = (e) => {
    e.preventDefault();
    setState({name: e.target.value});
  }
  
  handleSubmit = (e) => {
    e.preventDefault();
    let newName = this.state.name;
    ...
    setState({isSubmitted: true});
    setState({name: ""});
  }
  ```

## 10. Context API:
* Global state (language, user info, theme, etc.) is usually stored in the root component state properties.
* Normally, to pass data from a top-level component to a bottom level component, we should pass it through props of all the components in between.
* Using Context API, top-level components can pass data to bottom level components without using the bottom level components' props.
* To share global state, first we create a context with a default value like const ```LanguageContext = React.createContext('en');``` in its own module file and export it like ```export default LanguageContext;```
* Then, we import it in any component that provides its value or consumes its value, refer to [here](https://reactjs.org/docs/context.html) for examples.
* Context can hold any javascript object, not only primitives.
* It is advisable to use a state management library like ```Redux``` or ```MobX``` instead of the lower level Context api.

## 11. State management:
* When starting a new app or a new component tree, it is easier to start without thinking about any state and interaction, and just breaking down the target HTML output we want, into dumb visual components with no state or DOM events. The root component of the app or the component tree can be fed through its props with static data for prototyping the visuals.
* Next, we should identify the changing parts of the data and turn these parts into state. How we implement state is different depending on whether we are using a state management library or not. In any case, we do not put derived values into their own state, instead, we use functions, getters, or in case of ```MobX```, ```@computed``` functions to compute derived state from true state.
* If we are not using a state management library (meaning that if our app is very small), then we need to find the components which will host this state.
* If we are using a state management library (meaning that if our app is not very small), then we need to place this state into a store or multiple stores and pass the store to the component via its props, and fire actions on DOM events without using setState()
* Handling state with ```setState()``` inside an individual component is simple, however, when there is a component that depends on another component's state, things get complicated. Without using a state management library like ```Redux``` or ```MobX```, a common way to share state between components is called lifting the state up to a common ancestor component. Here we put the state changing code on the common ancestor component, and share the code (callback function) and shared state down the tree to the other components through their props. Inside the child components, we bind to this shared state passed on props, and call the callback function passed on props to change this state and trigger other components which depend on this state. As the app grows larger, this way of sharing state leads to complex and unmaintainable code and it is hard to find dependent components inside the codebase.

## 12. State management with MobX:
* ```MobX``` library offers a simple, object-oriented way to manage state, using observables, observers, and optional actions.
* With ```MobX```, we do not initialize the component's state property, instead, we mark the state variable with the ```@observable``` annotation.
* Inside an event handler, we do not use ```setState()``` to change state, instead, we change it like changing a regular javascript primitive value, object or array. The observable variables should be inside an object, like a component or store object, because they are implemented auto-magically internally with property getters and setters. We also mark the component or components which uses/depends on this state variable with the ```@observer``` annotation.
* We don't have to place the state variables inside a component, we can put it inside a store object and we can structure the code anyway we want.
* We can also use computed functions which change depending on an observable state variable (like fullname depending on firstname and lastname variables), and mark it with the ```@computed``` annotation.
* Refer to [here](https://mobx.js.org/getting-started.html) for a quick tutorial on ```MobX```
* Refer to [here](https://mobx.js.org/best/store.html) for best practices on structuring stores (like a single UI store and multiple domain stores)

## 13. State management with Redux:

* ```Redux``` library offers an advanced, functional way to predictively manage state, using reducers and actions. It is the most popular state management library among React developers.
* With ```Redux```, we need to write more verbose code than we were doing with ```MobX```, because there is less magic but more trackable state interactions.
* With ```Redux```, the app state is kept in a single store as a tree of keys and data parts corresponding to these keys. We do not directly manipulate app state inside the components.
* Inside the component DOM events and lifecycle methods, we create (fire/dispatch) actions which describe what happened. An action is a serializable object with a string ```type``` field and zero or more data fields (data fields cannot be a ```function``` or ```Promise``` because it needs to be serializable). These actions are then processed by reducer functions, which return the new version of the corresponding state part, which then replaces that part of the app state inside the store.
* State of an app is managed in a central store, and is created from and fed by the combined root reducer, which is a combination of all the reducers in the app. Components can bind to parts of this app state by connecting to reducers responsible for that part of the app state, and can change parts of this app state by creating actions, which are dispatched to (caught up by) reducers which again return the changed state.
* Normally, actions are created by functions called action creators, which each returns an action. We call action creators to dispatch actions. The action creator function is conventionally named same as the action ```type```, in camel case.
* The ```Redux``` name comes from the combination of ```Reducer``` and ```Flux```, and it is an implementation of the ```Flux``` architecture with reducers.
* A reducer is a function which gives part of this central app state to any component which is interested in this part of state. We can write a reducer which returns some static data, like
  ```jsx
  //inside reducers/booksReducer.js
  export default function() {
    return [
       { id: 1, title: "some title" },
       { id: 2, title: "another title" },
       { id: 3, title: "some other title" }
    ]
  }
  ```
  Here, the ```booksReducer``` does not need any parameter, but reducers in general can take ```state``` and ```action``` as a parameter.
* Reducer functions are triggered by actions and normally return specific part of state data based on the action type, refer to actions below for more details.
* Components which are interested in any part of the central app state are called containers (or smart components), components which only bind to their props without communicating with the central app state are simply called components (or dumb components).
* A reducer is responsible for managing changes to some small state, and it defines its branch (part or slice) of the larger app state object tree (considering each property of every object inside the app state as a branch or slice). A reducer does not need to manage the whole subtree, it can call other reducers responsible for the branches of the subtree, and use their results to create the new subtree to return. Note that, instead of doing this manually, you can use ```combineReducers()```, which will do this automatically for the combined reducers.
* At the start of the app, all top-level reducers are combined into the single root reducer, by using ```combineReducers()```, like
  ```jsx
  //inside reducers/rootReducer.js
  const rootReducer = combineReducers(
    {
      books: booksReducer,
      selectedBook: selectedBookReducer,
      ...
    }
  );
  
  export default rootReducer;
  ```
  The root reducer is responsible for the single app state. Then we wrap the ```<App>``` component with a ```<Provider>```, and pass the app store created by ```createStore(rootReducer)```, like
  ```jsx
  <Provider store={createStore(rootReducer)}>
    <App/>
  </Provider>
  ```
* Note that, the root reducer resulting from ```combineReducers()```, automatically calls each combined reducer. So the above example is the same as
  ```jsx
  //inside reducers/rootReducer.js
  export default function(state, action) {
    let books = booksReducer(state.books, action);
    let selectedBook = selectedBookReducer(state.selectedBook, action);
    return { books, selectedBook };
  }
  ```
We can also use ```combineReducers()``` to combine lower level reducers, in order to decompose a large deep app state into more maintainable smaller units.
* If we want to load some persisted state at the startup (from a database, ```localStorage```, API, etc.), we can load initial data into a variable like ```persistedState```, and call ```createStore()``` like ```createStore(rootReducer, persistedState)```, so  that parts of state coming from ```persistedState``` will override the state coming from ```rootReducer```
* The ```Redux``` store has ```getState()``` (gets the store state), ```subscribe()``` (subscribes to state changes), and ```dispatch()``` (dispatches an action to all reducers) methods which implement a pub/sub pattern and action dispatching. The root ```<App>``` component subscribes to the store to re-render when any part of the state in the store changes (the store calls ```ReactDOM.render()``` each time state changes inside the store). However, we don't use these methods inside other components, instead, we use functions which come with ```react-redux``` to bind component properties to store state and to dispatch actions using action creators. We typically place all reducers inside a ```reducers``` folder, and all actions into an ```actions``` folder in the project structure.
* Inside the container, the props of the container are bound to a reducer (part of the central app state) by declaring a ```mapStateToProps()``` function, and calling ```connect()``` function, like
  ```jsx
  class BookList extends React.Component {
    render() {
      const items = this.props.bookCollection.map(item => <li key={item.id}>{item.title}</li>);
      return <ul>{items}</ul>;
    }
  }
  
  mapStateToProps(state) {
    return {
      bookCollection: state.books
    };
  }
  
  export default connect(mapStateToProps)(BookList);
  ```
  Here, ```mapStateToProps()``` binds the ```BooksList``` component ```props``` to the ```books``` part of the central app state. Whenever the ```books``` changes, ```BooksList``` will be re-rendered with new values on its ```props.bookCollection```. We can have multiple ```props``` to state members mappings inside the returned object. The call to ```connect()``` function turns the dumb ```BooksList``` component into a smart component (container).
* We can use computed functions inside ```mapStateToProps()``` to map computed state, like a filtered array, a sorted array, an array's length, etc. This way, as long as the computed value doesn't change, our component does not re-render. 
* We can also use the ```Reselect``` library to define memoized computed functions (called selectors) that only re-compute when their parameters change, and bind props to these selectors to increase performance and keep normalized state in the store. Selectors can also be combined. For example, we can define a selector ```visibleTodosSelector```, like
  ```jsx
  //inside selectors/visibleTodosSelector.js
  import { createSelector } from 'reselect'

  const getVisibilityFilter = state => state.visibilityFilter
  const getTodos = state => state.todos

  export const visibleTodosSelector = createSelector(
    [getVisibilityFilter, getTodos],
    (visibilityFilter, todos) => {
      switch (visibilityFilter) {
        case 'SHOW_ALL':
          return todos
        case 'SHOW_COMPLETED':
          return todos.filter(t => t.completed)
        case 'SHOW_ACTIVE':
          return todos.filter(t => !t.completed)
      }
    }
  )
  ```
  and we can define a combined selector ```visibleTodosFilteredByKeywordSelector```, like
  ```jsx
  //inside selectors/visibleTodosFilteredByKeywordSelector.js
  import { createSelector } from 'reselect'

  const getKeyword = state => state.keyword

  export const visibleTodosFilteredByKeywordSelector = createSelector(
    [getVisibleTodos, getKeyword],
    (visibleTodos, keyword) =>
      visibleTodos.filter(todo => todo.text.indexOf(keyword) > -1)
  )
  ```
  and we can connect it to a component, like
  ```jsx
  import { getVisibleTodosFilteredByKeyword } from '../selectors'

  const mapStateToProps = state => {
    return {
      todos: getVisibleTodosFilteredByKeywordSelector(state)
    }
  }
  ```
Refer to [here](https://github.com/reduxjs/reselect) for details.
* Any event which needs to change part of the app state, creates an action which is an object with a string action type and various data members, like
  ```jsx
  //inside actions/selectBook.js
  export function selectBook(book) {
    return { type: "SELECT_BOOK", book: book };
  }
  ```
* Each action can be processed by (dispatched to) all the reducers in the app, and a reducer may respond (return specific date) if it is interested in the action, this way changing parts of the central app state. So a reducer is typically a switch statement with cases for each action type it is interested in to respond with a state change.
* Inside the container, to ensure the dispatching of an action to all reducers, we declare a ```mapDispatchToProps()``` function, and calling ```connect()``` function, like
  ```jsx
  class BookList extends React.Component {
    render() {
      const items = this.props.bookCollection.map(item => 
	<li key={item.id} onClick={(e) => this.props.selectBook(item)}>{item.title}</li>
      );
      return <ul>{items}</ul>;
    }
  }
  
  mapStateToProps(state) {
    return {
      bookCollection: state.books
    };
  }

  mapDispatchToProps(dispatch) {
    return bindActionCreators({selectBook: selectBook}, dispatch);
  }
  
  export default connect(mapStateToProps, mapDispatchToProps)(BookList);
  ```
  Here, ```mapDispatchToProps()``` allows dispatching of selectBook action to all reducers, and also adds ```selectBook``` on the ```BooksList``` component ```props```, so that we can call it like ```this.props.selectBook(...)``` to create a ```"SELECT_BOOK"``` action. We can have multiple ```props``` to ```action``` mappings inside the first parameter to ```mapDispatchToProps()``` call. We also pass ```mapStateToProps``` and ```mapDispatchToProps``` as parameters to the ```connect()``` function call.
* There are three different ways to use the ```mapDispatchToProps()``` function, the first one is using ```bindActionCreators()``` as in the previous example given above. Second, we can use long manual mapping, like
  ```jsx
  mapDispatchToProps(dispatch) {
    return {
      selectBook: (item) => { dispatch(selectBook(item)); },
      addBook: (item) => { dispatch(addBook(item)); },
      deleteBook: (item) => { dispatch(deleteBook(item)); }
    };
  }
  ```
  and third, the simplest way is return an object with the action creators as its fields, like
  ```jsx
  mapDispatchToProps(dispatch) {
    return {
      selectBook,
      addBook,
      deleteBook
    };
  }
  ```
* We can still dispatch an action even without using the ```mapDispatchToProps()``` function, like ```this.props.dispatch(this.props.selectBook(item))```, but this is not recommended.
* We can define the reducer which responds to this "SELECT_BOOK" action, like
  ```jsx
  //inside reducers/selectedBookReducer.js
  export default function(state = null, action) {
    switch(action.type) {
      case "SELECT_BOOK":
        return action.book;
    }
    
    return state;
  }
  ```
  Here, the ```state``` parameter is not the whole central app state, instead it is the ```selectedBook``` part inside ```rootReducer```, which this reducer is responsible for, and it is the value that was previously returned by this reducer. Also, the initial parameter value ```state = null``` applies only to the initial first call where there is no previously set state and it comes as undefined. We can define the initial state value for ```selectedBook``` part this way.
* In ```Redux```, the parts of the app state returned by reducers should be immutable (should not be mutated). So, inside a reducer we should not return a mutated version of the state, instead, we should create a fresh new state and return that. If the state parameter of the reducer is an array or object, we need to create a new one with a new reference, and return that. Note that we do not need to deep clone the state parameter, meaning that we do not need to recreate the elements of the array or properties of the object, if the elements or properties did not change. We only need to recreate the state parameter array/object, and recreate its changed elements/properties. For example, in a todo app let's have a todos reducer, its state parameter is the todo items array, with todo objects as elements of the array. Inside the reducer, we can mark a todo item complete by creating a new todo items array, copying the unchanged todo items objects exactly (without creating new todo objects), and creating a new todo item object with the changed status (completed), like
  ```jsx
  //inside reducers/todosReducer.js
  export default function(state = [], action) {
    switch (action.type) {
      case ADD_TODO:
        return [
          ...state,
          {
            text: action.text,
            completed: false
          }
        ];
      case TOGGLE_TODO:
        return state.map((todo, index) => {
          if (index === action.index) {
            return Object.assign({}, todo, {
              completed: !todo.completed
            });
          }
          else
	    return todo;
        })
      default:
        return state;
    }
  }
  ```
* Reducers should be pure functions, meaning that their result value only depends on their inputs (function parameters), and they should not mutate their input parameters either. They should be deterministic and return the same result when called with the same parameters, so they should not generate random numbers or ids, or get the current date. They should also not have side-effects, like making a database or network API call, or print something to the screen, or log to the console. We should do the nondeterministic and side-effect operations in the action creators instead and pass the side-effects to the reducer inside its action parameter, like a client-side or server-side generated guid or timestamp, or fetched values over a network API (get, post, put, etc.), etc. Generic operations like logging, should be carried out inside ```Redux``` middleware functions, which intercept all the reducers.
* The reason behind reducers being pure functions is because, the ```connect()``` mechanism does a shallow comparison, it checks the reference of the state slice returned from the reducer to decide if a re-render is necessary. If the state slice is mutated (its reference is same yet its content is changed), Redux will not notice the change and will not re-render the component. The reason for not mutating inner parts of the state returned by reducers, is because if we mutate it, we will lose the history of the state changes and lose the ability to track and debug state.
* When we need to change an inner nested member of the slice state, we need to clone both that member and all its parents, else Redux will not notice the change and re-render the connected component. For example, to change the value of state.a1.b1 inside a reducer, we need to clone and change b1, then clone a1, and clone the state parameter. We don't need to clone the other members of the state, like state.a2 or state.a2.b1, so these can keep the same references as before. Note that it is not recommended to store deeply referenced objects inside state. We can clone members and merge them with the spread operator ```...``` and object literal syntax ```{``` ```}```, like
  ```jsx
  //inside reducers/myDeepReducer.js
  export default function(state = {}, action) {
    switch(action.type) {
      case "CHANGE_B1":
        let a1 = {...state.a1, {b1: action.newB1}};
        return {...state, {a1: a1}}; // or in shorthand notation {...state, a1}
	}
    }
    
    return state;
  }
  ```
* The Redux state (and thus the reducers) should be organized according to what the domain logic (data coming from the server) and app logic (ajax pending state, etc.) needs. It should not be organized according to what the UI (display logic) needs. It is better to keep the state normalized, keep a single source of truth for each entity, and keep ids as a reference to other entities rather than those objects themselves.
* We can define the reducer which responds to this "SELECT_BOOK" action, like
  ```jsx
  //inside reducers/selectedBookReducer.js
  export default function(state = null, action) {
    switch(action.type) {
      case "SELECT_BOOK":
        return action.book;
    }
    
    return state;
  }
  ```
* We can bind a simple ```BookDetails``` component to the ```selectedBook``` part of the app state, like
  ```jsx
  class BookDetails extends React.Component {
    render() 
      if (!this.props.book)
        return <div>No book selected..</div>;
	  
      return <div>{this.props.book}</div>;
    }
    
    //binds the BookDetails component props to the selectedBook part of the central app state
    //whenever the selectedBook change, BookDetails will be re-rendered with new values on its book props
    mapStateToProps(state) {
      return {
        book: state.selectedBook
      };
    }
  }
    
  //turns the dumb component into a smart component (container)
  export default connect(mapStateToProps)(BookDetails);
  ```
* When using ```Redux```, we don't have to keep all state in the store, we can use route parameters and query strings that come from react router, or component state to keep temporary state. For example, we can use an ```:id``` route parameter inside the ```BookDetails``` container which is associated with a ```<Route>```, like
  ```jsx
  <Route path="/books/:id" component={BookDetails} />
  ```
  and assuming ```state.allBooks``` is an object with an ```id``` field which maps to the book with that id,
  ```jsx
  class BookDetails extends React.Component {
    render() 
      if (!this.props.bookId)
        return <div>No book selected..</div>;
	  
      return <div>{this.props.books[this.props.bookId].name}</div>;
    }
    
    mapStateToProps(state, ownProps) {
      return {
        books: state.allBooks,
	bookId: ownProps.params.id
      };
    }
  }
    
  export default withRouter(connect(mapStateToProps)(BookDetails));
  ```
* We can define operations to put state into and get state out of ```localStorage```, like
  ```jsx
  //inside localStorageState.js
  export function getStateFromLocalStorage() {
    try {
      const str = localStorage.getItem("state");
      if (str === undefined)
        return undefined;
      
      return JSON.parse(str);
    }
    catch(err) {
      return undefined;
    }
  }
  
  export function setStateToLocalStorage(state) {
    try {
      const str = JSON.stringify(jsonState);
      localStorage.setItem("state", str);
    }
    catch(err) {
      //log the error
    }
  }
  ```
  then load initial state from ```localStorage``` and update ```localStorage``` any time the store state changes, like
  ```jsx
  const persistedState = getStateFromLocalStorage();
  const store = createStore(rootReducer, persistedState)
  store.subscribe(() => setStateToLocalStorage(store.getState()));
  ```
  This way we don't lose state even when the user refreshes the page.
* The way with the least boilerblate to bind redux state and actions to props is like
  ```jsx
  import { connect } from 'react-redux'
  import { increment, decrement, reset } from './actionCreators'

  //Counter component
  //const Counter = ...

  const mapStateToProps = (state /*, ownProps*/) => {
    {
      counter: state.counter
    }
  }

  const mapDispatchToProps = { increment, decrement, reset }

  export default connect(
    mapStateToProps,
    mapDispatchToProps
  )(Counter)
  ```
* In summary, there are 8 steps when initially setting up ```Redux```:
    1. Create action
    2. Create reducer
    3. Create root reducer
    4. Configure store
    5. Instantiate store
    6. Connect component
    7. Pass props via connect
    8. Dispatch action
* In summary, there are 4 steps when adding a feature:
    1. Create action
    2. Enhance reducer
    3. Connect component
    4. Dispatch action
    
## 14. Calling an async API with Redux and redux-promise:
* We should not call an API inside a reducer function, because reducer functions should be pure functions (the result should only depend on the function arguments, and there should be no side effects).
* We can call an asynchronous API inside the component lifecycle methods and dispatch an action when the promise resolves and returns a result, like
  ```jsx
  //inside component
    componentDidMount() {
      axios.get(...someURL...)
      .then(weather => this.props.dispatch(fetchWeather(this.props.city, weather)));
  ```
  and the action, like
  ```jsx
  //inside actions/fetchWeather.js
  export function fetchWeather(city, weather) {
    return { type: "FETCH_WEATHER", city: city, weather: weather };
  }
  ```
* We can also chain promises and dispatch action in each chain, like
  ```jsx
  //inside actions/showNotificationWithTimeout.js
  let nextNotificationId = 0
  export function showNotificationWithTimeout(dispatch, text) {
    const id = nextNotificationId++
    dispatch(showNotification(id, text))

    setTimeout(() => {
      dispatch(hideNotification(id))
    }, 5000)
  }
  ```
  and dispatch it inside a component, like
  ```jsx
  // component.js
  showNotificationWithTimeout(this.props.dispatch, 'You just logged in.')
  ```
  However, in order to call ```dispatch()```, any component needs to be a container, meaning it needs to be a connected component.
* Another way of calling an asynchronous API, is calling it inside the actions, but then we cannot return the result, only the promise. For the reducers to work with an action that returns a promise, we need the ```redux-promise``` middleware. A middleware is a function that sits between actions and reducers, and can modify an action or even cancel it before it reaches the reducers. 
* We can send the promise returned from the async API, such as ```axios.get(someURL)```, as the payload data in the action. ```redux-promise``` will just wait until the promise resolves and change the promise payload on the action and turn it into the result data coming from the promise, so that the reducers only see the result data, not the promise payload on their action parameter. We can code the action creator, like
  ```jsx
  //inside actions/fetchWeather.js
  export function fetchWeather(city) {
    return { type: "FETCH_WEATHER", weather: axios.get(...someURL...) };
  }
  ```
  and the reducer, like
  ```jsx
  //inside reducers/fetchedWeatherReducer.js
  export default function(state = null, action) {
    switch(action.type) {
      case "FETCH_WEATHER":
        return action.weather;
    }
    
    return state;
  }
  ```

## 15. Chaining async calls with Redux Thunk
* To chain actions which return promises, or to chain async calls inside an action, or to catch errors from an async API call in an action, we need to use the ```redux-thunk``` library.
* With ```redux-thunk```, inside our action creator, we can return a function which takes ```dispatch``` and ```getState``` as parameters and returns an action. ```dispatch``` is the function which actually dispatches an action to all reducers. We can create the same action as in the previous example, but this time returning a function rather than a promise, like
  ```jsx
  //inside actions/fetchWeather.js
  export function fetchWeather(city) {
    const promise = axios.get(...someURL...);
    return (dispatch) => {
      promise.then(data => dispatch({ type: "FETCH_WEATHER", weather: data }));
    }
  }
  ```
* To display a busy indicator and to display errors from an API call inside a component, we can make multiple dispatches, first with a "FETCH_WEATHER_BUSY", then on success a "FETCH_WEATHER_SUCCESS", and in case of errors a "FETCH_WEATHER_ERROR", and we can even cache fetched data, like
  ```jsx
  //inside actions/fetchWeather.js
  export function fetchWeather(city) {
    const promise = axios.get(...someURL...);
    return function(dispatch, getState) {
      const { weather } = getState();
      //no action necessary if there is cached data
      if (weather[city])
        return;

      dispatch({ type: "FETCH_WEATHER_BUSY", city: city });

      promise.then(data => dispatch({ type: "FETCH_WEATHER_SUCCESS", weather: data }),
                   error => dispatch({ type: "FETCH_WEATHER_ERROR", error: error, city: city }));
    }
  }
  ```
  and inside the ```WeatherInfo``` component, like
  ```jsx
  import { Component } from 'react'
  import { connect } from 'react-redux'
  import fetchWeather from './actions/fetchWeather'

  class WeatherInfo extends Component {
    componentDidMount() {
      this.props.dispatch(fetchWeather(this.props.city));
    }

    componentDidUpdate(prevProps) {
      if (prevProps.city !== this.props.city) {
        this.props.dispatch(fetchWeather(this.props.city));
      }
    }

    render() {
      if (this.props.isBusy)
        return <p>Loading...</p>;
	
      if (this.props.error)
        return (
          <p>Error!</p>
          <p>{this.props.error}</p>
        );
      
      return (
        <p>{this.props.weather.city}</p>
        <p>{this.props.weather.temperature}</p>
      );
    }
  }
  
  mapStateToProps(state) {
    return {
      weather: state.weather,
      isBusy: state.isBusy,
      error: state.error
    };
  }
  
  export default connect(mapStateToProps)(WeatherInfo);
  ```
## 16. Integrating React Router with Redux:
* If we are using create-react-app, we won't need to configure a fallback url. But if we are serving index.html from express, we need a server-side route, like:
  ```jsx
  app.get('/*', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'))
  })
  ```
* We can import ```BrowserRouter``` and ```Provider```, like
  ```jsx
  import { BrowserRouter as Router, Route } from 'react-router-dom'
  import { Provider } from 'react-redux'
  ```
  and wrap our ```App``` with ```Provider``` and ```BowserRouter```, and define ```:filter``` on the route, like
  ```jsx
  import React from 'react'
  import PropTypes from 'prop-types'
  import { Provider } from 'react-redux'
  import { BrowserRouter as Router, Route } from 'react-router-dom'
  import App from './App'

  const Root = ({ store }) => (
    <Provider store={store}>
      <Router>
        <Route path="/:filter?" component={App} />
      </Router>
    </Provider>
  )

  Root.propTypes = {
    store: PropTypes.object.isRequired
  }

  export default Root
  ```
* Our startup ```index.js``` will be, like
  ```jsx
  import React from 'react'
  import { render } from 'react-dom'
  import { createStore } from 'redux'
  import todoApp from './reducers'
  import Root from './components/Root'

  const store = createStore(todoApp)

  render(<Root store={store} />, document.getElementById('root'))
  ```
* And our components can be, like
  ```jsx
  const App = ({ match: { params } }) => {
    return (
      <div>
        <AddTodo />
        <VisibleTodoList filter={params.filter || 'SHOW_ALL'} />
        <Footer />
      </div>
    )
  }
  
  const FilterLink = ({ filter, children }) => (
    <NavLink
      to={filter === 'SHOW_ALL' ? '/' : `/${filter}`}
      activeStyle={{
        textDecoration: 'none',
        color: 'black'
      }}
    >
      {children}
    </NavLink>
  )

  const Footer = () => (
    <p>
      Show: <FilterLink filter={VisibilityFilters.SHOW_ALL}>All</FilterLink>
      {', '}
      <FilterLink filter={VisibilityFilters.SHOW_ACTIVE}>Active</FilterLink>
      {', '}
      <FilterLink filter={VisibilityFilters.SHOW_COMPLETED}>Completed</FilterLink>
    </p>
  )
  ```
  and the ```VisibleTodoList``` component can be connected to Redux state and Route filter, like
  ```jsx
  const mapStateToProps = (state, ownProps) => {
    return {
      todos: getVisibleTodos(state.todos, ownProps.filter) // previously was getVisibleTodos(state.todos, state.visibilityFilter)
    }
  }
  ```

## 17. React-bootstrap components:
* Bootstrap components originally require jQuery, but ```react-bootstrap``` offers these as React components without the need for jQuery, refer to [here](https://react-bootstrap.github.io) for details and [here](https://blog.logrocket.com/how-to-use-bootstrap-with-react-a354715d1121) for a quick tutorial (also shows usage of ```reactstrap```, an alternative library for using ```bootstrap``` with React)

## 18. Hands-on project - CRUD app:
* I have created [here](https://github.com/kdakan/react-redux-crud-app) an app which demonstrates most of the concepts, using Redux. This app offers paginated and searchable lists, edit forms, client-side routing, Redux actions and reducers, and an API layer, with Bootstrap components like date picker and notifications. There is another version of the same app without Redux [here](https://github.com/kdakan/react-crud-app).
