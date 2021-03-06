# redux-wpapi
[![npm version](https://img.shields.io/npm/v/redux-wpapi.svg?style=flat-square)](https://www.npmjs.com/package/redux-wpapi)
[![Build Status](https://travis-ci.org/log-oscon/redux-wpapi.svg?branch=master)](https://travis-ci.org/log-oscon/redux-wpapi)

A [node-wpapi](https://github.com/WP-API/node-wpapi) integration for a Redux based Application.

## How it Works
This library exposes [node-wpapi](https://github.com/WP-API/node-wpapi) instance through the actionCreator [callAPI](#/src/actions/callAPI.js). The resulting
action is interpreted in [the middleware](#the-middleware), doing so by resolving the request and controlling the reducer through actions.

## Installation
```sh
npm install --save redux-wpapi
```
Then, to enable ReduxWPAPI, use [`applyMiddleware()`](http://redux.js.org/docs/api/applyMiddleware.html) and [`combineReducers()`](http://redux.js.org/docs/api/combineReducers.html):

```js
import reducers from './reducers';
import ReduxWPAPI from 'redux-wpapi';

const { reducer: wp, middleware } = new ReduxWPAPI({ /* … */ });
const store = createStore(
  // the reducer must be placed at the root of the state as `wp`
  // so the selector knows where state lives in
  { ...reducers, wp },
  applyMiddleware(middleware)
);

```

## Usage
```js
import React from 'react';
import { wp, selectRequest, ResponseStatus } from 'redux-wpapi';
import { connect } from 'react-redux';

export class HomePage extends React.Component {
  static loadData(props) {
    return props.callAPI(
      // The name where the request state will be placed
      'HomePagePosts',
      // A callback where wpapi instance is injected
      api =>
      api.posts()
      .page(props.page)
      .perPage(props.perPage)
    );
  }

  componentWillMount() {
    HomePage.loadData(this.props);
  }

  componentWillReceiveProps(props) {
    HomePage.loadData(props);
  }

  render() {
    const { status, data: posts } = this.props.request;

    if (!posts) {
      switch (status) {
          case ResponseStatus.pending: return <div>Loading…</div>;
          case ResponseStatus.rejected: return <div>An error has occurred</div>;
      }
    }

    return (
      <div>
        {!posts.length ? <NoPostFound /> : <PostList posts={posts} />}
      </div>
    );
  }
}

export default connect(createStructuredSelector({
  request: selectRequest('HomePagePosts'),
}), { callAPI })(HomePage);
```

## Contributions

All contributions are welcome, and very much appreciated.

We are preparing some helper [documents](https://github.com/log-oscon/redux-wpapi/issues/4) to facilitate the process (hopefully), but for now we're following the these guidelines:

* Be reasonable
* Give as much detailed information as you can
* Keep it as short as possible
* Let the code talk

### Must have's

Every Pull Request must have the following:

* Unit tests for any functionality that's exposed to the end user.
* An entry in the [CHANGELOG.md](https://github.com/log-oscon/redux-wpapi/master/CHANGELOG.md) file.
