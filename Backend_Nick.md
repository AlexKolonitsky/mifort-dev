## Concepts
React is a declarative, component-based JavaScript UI-framework. 
React’s core concept is that you page consists of a number of independent components, each with its own state and an array of methods. Components can be defined via JS functions:
```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
 or ES6 classes (which have some additional features), that extend the `React.Component` class: 
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```
Every React component has:
1. A `props` object that contains a set of input parameters that are passed to the component through attributes:
    ```javascript
    function App() {
      return (
        <div>
          <Welcome name="Sara" />
          <Welcome name="Cahal" />
          <Welcome name="Edite" />
        </div>
      );
    }
    ```
    `props` object is read-only, meaning all React components must act like pure functions with respect to their props.
2. A `state` object, which is local, private and fully controlled by the component and represents the component's state. The only place where you can directly assign the `state` is the component's constructor. Component's `setState()` method should be used in all other cases. Changing the state via `setState()` also causes the component to re-render itself, which is the preferred way to update the view.
3. A `render()` method that allows you to render the component on a page. 
_Note_: `render()` should always return a single root element.

As you could notice, the `render()` methods we used as an example returned a strange HTML-looking thing. This is JSX - a JS syntax extension. JSX can be split into multiple lines, and you can also embed JS expressions in it by wrapping them in singular curly braces. You can create normal HTML using JSX, as well as use any of the components that you've already declared. Any JSX object itself is also a JS expression, therefore it can be assigned to a variable, concatenated with another JSX object, etc.
_Note_: since JSX is closer to JS than to HTML, the `camelCase` attribute naming convention is used, e.g. `className` instead of `class`.
Finally, ReactDOM has its own `render()` method that renders one of your components into an existing element on the page:
```javascript
ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```
## Requirements
To start working with React you will need to:
1. Install React. React is accessible via [npm](https://www.npmjs.com/) (`npm install react react-dom`) or [Yarn](https://yarnpkg.com/) (`yarn add react react-dom`); the `react` and `react-dom` npm packages also provide single-file distributions in dist folders, which are hosted on a CDN:
    ```html
    <script src="https://unpkg.com/react@15/dist/react.min.js"></script>
    <script src="https://unpkg.com/react-dom@15/dist/react-dom.min.js"></script>
    ```
    The `create-react-app` is also a good way to get started with a new React app, which allows you to create the initial structure for your future application and to optimize your app for production later:
    ```
    npm install -g create-react-app
    create-react-app my-app
    cd my-app
    npm start
    ```
2. [Install Babel](http://babeljs.io/docs/setup/). Since JSX is not innately supported by JS, and some browsers don't have ES6 support yet, you'll need to convert your code into ES5. It is possible to work with React without using ES6 classes and JSX, but those are too convenient to pass up.
