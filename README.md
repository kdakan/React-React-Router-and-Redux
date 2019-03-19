# React with React Router 4, Redux and MobX Tutorial
This is an extensive tutorial on React v.16 with React Router v.4, and using Redux for state management. This can also serve as a mini-reference. This tutorial will take you from beginner level to intermediate/advanced level and covers enough breadth and depth to get you going with most of your projects.

Before beginning, make sure you have some knowledge of ES6. I have a tutorial here at https://github.com/kdakan/ES6-Tutorial in case you want to brush up on ES6.

Unlike Angular, React only handles the view part of the MV* architecture. There is no controller, service, or dependency injection. Programmers mainly utilize components with props and state and use a global state manager like ```Redux``` or ```Mobx``` to simplify handling and sharing state between components across the app. 

## Table of contents
* [Installation](#installation)
* [Creating and running an app](#creating-and-running-an-app)
* [Components](#components)
* [Importing components, CSS, and images](#importing-components-css-and-images)
* [Binding this to event handlers](#binding-this-to-event-handlers)
* [Lifecycle methods](#lifecycle-methods)
* [Working with forms and inputs](#working-with-forms-and-inputs)
* [Type-checking on props](#type-checking-on-props)
* [Routing](#routing)
* [Context api](#context-api)
* [State management](#state-management)
* [State management with ```MobX```](#state-management-with-mobx)
* [State management with ```Redux```](#state-management-with-redux)
* [Calling an async api with ```Redux```](#calling-an-async-api-with-redux)
* [Chaining async calls with ```Redux Thunk```](#chaining-async-calls-with-redux-thunk)
* [React-bootstrap components](#react-bootstrap-components)
* [Hands on project: ```pluralsight.com``` clone](#hands-on-project-pluralsightcom-clone)

## Installation:
* We can use the official CLI ```create-react-app``` to start developing in React, without getting lost in ```webpack``` and ```babel``` tool configuration.
* ```npm install -g npm@latest```
* ```cd c:\repos```
* ```npx create-react-app MyReactApp``` (npx installs create-react-app if missing, you can ```npm install -g create-react-app``` and ```create-react-app MyReactApp``` instead)
* ```cd MyReactApp```
* ```npm install --save bootstrap``` (if using bootstrap.css for styling)
* Note that with npm ver. 5, there is no need to give the ```--save``` option, ```npm install somePackage``` works the same way as ```npm install --save somePackage```, so that it adds ```somePackage``` to the ```dependencies``` section in the ```packages.json``` file.
* ```npm install --save prop-types``` (if using ```PropTypes``` for type cecking on props)
* ```npm install --save react-router-dom``` (if using the router)
* ```npm install --save redux redux-thunk``` (if using ```redux``` for state management)
* In ```VSCode``` editor, install ```Debugger for Chrome``` extension, and change the port to 3000 inside launch.json file, to debug inside the editor. Without this extension, you can still debug inside the ```Chrome``` browser.
* In ```Chrome```, install ```React Developer Tools``` extension, to see React component hierarchy, props and state inside Chrome F12 React tab.

## Creating and running an app:
* ```npm start``` (starts the app in development mode, with hot reload, so that you can see the changes without restarting the app)
* ```npm test``` (runs ```jest``` tests in the app)
* ```npm build``` (builds the app for xcopy deployment to production)
* ```npm eject``` (ejects the app source from ```create-react-app```, use this if you need to change ```webpack``` config, after ejecting, you cannot use create-react-app again for the same app source)

## Components:
* A React app is a tree of components, which communicate with each other via their props, which can be of any type, including primitives, objects, arrays, sets, and callback functions.
* There is a root ```App``` component, which hosts all other components, and is rendered on a ```div``` with ```id=app``` inside   ```index.html```, using 
  ```jsx 
  ReactDOM.render(<App>, document.getElementById("app"))
  ```
  (```App``` and ```app``` naming is conventional, can be any name you wany)
* Preferably, we define each component in its own file that exports it as an ES6 module, and we can import other components or ES6 modules.
* Component names should begin with an uppercase character, else React may confuse it with regular HTML tags, like ```<Button>``` vs     ```<button>```.
* Props and state changes trigger the ```render()``` method of a component.
* Functional components have a props argument and return rendered result in JSX syntax, like 
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
* Class components extend ```React.Component```, have a ```render()``` function which returns rendered result in JSX syntax. If you return null, it generates no HTML.
* Class components can have a ```constructor(props)```, should first call ```super(props)```, and later can initialize component's   state, like 
  ```jsx
  this.state = {someState: ..., someOtherState: ...}
  ```
* Class components can initialize their state even without a constructor, in shorter class field syntax, omitting this, like 
  ```jsx
  state = {someState: ..., someOtherState: ...}
  ```
* Components which hold state and delegates rendering much of JSX markup to presentational components, are also called container components. Container components can render other container components and presentational components. Presentational components don't have state and just render JSX with props passed to them and call the callback functions (actions) again passed through their props. Container components are also called smart components, stateful components, or conroller views. Presentational components are also called dumb components, stateless components, or views. 
* Container components are usually connected to a state management library like ```Redux```, but presentational components are not. A React app is composed of low number of containers and high number of presentational components. Because presentational components are decoupled from state, they are more reusable and also simpler to use and understand.
* React uses virtual DOM, so ```render()``` method does not update all the HTML for the component, but only updates the changed parts. React diff'es the current props and state with the next props and state and renders if there is a change.
* Even when using virtual DOM, unnecessary render calls cost some performance because of the diff'ing of the current virtual DOM and next virtual DOM. Therefore it is more performant to check inside ```shouldComponentUpdate()```if the current props and state are different than next props and state and return false if not. Extending component from ```React.PureComponent``` instead of ```React.Component``` will do this by shallow comparison of props and state.
* Components can use functions that return partial rendered result in JSX syntax, can also store this JSX in a variable, and use them to compose into a larger rendered JSX result, if null is returned, no HTML is generated.
* Components cannot not change their props, props are immutable.
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
* To clone an array, ```someArray.slice()```, ```[].concat(someArray)```, or spread operator ```[...someArray]``` can be used. To clone an array and add an item, we can use ```someArray.concat([item])``` or ```[...someArray, item]```. To remove an item from an array, we would normally use ```someArray.splice(index, 1)```, but to clone and remove an item we use ```someArray.slice(0, index).concat(someArray.slice(index + 1))```, which clones elements up to index and clones elements from index + 1 up to end and concatenates them. This is the same as ```[...someArray.slice(0, index), ...someArray.slice(index + 1)]```. To increment an item in an array without mutating it, we can similarly use ```someArray.slice(0, index).concat(someArray[index] + 1).concat(someArray.slice(index + 1))```, or ```[...someArray.slice(0, index), someArray[index] + 1, ...someArray.slice(index + 1)]```. To do more complicated manipulations on a cloned array, we can use ```someArray.map(item => ...code that returns a cloned and modified element...)```.
* To clone an object (shallow copy, meaning that members are copied but not cloned), we can use ```Object.assign({}, someObject)``` or ```{...someObject}```. To clone an object and change a field, we can use ```Object.assign({}, someObject, { someField: someValue })``` or ```{...someObject, someField: someValue}```.
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
* We can render items in an array by using ```Array.map()``` function and map items to JSX elements. Items rendered this way should have unique key property. Do not use array index as key because as items are inserted or deleted in the middle of the array, index of existing items change and this confuses React and causes rendering issues.
* State is private to each component, to update state of a parent component, the child component should use the callback function passed to it via props.
* Components can change their state or their parent components' state by calling callback functions via props, on events like button click, link click, form submit, timer tick, ajax requests, etc.
* Css class attribute is used as ```className``` in JSX and HTML label attribute for is used as ```htmlFor```, because JSX is compiled to javascript, and class and for are reserved words in javascript.
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

## Importing components, CSS, and images:
* We can import React components, like 
  ```import MyComponent from './components/myComponent'``` 
  This code loads the ```MyComponent``` which is ```export default``` either from the ```./components/myComponent.js``` file or from the ```index.js``` file inside the ```./components/myComponent``` folder. 
* It is common to use features as parent folder names or container names as parent folder names and to place other related components inside subfolders. For example ```./components/search``` folder can have ```./components/search/filter/search-filter.js```, ```./components/search/panel/search-panel.js```, ```./components/search/panel/search-container.js```, ```./components/search/list/search-list.js```, ```./components/search/list/search-list-item.js``` components. There can also be a ```./components/search/index.js``` where each of the components are re-exported for ease of importing from the single location ```./components/search```, like
  ```jsx
  //inside ./components/search/index.js
  export from './filter/search-filter.js'
  export from './panel/search-panel.js'
  export from './panel/search-container.js'
  export from './list/search-list.js'
  export from './list/search-list-item.js'
  ```
  and ```SearchFilter``` default export inside ```./filter/search-filter.js```, like
  ```jsx
  //inside ./components/search/filter/search-filter.js
  export default class SearchFilter {
    ...
  }
  ```
  and imported inside ```./components/app.js```, like
  ```jsx
  //inside ./components/app.js
  import { SearchFilter, SearchContainer } from './search'
  ```
  or like
  ```jsx
  //inside ./components/app.js
  import SearchFilter from './search/filter/search-filter.js'
  import SearchContainer from './search/panel/search-container.js'
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
 
## Binding this to event handlers:
* We need to bind ```this``` to DOM event handler callback functions, in order to access component members such as ```this.props``` or ```this.state```
* The official recommended and most performant way is to bind ```this``` explicitly inside the constructor, like
  ```jsx
  constructor(props) {
    ...
    this.handleClick = this.handleClick.bind(this);
    ...
  }
  ```
  and use it inside ```render()```, like
  ```jsx
  onClick={this.handleClick}
  ```
* Another way is to to bind this explicitly inside ```render()```, like 
  ```jsx
  onClick={this.handleClick.bind(this)}
  ``` 
* Another common way is to wrap it inside an arrow function inside ```render()```, like 
  ```jsx
  onClick={e => this.handleClick(e)}
  ```
  or define the handler as an arrow function like 
  ```jsx
  handleClick = () {...}
  ```
  and use it inside ```render()```, like
  ```jsx
  onClick={this.handleClick}
  ``` 
* There are at least 5 different ways to bind this to event handler functions, some may cause performance issues, so refer to https://medium.freecodecamp.org/react-binding-patterns-5-approaches-for-handling-this-92c651b5af56 for details. You can always use the recommended explicit way (bind it inside the constructor) to play safe and keep performance high, without triggering unnecessary re-renders.

## Lifecycle methods:
* Class components can have lifecycle methods like ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```, etc.
* Order of execution is: ```constructor()```, ```getDerivedStateFromProps()```, ```componentWillMount()```, ```render()```,   ```componentDidMount()```, ```shouldComponentUpdate()```, ```componentWillUpdate()```, ```componentDidUpdate()```, ```componentWillUnmount()```, ```componentDidUnmount()```.
* We can make Ajax requests to get initial data inside ```componentWillMount()``` or ```componentDidMount()```.
* To use async/await pattern to call an async api (async api meaning the api returns a ```Promise``` instead of data result), we can use ```asycn```   before ```componentDidMount()``` and use ```await``` before the api call, like 
  ```jsx
  async componentDidMount() { 
    let data = await api.fetchData(); 
    this.setState({data: data});
  }
  ```
* We can mount a jQuery plugin inside ```componentDidMount()``` and destroy it inside ```componentWillUnmount()```.
* If we need to manipulate the DOM, or poll periodically for new data, we can use ```componentDidMount()```, like
  ```jsx
  this.timer = setInterval(...)
  ``` 
  to fetch new data inside ```componentDidMount()```.
* We should do cleanup actions like ```clearInterval(this.timer)``` inside ```componentWillUnmount()```.
* We can use ```shouldComponentUpdate()``` to increase rendering performance. Normally components re-render when props or state have changed.
* To catch errors inside the components and log them, we can use 
  ```jsx
  componentDidCatch(error, info) {
    console.log(error, info);
  }
  ```
  in the root ```App``` component.
* Refer to https://blog.bitsrc.io/react-16-lifecycle-methods-how-and-when-to-use-them-f4ad31fb2282 for more lifecycle methods and details.
 
## Working with forms and inputs:
* React DOM events are synthetic events, which wrap DOM events in a cross-browser way.
* We need to explicitly call ```e.preventDefault()``` or ```e.stopPropogating()``` inside a DOM event handler like submitting a form or navigating to a link, to prevent normal event actions and bubbling. This is unlike the case where jquery implicitly calls both ```e.preventDefault()``` and ```e.stopPropogating()``` if you return in an event handler.
* We can bind form inputs to a state property, like 
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
* Instead of binding an input to a state property, we can also use the input value using by assigning a ref property to the input and use the input value through the ref.
* To use a rendered DOM element inside an event handler function, we can assign a ref property to the element, like 
  ```jsx
  <input ref={element => this.nameInput = element}
  ```
  and use it inside a form submit handler function, like 
  ```jsx
  let newName = this.nameInput.value;
  ```
* We can also use a ```jQuery```, ```GoogleMaps``` or similar plugin, using the DOM element ref of an empty ```<div>```, and mount it inside the ```componentDidMount()``` method and unmount/destroy it inside the ```componentWillUnmount()``` method of our React component.
* It is recommended to use a ```<form>``` with a submit button (```<button type="submit">```), when we have inputs and a button to take action on the inputs. Because this way enter key acts as clicking the submit button by default. However we also need to have an ```onSubmit``` DOM event handler which calls ```e.preventDefault()``` to prevent server-side posting/reloading of the page.
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

## Type-checking on props:
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

## Routing:
* We need routing for the browser back/forward buttons to work properly and also allow deep linking via urls, without reloading the ```index.html``` from the server and losing all the state.
* We need server side routing/rendering to serve public pages for better SEO, and we need client routing/rendering to serve authorized content for better user experience (for faster transitions without loading the full page).
* We need at least one route on the server, to serve the ```index.html``` and its js and css files (the React app), like
  ```javascript
  app.all('*', express.static('public/index.html'));
  ```
* On this node.js express server app, the first request for any url in the site domain will first load the React app via ```index.html```,  and later requests will be handled by ```react-router-dom``` and React components.
* On a site which does not serve public pages via server side routing/rendering, the first page served will be the login/signup page, which can either be rendered on the server or the client. Authorized content can also be routed to a different subdomain like ```app.mysite.com```.
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
  Here, both the ```style``` and ```activeStyle``` props accept an object as a value, that is why ```{{``` ```}}``` is needed. The outer ```{}``` is for props binding syntax, the inner ```{}``` is for object literal syntax.
* We can declare a route and associate it with a component to render, like 
  ```jsx
  <Route exact path='/' component={Home} />
  <Route path='/search' component={Search} />
  ```
* ```Route``` component renders the associated component if the path matches the url, else renders ```null``` (renders no output).
* ```Redirect``` component declares a client side redirection, so ```Route``` and ```Redirect``` are simply placeholders for their associated components, like
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
* Multiple ```Route``` components can match a url path, like routes with both ```/``` and ```/list``` paths will match the url www.mysite.com/list. But we can force a single exact match by using ```exact``` keyword in the ```Route``` component.
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
* Components triggered (rendered) by a ```Route``` component receive extra ```props```, which are ```match```, ```location``` and ```history```
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
   
  ```const Master = ({ match }) => (...)``` means use the ```match``` field inside the ```props``` parameter, so it could also be coded like below
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

## Context api:
* Global state (language, user info, theme, etc.) is usually stored in the root component state properties.
* Normally, to pass data from a top level component to a bottom level component, we should pass it through props of all the components in between.
* Using Context api, top level components can pass data to bottom level components without using the bottom level components' props.
* To share global state, first we create a context with a default value like const ```LanguageContext = React.createContext('en');``` in its own module file and export it like ```export default LanguageContext;```
* Then, we import it in any component that provides its value or consumes its value, refer to https://reactjs.org/docs/context.html for examples.
* Context can hold any javascript object, not only primitives.
* It is advisable to use a state management library like ```Redux``` or ```MobX``` instead of the lower level Context api.

## State management:
* When starting a new app or a new component tree, it is easier to start without thinking about any state and interaction, and just breaking down the target HTML output we want, into dumb visual components with no state or DOM events. The root component of the app or the component tree can be fed through its props with static data for prototyping the visuals.
* Next, we should identify the changing parts of the data and turn these parts into state. How we implement state is different depending on whether we are using a state management library or not. In any case, we do not put derived values into their own state, instead we use functions, getters, or in case of ```MobX```, ```@computed``` functions to compute derived state from true state.
* If we are not using a state management library (meaning that if our app is very small), then we need to find the components which will host this state.
* If we are using a state management library (meaning that if our app is not very small), then we need to place this state into a store or multiple stores and pass the store to the component via its props, and fire actions on DOM events without using setState()
* Handling state with ```setState()``` inside an individual component is simple, however when there is a component that depends on another component's state, things get complicated. Without using a state management library like ```Redux``` or ```MobX```, a common way to share state between components is called lifting the state up to a common ancestor component. Here we put the state changing code on the common ancestor component, and share the code (callback function) and shared state down the tree to the other components through their props. Inside the child components, we bind to this shared state passed on props, and call the callback function passed on props to change this state and trigger other components which depend on this state. As the app grows larger, this way of sharing state leads to complex and unmaintainable code and it is hard to find dependent components inside the codebase.

## State management with ```MobX```:
* ```MobX``` library offers a simple object oriented way to manage state, using observables, observers, and optional actions.
* With ```MobX```, we do not initialize the component's state property, instead, we mark the state variable with the ```@observable``` annotation.
* Inside an event handler, we do not use ```setState()``` to change state, instead, we change it like changing a regular javascript primitive value, object or array. The observable variables should be inside an object, like a component or store object, because they are implemented auto-magically internally with property getters ans setters. We also mark the component or components which uses/depends on this state variable with the ```@observer``` annotation.
* We don't have to place the state variables inside a component, we can put it inside a store object and we can structure the code any way we want.
* We can also use computed functions which change depending on an observable state variable (like fullname depending on firstname and lastname variables), and mark it with the ```@computed``` annotation.
* Refer to https://mobx.js.org/getting-started.html for a quick tutorial on ```MobX```
* Refer to https://mobx.js.org/best/store.html for best practices on structuring stores (like a single ui store and multiple domain stores)

## State management with ```Redux```:
* ```Redux``` library offers an advanced functional way to predictively manage state, using reducers and actions.
* With ```Redux```, we need to write more verbose code than we were doing with ```MobX```, because there is less magic but more trackable interactions with state.
* With ```Redux```, the app state is a single immutable tree of keys and state parts corresponding to these keys. State is not manipulated directly inside the components, but indirectly via actions created in the component DOM or lifecycle events and reducers which catch and process these actions and then create new version of the corresponding state part, which then replace that part of the app state.
* State of an app is managed in a central store, called the root reducer, which is a combination of all the reducers in the app. Components can bind to parts of this app state by connecting to reducers responsible for that part of the app state, and can change parts of this app state by creating actions, which are dispatched to (caught up by) reducers which again return the changed state.
* ```Redux``` name comes from the combination of ```Reducer``` and ```Flux```, and it is an implementation of the ```Flux``` architecture with reducers.
* A reducer is a function which gives part of this central app state to any component which is interested in this part of state. We can write a reducer which returns some static data, like
  ```jsx
  //inside booksReducer.js
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
* At the start of the app, all reducers are combined into a single app state store (root reducer) by using ```combineReducer()```, like
  ```jsx
  const rootReducer = combineReducers(
    {
      books: booksReducer,
      selectedBook: selectedBookReducer,
      ...
    }
  );
  
  export default rootReducer;
  ```
  And we wrap the ```<App>``` component with a ```<Provider>```, like
  ```jsx
  <Provider store={createStore(rootReducer)}>
    <App/>
  </Provider>
  ```
* If we want to load some persisted state at the startup (from a database, ```localStorage```, api, etc.), we can load initial data into a variable like ```persistedState```, and call ```createStore()``` like ```createStore(rootReducer, persistedState)```, so  that parts of state coming from ```persistedState``` will override the state coming from ```rootReducer```
* The ```Redux``` store has ```getState()``` (gets the store state), ```subscribe()``` (subscribes to state changes), and ```dispatch()``` (dispatches an action to all reducers) methods which implement a pub/sub pattern and action dispatching. The root ```<App>``` component subscribes to the store to re-render when any part of the state in the store changes (the store calls ```ReactDOM.render()``` each time state changes inside the store). However, wo don't use these methods inside other components, instead we use functions which come with ```react-redux``` to bind component properties to store state and to dispatch actions using action creators. We typically place all reducers inside a ```reducers``` folder, and all actions into an ```actions``` folder in the project structure.
* Inside the container, the props of the container are bound to a reducer (part of the central app state) by declaring a ```mapStateToProps()``` function, and calling ```connect()``` function, like
  ```jsx
  class BookList extends React.Component {
    render() {
      const items = this.props.bookCollection.map(item => <li key={item.id}>{item.title}</li>);
      return <ul>{items}</ul>;
    }
    
    mapStateToProps(state) {
      return {
        bookCollection: state.books
      };
    }
  }
  
  export default connect(mapStateToProps)(BookList);
  ```
  Here, ```mapStateToProps()``` binds the ```BooksList``` component ```props``` to the ```books``` part of the central app state. Whenever the ```books``` changes, ```BooksList``` will be re-rendered with new values on its ```props.bookCollection```. We can have multiple ```props``` to state members mappings inside the returned object. The call to ```connect()``` function turns the dumb ```BooksList``` component into a smart component (container).
* Any event which needs to change part of the app state creates an action which is an object with an action type and various data members, like
  ```jsx
  //inside selectBook.js
  export function selectBook(book) {
    return { type: "SELECT_BOOK", book: book };
  }
  ```
* Each action can be processed by (dispatched to) all the reducers in the app, and a reducer may respond (return specific date) if it is interested in the action, this way changing parts of the central app state. So a reducer is typically a switch statement with cases for each action type it is interested in to respond with a state change.
* Inside the container, to ensure the dispatching of an action to all reducers, we declare a ```mapDispatchToCreators()``` function, and calling ```connect()``` function, like
  ```jsx
  class BookList extends React.Component {
    render() {
      const items = this.props.bookCollection.map(item => 
	<li key={item.id} onClick={(e) => this.props.selectBook(item)}>{item.title}</li>
      );
      return <ul>{items}</ul>;
    }

    mapStateToProps(state) {
      return {
        bookCollection: state.books
      };
    }

    mapDispatchToCreators(state) {
      return bindActionCreators({selectBook: selectBook}, dispatch);
    }
  }
   
  export default connect(mapStateToProps, mapDispatchToCreators)(BookList);
  ```
  Here, ```mapStateToProps()``` allows dispatching of selectBook action to all reducers, and also adds ```selectBook``` on the ```BooksList``` component ```props```, so that we can call it like ```this.props.selectBook(...)``` to create a ```"SELECT_BOOK"``` action. We can have multiple ```props``` to ```action``` mappings inside the first parameter to ```bindActionCreators()``` call. We also add ```mapStateToPropscall``` parameter to the ```connect()``` function call.
* We can define the reducer which responds to this "SELECT_BOOK" action, like
  ```jsx
  //inside selectedBookReducer.js
  export default function(state = null, action) {
    switch(action.type) {
      case "SELECT_BOOK":
        return action.book;
    }
    
    return state;
  }
  ```
  Here, the ```state``` parameter is not the whole central app state, instead it is the ```selectedBook``` part inside ```rootReducer```, which this reducer is responsible for, and it is the value that was previously returned by this reducer. Also, the initial parameter value ```state = null``` applies only to the initial first call where there is no previously set state and it comes as undefined. We can define the initial state value for ```selectedBook``` part this way.
* In ```Redux```, the parts of the app state returned by reducers should be immutable, so a reducer should not return a mutated version of the state, it should return create a fresh new one and return that. Reducers should be pure functions, meaning that their output only depends on their inputs (function parameters), and they should not mutate their input parameters either. They should not have side-effects, like making a database or network api call, or print something to the screen.
* We can define the reducer which responds to this "SELECT_BOOK" action, like
  ```jsx
  //inside selectedBookReducer.js
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
* When using ```Redux```, we don't have to keep all state in the store, we can use route parameters and query strings that comes from react router, or component state to keep temporary state. For example, we can use an ```:id``` route parameter inside the ```BookDetails``` container which is associated with a ```<Route>```, like
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
  //inside localStorage.js
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

## Calling an async api with ```Redux```:
* We can call an asynchronous api inside the component lifecycle methods and dispatch an action when the call finishes (the promise resolves and returns a result), but this is not the best way to do this.
* Also, we should not call an api inside a reducer function, because reducer functions should be pure functions (the result should only depend on the function arguments, and there should be no side effects).
* A better way of calling an asynchronous api is inside the actions, but then we cannot return the result, only the promise. For the reducers to work with an action that returns a promise, we need the ```redux-promise``` middleware. A middleware is a function that sits between actions and reducers, and can modify an action or even cancel it before it reaches the reducers.
* We send the promise returned from the async api, such as ```axios.get(someURL)```, as the payload data in the action. ```redux-promise``` will just wait until the promise resolves and change the promise payload on the action and turn it into the result data coming from the promise, so that the reducers only see the result data, not the promise payload on their action parameter. We can code the action creator, like
  ```jsx
  //inside fetchWeather.js
  export function fetchWeather(city) {
    return { type: "FETCH_WEATHER", weather: axios.get(...someURL...) };
  }
  ```
  and the reducer, like
  ```jsx
  //inside fetchedWeatherReducer.js
  export default function(state = null, action) {
    switch(action.type) {
      case "FETCH_WEATHER":
        return action.weather;
    }
    
    return state;
  }
  ```

## Chaining async calls with ```Redux Thunk```
* To chain actions which return promises, or to chain async calls inside an action, or to catch errors from an async api call in an action, we need to use the ```redux-thunk``` library.
* With ```redux-thunk```, inside our action creator, we can return a fuction which takes ```dispatch``` and ```getState``` as parameters and returns an action. ```dispatch``` is the function which actually dispatches an action to all reducers. We can create the same action as in the previous example, but this time returning a function rather than a promise, like
  ```jsx
  //inside fetchWeather.js
  export function fetchWeather(city) {
    const promise = axios.get(...someURL...);
    return (dispatch) => {
      promise.then(data => dispatch({ type: "FETCH_WEATHER", weather: data }));
    }
  }
  ```
* To display a busy indicator and to display errors from an api call inside a component, we can make multiple dispatches, first with a "FETCH_WEATHER_BUSY", then on success a "FETCH_WEATHER_SUCCESS", and in case of errors a "FETCH_WEATHER_ERROR", and we can even cache fetched data, like
  ```jsx
  //inside fetchWeather.js
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

    mapStateToProps(state) {
      return {
        weather: state.weather,
        isBusy: state.isBusy,
        error: state.error
      };
    }
  }
  
  export default connect(mapStateToProps)(WeatherInfo);
  ```
  
## React-bootstrap components:
* Bootstrap components originally require jQuery, but ```react-bootstrap``` offers these as React components without need for jQuery, refer to https://react-bootstrap.github.io for details and https://blog.logrocket.com/how-to-use-bootstrap-with-react-a354715d1121 for a quick tutorial (also shows usage of ```reactstrap```, an alternative library for using ```bootstrap``` with React)

## Hands on project: ```pluralsight.com``` clone:
* TODO
