# url-router
A Trie-based router.

## Installation
```
npm install url-router
```

## Examples

```js
const assert = require('assert')
const Router = require('url-router')

const router = new Router([
  ['/foo', 1],
  ['/foo/bar', 2],
  ['/user/:id', 3],
  ['/user/:id/:page', 4],
  ['/people/:name(\\w+)', 5],
  ['(.*)', 6],
  ['/:year(\\d+)-:month(\\d+)', 7]
])

assert.deepStrictEqual(
  router.find('/foo'),

  {
    handler: 1,
    params: {}
  }
)

assert.deepStrictEqual(
  router.find('/foo/bar'),

  {
    handler: 2,
    params: {}
  }
)

assert.deepStrictEqual(
  router.find('/user/123'),

  {
    handler: 3,
    params: {
      id: '123'
    }
  }
)

assert.deepStrictEqual(
  router.find('/user/456/articles'),

  {
    handler: 4,
    params: {
      id: '456',
      page: 'articles'
    }
  }
)

assert.deepStrictEqual(
  router.find('/people/john'),

  {
    handler: 5,
    params: {
      name: 'john'
    }
  }
)

assert.deepStrictEqual(
  router.find('/404'),

  {
    handler: 6,
    params: {}
  }
)

assert.deepStrictEqual(
  router.find('/2019-11'),

  {
    handler: 7,
    params: {
      year: '2019',
      month: '11'
    }
  }
)
```

## API

### Router
```js
new Router([
  [pattern1, handler1],
  [pattern2, handler2],
  ...
])
```

Creates a router instance.

If the routes array is provided, `router.add` will be applied on each route.

### router.add
```js
router.add(pattern, handler)
```

Adds a route definition.

#### Params

##### pattern
`String`. The pattern to match against the request path.

You can define params in `pattern`, for example:

```js
const router = new Router()
router.add('/people/:username/articles/:articleId(\\d+)', handler)
const result = router.find('/people/johnsmith/articles/123')
/*
result:
{
  handler: handler,
  params: {
    username: 'johnsmith',
    articleId: '123'
  }
}
*/
```

If regex is omitted, it defaults to `[^/]+`.

You can also use regex without setting the parameter name, for example:

```js
router.add('(.*)', NotFound)
```

This defines a catch-all route.

##### handler
`Any`. The handler you wish to handle the request.
Based on your framework design, the handler can be a function to handle the request,
or the file path to your controller file, or an object (such as Vue component), etc.

#### Returns
The router instance. So you could use method chaining:

```js
router
  .add('/foo', foo)
  .add('/bar', bar)
```

### router.find
```js
router.find(path)
```

Finds the route which matches the path.

#### Parameters

##### path
`String.` The request path.  

#### Returns

`handler` and `params` of the route:

```js
{
  handler,
  params
}
```

Or `null` if not found.

## License
[MIT](LICENSE)
