# Advanced react

## The Context API and Higher Order Components

### Type-checking with PropTypes

[prop-types](https://www.npmjs.com/package/prop-types) | [Flow is a static type checker](https://flow.org/)

### React's Context API

**Note:**
The legacy context API will be removed in a future major version. Use the new context API introduced with version 16.3. The legacy API will continue working for all 16.x releases.

[Context](https://reactjs.org/docs/context.html) | [Legacy Context](https://reactjs.org/docs/context.html)


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

#### `setState(stateChange[, callback])`

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
