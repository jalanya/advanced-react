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
