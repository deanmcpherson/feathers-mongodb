feathers-mongodb
================

[![Build Status](https://travis-ci.org/feathersjs/feathers-mongodb.png?branch=master)](https://travis-ci.org/feathersjs/feathers-mongodb)

[![Code Climate](https://codeclimate.com/github/feathersjs/feathers-mongodb.png)](https://codeclimate.com/github/feathersjs/feathers-mongodb)

> A MongoDB CRUD service for [FeathersJS](http://feathersjs.com)


## Installation

```bash
npm install mongodb feathers-mongodb --save
```

## Documentation

Please refer to the [Feathers database adapter documentation](http://docs.feathersjs.com/databases/readme.html) for more details or directly at:

- [MongoDB](http://docs.feathersjs.com/databases/mongodb.html) - The detailed documentation for this adapter
- [Extending](http://docs.feathersjs.com/databases/extending.html) - How to extend a database adapter
- [Pagination and Sorting](http://docs.feathersjs.com/databases/pagination.html) - How to use pagination and sorting for the database adapter
- [Querying](http://docs.feathersjs.com/databases/querying.html) - The common adapter querying mechanism

## Getting Started

You can create a MongoDB service like this:

```js
var MongoClient = require('mongodb').MongoClient;
var service = require('feathers-mongodb');
var app = feathers();

MongoClient.connect('mongodb://localhost:27017/feathers').then(function(db){
  app.use('/messages', service({
    Model: db
  }));

  app.listen(3030);
});
```

This will create a `messages` endpoint and connect to a local `messages` collection on the `feathers` database.


### Complete Example

Here's a complete example of a Feathers server with a `messages` MongoDB service.

```js
const feathers = require('feathers');
const rest = require('feathers-rest');
const socketio = require('feathers-socketio');
const errors = require('feathers-errors');
const bodyParser = require('body-parser');
var MongoClient = require('mongodb').MongoClient;
const service = require('feathers-mongodb');

// Create a feathers instance.
const app = feathers()
  // Enable Socket.io
  .configure(socketio())
  // Enable REST services
  .configure(rest())
  // Turn on JSON parser for REST services
  .use(bodyParser.json())
  // Turn on URL-encoded parser for REST services
  .use(bodyParser.urlencoded({extended: true}));

  // Connect to your MongoDB instance(s)
MongoClient.connect('mongodb://localhost:27017/feathers').then(function(db){
  // Connect to the db, create and register a Feathers service.
  app.use('/messages', service({
    Model: db.collection('messages'),
    paginate: {
      default: 2,
      max: 4
    }
  }));

  // A basic error handler, just like Express
  app.use(errors.handler());

  // Start the server
  var server = app.listen(3030);
  server.on('listening', function() {
    console.log('Feathers Message MongoDB service running on 127.0.0.1:3030');
  });
}).catch(function(error){
  console.error(error);
});
```

You can run this example by using `npm start` and going to [localhost:3030/messages](http://localhost:3030/messages). You should see an empty array. That's because you don't have any messages yet but you now have full CRUD for your new message service!

## Changelog

__2.0.0__

- Compatibility with Feathers 2.x
- Changing how a service is initialized
- Removing mongodb as a bundled dependency
- Converting over to ES6
- Converting to use the new service test harness
- Moving over to Promises.
- Updating documentation and example.

__1.1.0__

- Update to common infrastructure and querying criteria ([#15](https://github.com/feathersjs/feathers-mongodb/pull/15))

__1.0.0__

- makes this adapter consistent with the others in terms of documentation and file structure
- updates mongoskin dependency to the latest
- adds support for special query filters
    - $sort
    - $select
    - $skip
    - $limit
- Closes #8 by making sure that we autoreconnect by default when not passing a connection string

__0.3.0__

- Implement `.patch` support ([#5](https://github.com/feathersjs/feathers-mongodb/issues/5))
- Better documentation
- Refactoring that removes pre-implemented MongoSkin options

__0.2.x__

- Pre-releases

## License

Copyright (c) 2015

Licensed under the [MIT license](LICENSE).
