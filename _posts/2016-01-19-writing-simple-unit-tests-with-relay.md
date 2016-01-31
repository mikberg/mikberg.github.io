---
layout: post
title:  "Writing Simple Unit Tests with Relay"
date:   2016-01-19 13:59:43 +0000
categories: javascript react relay testing
heroimg: images/power-outage.jpg
heroimgcreds: "Header image by [Lisa Bettany](https://flic.kr/p/dp4D78) is licensed under a [Creative Commons BY 2.0 license](https://creativecommons.org/licenses/by/2.0/)."
---

Ever since I started using [Relay][relay] in my React projects, code coverage has dropped significantly in their code bases. Testing Relay containers is not trivial, and the project definitively [lacks a test tool][lacks-test-tool]. There I decided to make an effort and a write-up of my findings.

Mock Relay gist: [https://gist.github.com/mikberg/07b4006e22aacf31ffe6][gist]

## Getting Started: Mocking Relay

To get any testing done, Relay must be mocked to prevent invariant violations such as this one:

```
Invariant Violation: RelayContainer: `Relay(Main)` was rendered without a valid route.
```

If you’re using [Jest][jest], then it’s both automatic and a piece of cake.

If not, then mocking a dependency might be trickier, but if you’re using [Webpack][webpack], then I’ve got this trick up my sleeve:

```js
// webpack.test.config.js (webpack config used in tests)
module.exports = {
  resolve: {
    alias: {
      'react-relay': /* path to mock relay */,
      'real-react-relay': path.join(__dirname, '/../node_modules/react-relay/'),
    }
  },
};
```

In other words: During tests, importing `react-relay` resolves to our mock Relay module, while the real Relay module is available as `real-react-relay`.

Using this, we can write our mock module like this:

```js
// Our mock Relay module
import Relay from 'real-react-relay';
export const QL = Relay.QL;
// ...
```

[I’ve published my mock Relay module as a gist][gist], if you’re interested, heavily inspired by [this comment to facebook/relay#161][facebook/relay#161].

## Testing Containers

Alrighty, we have successfully mocked Relay and are ready to write some actual tests. In the mock Relay module, that `createContainer` simply returns the container class itself.

```js
createContainer: component => component
```

That makes rendering the component with fixtures super simple, we can just give it to the component as props:

```js
const fixtures = {
  viewer: {
    id: ‘myid’,
    hasNewNotifications: false,
  }
};
const element = TestUtils.renderIntoDocument(<MyContainer {...fixtures} />);
```

We can now test that the component renders correctly with every possible data combination from Relay. We can also test static methods. Yay!
In order to test our Relay specs, the second argument sent to `Relay.createContainer`, we modify the mock’s method to sneakily attach its own method to the container class:

```js
...
createContainer: (component, specs) => {
  /* eslint no-param-reassign:0 */
  component.getRelaySpecs = () => specs;
  return component;
},
...
```

Testing becomes a breeze:

```js
// Test our prepareVariables method
const variables = { id: 1 };
expect(MyContainer.getRelaySpecs().prepareVariables(variables))
  .to.equal(variables);
```

Testing the execution of mutations is mentioned in the bottom of the article.

## Testing Queries

Whether or not it is strictly necessary to test the Relay queries, the Relay.QL`…` parts, is up for debate. You’ll probably be pretty safe leaving it out, as they are automatically validated against Relay’s schema.

The real problem here is that the Babel Relay plugin [transforms][babel-transform] our queries *before* they are evaluated by the Relay.QL function; thus it can’t easily be mocked or proxied. If we try to mock it, our mock won’t even be evaluated, because the whole expression is replaced before it’s evaluated.

As far as I know, there’s not really any efficient way of testing the queries other than running tests on the objects they are transformed into.

```js
// isMatch from lodash -- I'm sure there's a library
// implementing this method too, leave a note if you know one.
function findDeep(object, compare) {
  if (compare(object)) {
    return object;
  }
  if (typeof object === 'object') {
    for (const key in object) {
      if ({}.hasOwnProperty.call(object, key)) {
        const result = findDeep(object[key], compare);
        if (result) {
          return result;
        }
      }
    }
  }
}
// This is a pretty stupid test
it(‘queries an ID’, () => {
  const query = MyContainer.getRelaySpecs().fragments.viewer();
  const needle = { fieldName: ‘id’ };
  const result = findDeep(viewerQuery, (obj) => isMatch(obj, needle));
  expect(result.type).to.equal(‘ID’);
});
```

It does increase the code coverage, though not in a very useful way.

## Testing Mutations

Most of the mutation objects is straight forward to test. `getVariables`, for instance, should be a breeze under most circumstances.

```js
const mutation = new CreateEventMutation(inFixtures);
expect(mutation.getVariables(inFixtures))
  .to.deep.equal({ event: inFixtures.event });
```

In most cases however, we’ll get a warning that parts of the fixtures we use to create the mutation really should have come from Relay. With components, we mocked `createContainer` to return the component itself (with some extras), so that we could pass in fixtures as props. We’ll do a similar trick for mutations:

```js
// In our mock Relay module
export class Mutation extends Relay.Mutation {
  _resolveProps(props) {
    this.props = props;
  }
}
```

Configs from `getConfigs()` can be a bit trickier to test. Since they are mostly about defining stuff, I guess that’s not the end of the world.

```js
it(‘correlates input viewer id to returned viewer id’, () => {
  const fieldsChange = mutation.getConfigs()
    .find(config => config.type === ‘FIELDS_CHANGE’);
  expect(fieldsChange.fieldIDs.viewer)
    .to.equal(inFixtures.viewer.id);
});

it(‘requires event.id to be fetched’, () => {
  const requiredChildren = mutation.getConfigs()
    .find(config => config.type === ‘REQUIRED_CHILDREN’);
  const event = findDeep(requiredChildren, obj =>
    isMatch(obj, { type: ‘Event’ }));
  expect(!!findDeep(event, obj =>
    isMatch(obj, { fieldName: ‘id’ }))).to.equal(true);
 });
```

## Test Mutation Usage

The last point is the touch point between our components and mutations. To get this tested, we can simply use Sinon to stub them into the behaviour that we want: calling either the `onSuccess` or the `onFailure` callback.

But this would require us to stub the methods every time, which can become cumbersome. I opted to create a special class:

```js
export class MockStore {
  reset() {
    this.successResponse = undefined;
  }
  succeedWith(response) {
    this.reset();
    this.successResponse = response;
  }
  ...
}
```

See the rest of the class [in the Github Gist][gist].

That allows us to use Sinon to create stubs directing our code the way we want:

```js
it(‘redirects to the new event on success’, () => {
  elem.context = {
    router: {
      replace: sinon.spy(),
    },
  };
  Relay.Store.succeedWith({ createEvent: { event: { id: ‘A’ } } });
  elem._handleSubmit(e);
  expect(elem.context.router.replace.calledWith(‘/events/A/’))
    .to.equal(true);
});

it(‘logs error on failure’, () => {
  sinon.stub(console, ‘error’);
  Relay.Store.failWith({ getError: () => ‘error’ });
  elem._handleSubmit(e);
  expect(console.error.calledWith(‘error’)).to.equal(true);
  console.error.restore();
});
```

Combining all of the techniques above, we at least have a starting point for testing Relay applications. I must admit I still find testability a pain point with Relay apps, and simply because the unit tests work, I wouldn’t be completely confident about the app’s health before actually running it.

I think it could be healthy to use some simple integration tests to verify that the application works.

[relay]: http://facebook.github.io/relay/
[lacks-test-tool]: https://github.com/facebook/relay/issues/161
[gist]: https://gist.github.com/mikberg/07b4006e22aacf31ffe6
[jest]: https://facebook.github.io/jest/docs/manual-mocks.html#content
[webpack]: http://webpack.github.io/
[facebook/relay#161]: https://github.com/facebook/relay/issues/161#issuecomment-134324986
[babel-transform]: https://github.com/facebook/relay/blob/master/scripts/babel-relay-plugin/lib/getBabelRelayPlugin.js#L147
