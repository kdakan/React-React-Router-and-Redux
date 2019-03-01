# React Notes (ver.16.x):
-------------------------
These are some notes I've taken while following tutorials and reading docs and posts on React.

## Installation, creating, and running an app:
* ```npm install -g npm@latest```
* ```cd c:\repos```
* ```npx create-react-app MyReactApp``` (npx installs create-react-app if missing, you can ```npm install -g create-react-app``` and ```create-react-app MyReactApp``` instead)
* ```cd MyReactApp```
* ```npm install --save bootstrap``` (necessary if using bootstrap.css for styling)
* ```npm install --save prop-types``` (necessary if using PropTypes for type cecking on props)
* ```npm install --save react-router-dom``` (necessary if using the router)
* ```npm start``` (starts the app in development mode, with hot reload)
* ```npm test``` (runs jest tests in the app)
* ```npm build``` (builds the app for xcopy deployment to production)
* ```npm eject``` (ejects the app source from ```create-react-app```, if extra modifications on webpack config is needed, after ejecting, you cannot use create-react-app again for this app source)
* In VSCode editor, install ```Debugger for Chrome``` extension, and change the port to 3000 inside launch.json file, to debug inside the editor (without this, you can also debug inside Chrome)
* In Chrome, install ```React Developer Tools``` extension, to see React component hierarchy, props and state inside Chrome F12 React tab
 
## Component, props, state, JSX, array rendering, etc.:
* A React app is a tree of components, which communicate with each other via their props, which can be of any type, including primitives, objects, arrays, sets, and callback functions
* There is a root App component, which hosts all other components, and is rendered on a ```div``` with ```id=app``` inside   ```index.html```, using 
  ```jsx 
  ReactDOM.render(<App>, document.getElementById("app"))
  ```
  (App and app naming is conventional, can be anything)
* Each component is preferably defined in a single file that exports it as an ES6 module, and can import other components or js modules
* Component names should begin with an uppercase character, else React may confuse it with regular HTML tags, like ```<Button>``` vs     ```<button>```
* Props and state changes trigger the ```render()``` method of components
* Functional components have a props argument and return rendered result in JSX syntax, like 
  ```jsx
  const MyComponent = (props) => {
    return (
      ...some JSX...
    );
  };
  ``` 
  or with an evetn handler like 
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
* Functional components do not use this keyword, do not have lifecycle methods, and cannot access refs
* Functional components do not have state, they are presentational components which use only their props, and only render result in JSX syntax
* Functional components that have inner functions (like event handler callback functions) can cause performance issues, so it is better to use class components if event handlers are needed
* Class components extent React.Component, have a ```render()``` function which returns rendered result in JSX syntax, if null is returned, no HTML is generated
* Class components can have a ```constructor(props)```, should first call ```super(props)```, and later can initialize component's   state, like 
  ```jsx
  this.state = {someState: ..., someOtherState: ...}
  ```
* Class components can initialize their state even without a constructor, in shorter class field syntax, omitting this, like 
  ```jsx
  state = {someState: ..., someOtherState: ...}
  ```
* Class components which hold state without much JSX markup are also called container components and can render other container components or presentational components
* React uses virtual DOM, so ```render()``` method does not update all the HTML for the component, but only updates the changed parts (it diff'es the current props and state with the next props and state and if there is a chang
* Even when using virtual DOM, unnecessary render calls cost some performance because of the diff'ing of the current virtual DOM and next virtual DOM, therefore it is more performant to check inside ```shouldComponentUpdate()```if the current props and state are different than next props and state and return false if not (extending component from ```React.PureComponent``` instead of ```React.Component``` will do this by shallow comparison of props and state)
* Components can use functions that return partial rendered result in JSX syntax, can also store this JSX in a variable, and use them to compose into a larger rendered JSX result, if null is returned, no HTML is generated
* Components cannot not change their props, props are immutable
* Components use 
  ```jsx
  this.setState({someState: ...})
  ```
  to change their state, they do not assign values to the state directly
* React can detect changes faster when an array state member is not mutated but assigned a new or cloned array, so instead of   
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.push(item)})
  ```
  it is better to use 
  ```jsx
  this.setState({someArrayState: this.state.someArrayState.concat([item])})
  ```
  to clone an array, ```someArray.slice()```, ```[].concat(someArray)```, or spread operator ```[...someArray]``` can be used
* State can be set in multiple steps, so that all state object members do not have to be given, like 
  ```jsx
  this.setState({someState: ...})
  ``` 
  and later 
  ```jsx
  this.setState({someOtherState: ...})
  ```
* JSX is transpiled to js by Babel, like 
  ```jsx
  React.createElement(...)
  ```
* Items in an array are rendered by using ```Array.map()``` function and mapped to JSX elements, elements rendered this way should each have unique key property, do not use array index as key because as items are inserted or deleted in the middle of the array, index of existing items change and this confuses React and causes rendering issues
* State is private to each component, to update state of a parent component, the child component should use the callback function passed to it via props
* Components can change their state or their parent components' state by calling callback functions via props, on events like button click, link click, form submit, timer tick, ajax requests, etc.
* Css class attribute is used as ```className``` in JSX and HTML label attribute for is used as ```htmlFor```, because JSX is compiled to js, and class and for are reserved words in js
* Binding syntax is placing simple js statements inside {}, props object is references by ```{this.props.someProp}```, state objects   referenced by ```{this.state.someState}```, callback function prop can be referenced by 
  ```jsx
  onClick={(e) => this.props.someParentFunction(e)}
  ```
* There are other alternative ways to bind callback functions, see the Binding section
* UI container components such as panel or dialog components, can render whatever is passed inside the opening and closing tags, using   ```this.props.children```, like 
  ```jsx
  <Dialog>
    <input .../>
    <button>OK</button>
  </Dialog>
  ``` 
  and Dialog component's render method 
  ```jsx
  render() {
    return (
      <div className="dialog-frame">{this.props.children}</div>
    )
  }
  ```
* When structuring an app into components, it is easier to start with components without state, DOM events, and callback functions, and visualize a static page, and later add state, DOM events, and callback functions later one by one
* It is better to place functions that do not use props or state or this, outside the component function or class
 
## Importing various file types:
* React components can be imported like 
  ```jsx import MyComponent from './components/myComponent'``` 
  (loads either from ```./components/myComponent.js``` file or from ```index.js``` inside ```./components/myComponent``` folder)
* Css files used inside a component can be imported like 
  ```jsx
  import './myComponent.css'
  ```
* Bootstrap.css file can be imported like 
  ```jsx
  import '../node_modules/bootstrap/dist/css/bootstrap.min.css'
  ```
* Css files used everywhere in the site can be added into main site index.html as a regular css file like 
  ```jsx
  <link rel="stylesheet" href="stylesheets/site.css">
  ```
* Image files used inside a component can be imported like 
  ```jsx
  import myImage './myImage.png'
  ``` 
  and used like 
  ```jsx
  <img src={myImage} />
  ```
 
## Binding this to event handlers (callback functions):
* React components can bind arrow functions to DOM events, like 
  ```jsx
  onClick={e => this.handleClick(e)}
  ```
  else have to either bind this to function explicitly like 
  ```jsx
  onClick={this.handleClick.bind(this)}
  ``` 
  or define the handler as an arrow function like 
  ```jsx
  handleClick = () {...}
  ``` 
  for the component fields be accessible via this keyword inside the function
* There are at least 5 different ways to bind this to event handler functions, some may cause performance issues, so refer to https://medium.freecodecamp.org/react-binding-patterns-5-approaches-for-handling-this-92c651b5af56 for details

## Lifecycle methods:
* Class components can have lifecycle methods like ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```, etc.
* Order of execution is: ```constructor()```, ```getDerivedStateFromProps()```, ```componentWillMount()```, ```render()```,   ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentWillUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```
* Ajax requests to get initial data should be done inside componentDidMount()
* To use async/await pattern to call an async api (async api meaning the api returns a Promise instead of data result), use ```asycn```   before ```componentDidMount()``` and use ```await``` before the api call, like 
  ```jsx
  async componentDidMount() { 
    let data = await api.fetchData(); 
    this.setState({data: data});
  }
  ```
* If manipulating the DOM, or polling periodically for new data, we can use 
  ```jsx
  this.timer = setInterval(...)
  ``` 
  to fetch new data inside ```componentDidMount()```
* Cleanup actions like ```clearInterval(this.timer)``` should be done inside ```componentWillUnmount()```
* ```shouldComponentUpdate()``` can sometimes be used to increase rendering performance, normally React components re-render when props   or state have changed
* To catch errors inside the components and log them, use 
  ```jsx
  componentDidCatch(error, info) {
    console.log(error, info);
  }
  ```
  in the root component
* Refer to https://blog.bitsrc.io/react-16-lifecycle-methods-how-and-when-to-use-them-f4ad31fb2282 for more lifecycle methods and details
 
## Working with form inputs:
* React DOM events are synthetic events, which wrap DOM events in a cross-browser way
* DOM event handlers need to call ```e.preventDefault()``` or ```e.stopPropogating()```, to prevent normal event actions and bubbling, like in submitting a form or navigating to a link (unlike returning false in jquery which calls both ```e.preventDefault()``` and ```e.stopPropogating()``` in effect)
* Form inputs can be bound to a state property, like 
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
* Instead of binding an input to a state property, we can also use the input value using by assigning a ref property to the input and use the input value through this ref
* To use a rendered DOM element inside an event handler function, we can assign this element a ref property, like 
  ```jsx
  <input ref={element => this.nameInput = element}
  ```
  and use it inside a form submit handler function, like 
  ```jsx
  let newName = this.nameInput.value;
  ```
* Form validation such as required field, can be done like 
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

## Type-checking on props:
* To check for missing or mis-typed props passed at development runtime, add a propTypes member on the component class or function, with prop fields and their types, like
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

## Routing:
* Router is needed for the browser back/forward buttons to work properly without reloading the index.html from the server and losing all state
* JSX inside the render() method of the root App component should be surrounded by <BrowserRouter> component like ```<BrowserRouter>...</BrowserRouter>```
* Anchor links can be like 
  ```jsx
  <Link to='/'>Home</Link>
  ```
  or
  ```jsx
  <Link to='/search'>Search</Link>
  ```
* Routes can be assigned to components by placing <Route> components inside a <Switch> component so that these components are rendered in place of <Switch>, like
   ```jsx
    <Switch>
     <Route exact path='/' component={Home} />
     <Route path='/list' component={List} />
     <Route path='/search' component={Search} />
   </Switch>
  ```
* Routes can also be nested so that these nested components will be rendered inside (as children of) the App component, like ```App``` component ```render()``` method 
  ```jsx
  render() {
    return (
      <MyNav/>
      <div className="container-fluid">{this.props.children}</div>
    )
  }
  ```
   and routes defined in a module file called ```routes.js``` like
   ```jsx
   const routes = (
   <Route path='/' component={App} >
     <IndexRoute component={Home} />
     <Route path='/list' component={List} />
     <Route path='/search' component={Search} />
   </Route>);
   export default routes;
   ```
   and ```MyNav``` component render method like 
   ```jsx
   render() {
     return (
       <nav>
	 <IndexLink to="/" activeClassName="active">Home</IndexLink>{" | "}
	 <Link to="/list" activeClassName="active">List</Link>{" | "}
	 <Link to="/search" activeClassName="active">Search</Link>
       </nav>
     )
   }
   ```
   and the root component declared as Router instead of App, like 
   ```jsx
   ReactDOM.render(<Router history={browserHistory} routes={routes} />, document.getElementById("app"));
  ``` 
  (```browserHistory``` means use HTML5 push state instead of # addresses)
   
## Context api:
* Global state (language, user info, theme, etc.) is usually stored in the root component state properties
* Normally, to pass data from a top level component to a bottom level component, should be passed through props of all the components in between
* Using Context api, top level components can pass data to bottom level components without using the bottom level components' props
* To share global state, first a context is created with a default value like const ```LanguageContext = React.createContext('en');``` in its own module file and exported like export default LanguageContext;
* Then, it is imported in any component that provides its value or consumes its value, refer to https://reactjs.org/docs/context.html for examples
* Context can hold any js object, not only primitives

## Async, await, and Promise:
* async function wraps its return value in a Promise object, so ```async f() {... return 123;}``` is the same as ```f() {... return new Promise(...resolve(123)...);}```
* This is similar to a C# async method returning a ```Task<int> object``` wrapping its return value of type ```int```
* await resolves a Promise object, so await p resolves the Promise object p, and await f() resolves the Promise returned by function f()
* When an async function returns a Promise, js flattens it out to a single Promise, so ```async f() {... return new Promise(...resolve(123)...);}``` is the same as ```async f() {... return 123;}```
* async and await are frequently used together in the async/await pattern like ```x = await f();``` but using with promises, they don't have to be used together all the time
* Refer to https://dev.to/codeprototype/async-without-await-await-without-async--oom for more details
* To run two async functions in parallel, you cannot use ```await f1(); await f2();```, instead either use ```Promise.all()``` or ```p1 = f1(); p2 = f2(); await p1; await p2;``` (starts f1() and f2() and resolves both later
* Refer to https://medium.freecodecamp.org/avoiding-the-async-await-hell-c77a0fb71c4c for more details
 
## Fetch api examples:
* Refer to https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch for more details and examples
 ```jsx
 //basic get
 fetch('https://api.github.com/users/KrunalLathiya')
 .then(response => response.json())
 .then(data => {
   console.log(data) // Prints result from `response.json()` in getRequest
 })
 .catch(error => console.error(error))

 //cors
 fetch('https://api.github.com/users/KrunalLathiya', {
   credentials: 'include', // Useful for including session ID (and, IIRC, authorization headers)
 })
 .then(response => response.json())
 .then(data => {
   console.log(data) // Prints result from `response.json()`
 })
 .catch(error => console.error(error));
 
 //post data
 fetch('https://jsonplaceholder.typicode.com/users', {
   headers: { "Content-Type": "application/json; charset=utf-8" },
   method: 'POST',
   body: JSON.stringify({
     username: 'Elon Musk',
     email: 'elonmusk@gmail.com',
   })
 })
  
 //post data
 fetch('https://appdividend.com/api/v1/users', {
   credentials: 'same-origin', // 'include', default: 'omit'
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: JSON.stringify({user: 'Krunal'}), // Coordinate the body type with 'Content-Type'
   headers: new Headers({
     'Content-Type': 'application/json'
   }),
 })
 .then(response => response.ok() response.json())
 .then(data => console.log(data)) // Result from the `response.json()` call
 .catch(error => console.error(error))

 //delete data
 fetch('https://jsonplaceholder.typicode.com/users/1', { 
   method: 'DELETE' 
 });

 //use async/await
 let res = await fetch('https://api.github.com/users/KrunalLathiya');
 let data = await res.json();
 console.log(data);
 
 //upload file
 const formData = new FormData()
 const fileField = document.querySelector('input[type="file"].avatar')
 formData.append('username', 'abc123')
 formData.append('avatar', fileField.files[0])
 fetch('https://appdividend.com/api/v1/users', {
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: formData  // Coordinate the body type with 'Content-Type'
 })
 .then(response => response.json())
 
 //upload multiple files
 <input type='file' multiple class='files' name='files' />
 const formData = new FormData()
 const fileFields = document.querySelectorAll('input[type="file"].files')
 // Add all files to formData```
 [].forEach.call(fileFields.files, f => formData.append('files', f))
 // Alternatively for PHP peeps, use `files[]` for the name to support arrays
 // Array.prototype.forEach.call(fileFields.files, f => formData.append('files[]', f))
 fetch('https://appdividend.com/api/v1/users', {
   method: 'POST', // 'GET', 'PUT', 'DELETE', etc.
   body: formData  // Coordinate the body type with 'Content-Type'
 })
 .then(response => response.json())
 .then(data => console.log(data))
 .catch(error => console.error(error))
 
