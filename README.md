# GraphQL Yoga

[![npm version](https://badge.fury.io/js/graphql-yoga.svg)](https://badge.fury.io/js/graphql-yoga) [![Build Status](https://travis-ci.org/dotansimha/graphql-yoga.svg?branch=master)](https://travis-ci.org/dotansimha/graphql-yoga) [![Gurubase](https://img.shields.io/badge/Gurubase-Ask%20GraphQL%20Yoga%20Guru-006BFF)](https://gurubase.io/g/graphql-yoga)

GraphQL Yoga is a fully-featured GraphQL Server with focus on easy setup, performance and great developer experience.

## Features

- **Easiest way to run a GraphQL server:** Sensible defaults & includes everything you need with minimal setup.
- **Includes Subscriptions:** Built-in support for GraphQL subscriptions using WebSockets.
- **Compatible:** Works with all GraphQL clients (Apollo, Relay...) and fits seamless in your GraphQL workflow.
- **Extensible:** Simple and powerful API for adding plugins and custom functionality.

## Getting Started

Install GraphQL Yoga using yarn or npm:

```bash
yarn add graphql-yoga
```

or

```bash
npm install graphql-yoga
```

Then create a `server.js` file:

```javascript
const { GraphQLServer } = require('graphql-yoga')

// Type definitions define the "shape" of your data and specify
// which ways the data can be fetched from the GraphQL server.
const typeDefs = `
  type Query {
    hello: String!
  }
`

// Resolvers define the technique for fetching the types defined in the
// schema. This resolver retrieves books from the "books" array above.
const resolvers = {
  Query: {
    hello: () => `Hello World!`,
  },
}

// The `GraphQLServer` class is used to create a new GraphQL server.
const server = new GraphQLServer({ typeDefs, resolvers })

// The `start` method is used to start the server.
server.start(() => console.log('Server is running on http://localhost:4000'))
```

Run the server:

```bash
node server.js
```

For more information about how to get started with GraphQL Yoga, check our official documentation. You can also ask [GraphQL Yoga Guru](https://gurubase.io/g/graphql-yoga), it is a GraphQL Yoga-focused AI to answer your questions.

## Contributing

We welcome contributions of all kinds from anyone. Please take a look at the [contributing guide](./CONTRIBUTING.md) if you're interested in helping out. If you have questions, feel free to open an issue in this repository.