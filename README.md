page-express-mapper
===

[![npm](https://img.shields.io/npm/v/page-express-mapper.svg)](https://www.npmjs.com/package/page-express-mapper)

A plugin for [page.js](http://visionmedia.github.io/page.js/) which aims to provide a direct imitation of the [Express](http://expressjs.com/) API so you can write isomorphic (aka universal, [amphibious](https://twitter.com/kethinov/status/566896168324825088), etc) router code that can be shared on the client and the server with your Express application without modification.

With this plugin you should be able to write isomorphic JavaScript apps that maximize code reuse on the client and the server, so long as you run Express on the server, page.js on the client, and use a JS-based templating system that can run on the client and server.

This module was built and is maintained by the [Roosevelt web framework](https://github.com/rooseveltframework/roosevelt) [team](https://github.com/orgs/rooseveltframework/people), but it can be used independently of Roosevelt as well.

Usage
===

- Add `page-express-mapper` to your npm dependencies.
- Load `page-express-mapper.js` into your frontend JS bundle along with `page.js`.
- Then initialize it by calling `pageExpressMapper()` *before* defining any routes:

Assuming your server code begins something like:

```js
// express-server.js

// init express
const app = express()

// load an isomorphic routes.js file that declares routes
// you can share this file verbatim with the client
require('routes')(app)
```

And your client code begins something like:

```js
// client.js

// require dependencies
const page = require('page')
const pageExpressMapper = require('page-express-mapper')

// configure pageExpressMapper
const router = pageExpressMapper({
  renderMethod: function(template, model) {
    // render a template using your
    // favorite templating system here
  }
})


// load the same isomorphic routes.js file as above
// you can share this file verbatim with the server
require('routes')(router)

// init page.js
page()
```

You can then write identical routes for both sides, such as:

```js
// routes.js

// these routes will be shared on both the client and server
module.exports = function(router) {
  router.route('/someRoute').get(function(req, res) {
    res.render('someTemplate', {some: 'model'})
  })
}
```

Note: You will also need a way to share your templates with the client and server as well. If you're using [Roosevelt](https://github.com/rooseveltframework/roosevelt), you can use the `clientViews` feature. If you're using vanilla Express, you'll need to create a build script that will allow the template files to be shared on both sides.

Params
===

`pageExpressMapper()` returns a `router` object to attach routes to (like Express) and accepts the following params:

function renderMethod(template, model)
---

*Required*

This is designed to mimic the [Express render method](http://expressjs.com/api.html#app.render). Load your own templating system elsewhere in your app and call its render method here as a function of your own design.

As with Express, the `template` argument should refer to a named template and the `model` argument should be a JSON string or JavaScript object.

For example, using the [Teddy](https://github.com/rooseveltframework/teddy) templating engine:

```js
pageExpressMapper({
  renderMethod: function(template, model) {
    const newHTML = teddy.render(template, model)
    // do something with the new HTML
  }
})
```

This should work with any templating engine which supports both client-side rendering and Express on the server-side.

object customRouter
---

*Optional*

By default this plugin matches the Express 4 API, but if you want to remap it, supply a `customRouter`. For example, to match the Express 3 API, you could do this:

```js
pageExpressMapper({
  renderMethod: someRenderMethod,
  customRouter: {
    get: function(route, callback) {
      page(route, callback)
    },
    post: function(route, callback) {
      page(route, callback)
    },
    all: function(route, callback) {
      page(route, callback)
    }
  }
})
```

Default: `undefined`

Sample app
===

- Clone this repo.
- Install dependencies.
- `cd` to your clone.
- `npm run sample-app`.
- Open the browser dev tools and examine the console logs when you click the links in the sample app to see evidence of it working.
