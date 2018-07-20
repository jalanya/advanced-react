# Advanced react
## Dependencies

### Production Dependencies

Dependency | Use
-- | ---
[babel-cli](https://babeljs.io/docs/en/babel-cli.html)| Babel Command line interface. This can be used to compile files from the command line
[babel-loader](https://github.com/babel/babel-loader)|Adds Babel support to Webpack. This package allows transpiling JavaScript files using Babel and webpack.
[babel-polyfill](https://babeljs.io/docs/en/babel-polyfill)| Polyfill for Babel features that cannot be transpiled. Like async/await
[babel-preset-env](https://babeljs.io/docs/en/babel-preset-env)| It is a preset that compiles down to a minimum of ES5 (preset-es2015), but can also take a browser or runtime version and determine which plugins are needed for that specific environment.
[babel-preset-react](https://babeljs.io/docs/en/babel-preset-react.html)|Add JSX support to Babel
[babel-preset-stage-2](https://babeljs.io/docs/en/babel-preset-stage-2)| Any transforms in [stage-x presets](https://babeljs.io/docs/en/plugins/) are changes to the language that haven't been approved to be part of a release of Javascript (such as ES6/ES2015)

##### [babel-preset-es2015 -> babel-preset-env](http://babeljs.io/docs/en/env/)
##### [babel/babel-preset-env](https://github.com/babel/babel-preset-env)
##### [Babel & preset-env](https://codeburst.io/babel-preset-env-cbc0bbf06b8f)
##### [What are Babel "plugins" and "presets"?](https://www.fullstackreact.com/articles/what-are-babel-plugins-and-presets/)

## The Context API and Higher Order Components

### Type-checking with PropTypes

[prop-types](https://www.npmjs.com/package/prop-types) | [Flow is a static type checker](https://flow.org/)

### React's Context API

**Note:**
The legacy context API will be removed in a future major version. Use the new context API introduced with version 16.3. The legacy API will continue working for all 16.x releases.

[Context](https://reactjs.org/docs/context.html) | [Legacy Context](https://reactjs.org/docs/legacy-context.html)


### Mapping Extra Props

Currying is a process of converting a function with n number of arguments into a nested unary function.

`const add = (x,y) => x + y;`

It’s a simple function. We can call this function like add(1,1), which is going to give us the result 2. Nothing fancy here. Now here is the curried version of the add function:

`const addCurried = x => y => x + y;`

e.g. `export default storeProvider(extraProps)(Article)`

```
import React from 'react';
import PropTypes from 'prop-types';

const storeProvider = (extraProps) => (Component) => {
  return class extends React.Component {
    static displayName = `${Component.name}Container`;
    static contextTypes = {
      store: PropTypes.object,
    };

    render() {
      return <Component
        {...this.props}
        {...extraProps(this.context.store, this.props)}
        store={this.context.store} />;
    }
  };
};

export default storeProvider;
```


```
import React from 'react';
import PropTypes from 'prop-types';
import storeProvider from './storeProvider';

const Article = (props) => {
  const { article, author } = props;
  return (
   ...
  );
};

Article.propTypes = {
  ...
};

function extraProps(store, originalProps) {
  return {
    author: store.lookupAuthor(originalProps.article.authorId),
  };
}

export default storeProvider(extraProps)(Article);
```

## Subscribing to State

### Using the setState Function

#### [`setState(stateChange[, callback])`](https://reactjs.org/docs/react-component.html#setstate)

The second parameter to setState() is an optional callback function that will be executed once setState is completed and the component is re-rendered. Generally we recommend using `componentDidUpdate()` for such logic instead.

You may optionally pass an object as the first argument to setState() instead of a function.

#### [lodash.pickBy](https://lodash.com/docs/4.17.10#pickBy)

#### [lodash.debouce](https://lodash.com/docs/4.17.10#debounce)

### Subscribing to State from Child Components

#### [`React.PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent)

`React.PureComponent` is similar to `React.Component`. The difference between them is that `React.Component` doesn’t implement `shouldComponentUpdate()`, but `React.PureComponent` implements it with a shallow prop and state comparison.

If your React component’s `render()` function renders the same result given the same props and state, you can use `React.PureComponent` for a performance boost in some cases.

#### [`forceUpdate()`](https://reactjs.org/docs/react-component.html#forceupdate)

```
component.forceUpdate(callback)
```

By default, when your component’s state or props change, your component will re-render. If your `render()` method depends on some other data, you can tell React that the component needs re-rendering by calling `forceUpdate()`.

Calling `forceUpdate()` will cause `render()` to be called on the component, skipping `shouldComponentUpdate()`. This will trigger the normal lifecycle methods for child components, including the `shouldComponentUpdate()` method of each child. React will still only update the DOM if the markup changes.

Normally you should try to avoid all uses of `forceUpdate()` and only read from this.props and this.state in `render()`.

## Performance Optimization
### Understanding shouldComponentUpdate and componentWillUpdate

#### [`componentDidMount()`](https://reactjs.org/docs/react-component.html#componentdidmount)
`componentDidMount()` is invoked immediately after a component is mounted (inserted into the tree). Initialization that requires DOM nodes should go here. If you need to load data from a remote endpoint, this is a good place to instantiate the network request.

This method is a good place to set up any subscriptions. If you do that, don’t forget to unsubscribe in `componentWillUnmount()`.

#### [`UNSAFE_componentWillUpdate()`](https://reactjs.org/docs/react-component.html#unsafe_componentwillupdate)

`UNSAFE_componentWillUpdate()` is invoked just before rendering when new props or state are being received. Use this as an opportunity to perform preparation before an update occurs. This method is not called for the initial render.

A way where you could look into how many times the rendering is happening, you can use the code below:
```
shouldComponentUpdate(nextProps, nextState) {
  return true;
}

componentWillUpdate(nextProps, nextState) {
  console.log('Updating Component...');
}
```

#### [`Date.prototype.toLocaleTimeString()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleTimeString)

### Making Store-connected Components Subscribe to Partial State

#### [Performance Tools](https://reactjs.org/docs/perf.html)

#### [Debugging React performance with React 16 and Chrome Devtools](https://building.calibreapp.com/debugging-react-performance-with-react-16-and-chrome-devtools-c90698a522ad)

#### [Profiling Components with the Chrome Performance Tab](https://reactjs.org/docs/optimizing-performance.html#profiling-components-with-the-chrome-performance-tab)

#### [Puts your console on blast when React is making unnecessary updates](https://github.com/maicki/why-did-you-update)

### Immutable Data Structures

Code below is mutating the state.
```
setTimeout(() => {
  const fakeArticle = {
    ...rawData.articles[0],
    id: 'fakeArticleId',
  }
  this.data.articles[fakeArticle.id] = fakeArticle;
   this.notifySubscribers();
}, 1000);
```

Code below is not mutating the state.
```
setTimeout(() => {
  const fakeArticle = {
    ...rawData.articles[0],
    id: 'fakeArticleId',
  }
  this.data = {
    ...this.data,
    articles: {
      ...this.data.articles,
      [fakeArticle.id]: fakeArticle
    },
  };
  this.notifySubscribers();
}, 1000);
```
Always, we must use immutable data structures, never mutate your data structures directly, because that's going to have performance implications on your application, eventually. However, when you start copying object to avoid mutability, things might start to be harder than they should so it should be great to use some kind of immutability helper.

##### [Immutability Helpers](https://reactjs.org/docs/update.html)
##### [immutable-js](https://facebook.github.io/immutable-js/)

## Production Deployment Best Practices

### Continuous Integration and Test Coverage

##### [Customizing Git - Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

Git hooks are scripts that Git executes before or after events such as: commit, push, and receive.

[Git hooks](https://githooks.com/)

##### [Test and Deploy with Confidence](https://travis-ci.org/)
Easily sync your GitHub projects with Travis CI and you’ll be testing your code in minutes!

### Upgrading Dependencies

[Semantic Versioning 2.0.0](https://semver.org/)
Given a version number MAJOR.MINOR.PATCH


### Separating Vendor Files

##### CommonsChunkPlugin
```
new webpack.optimize.CommonsChunkPlugin(options);
```
The CommonsChunkPlugin is an opt-in feature that creates a separate file (known as a chunk), consisting of common modules shared between multiple entry points.

The CommonsChunkPlugin has been removed in webpack v4 legato. To learn how chunks are treated in the latest version, check out the [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/).


#### New optimization.splitChunks option was introduced
You can find more information in the links below:

##### [webpack 4.0.0-alpha.5 feedback](https://github.com/webpack/webpack/issues/6357)

##### [RIP CommonsChunkPlugin](https://gist.github.com/sokra/1522d586b8e5c0f5072d7565c2bee693)


### Running Node.js in Production

#### [PM2](http://pm2.keymetrics.io/)
Advanced, production process manager for Node.js - A Complete feature set for production environment, built with a worldwide community of developers and enterprises

- Behavior configuration | Source map support
- Container Integration | Watch & Reload
- Log management | Monitoring
- Module System | Max memory reload
- Cluster Mode | Hot reload
- Development workflow | Startup Scripts
- Deployment workflow | PaaS Compatible
- Keymetrics monitoring | API


### Summary and Course Wrap Up

##### [jscomplete/reactful](https://github.com/jscomplete/reactful)

An opinionated CLI for full-stack server-rendered React applications

#### REPLs

##### [jsComplete/Repl](https://jscomplete.com/repl)
This is a playground for JavaScript and React to test simple code or prototype ideas.


##### [Codesandbox](https://codesandbox.io/)
The online code editor

#### Recommendations

##### [Advanced Node.js](https://app.pluralsight.com/library/courses/nodejs-advanced/table-of-contents)
##### [Building Scalable APIs with GraphQL](https://app.pluralsight.com/library/courses/graphql-scalable-apis/table-of-contents)
