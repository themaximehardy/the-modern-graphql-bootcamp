# GraphQL Basics: Subscriptions

```js
// src/index.js
import { GraphQLServer, PubSub } from 'graphql-yoga'; // Add PubSub
import db from './db';
import Query from './resolvers/Query';
import Mutation from './resolvers/Mutation';
import Subscription from './resolvers/Subscription'; // Add Subscription
import User from './resolvers/User';
import Post from './resolvers/Post';
import Comment from './resolvers/Comment';

const pubsub = new PubSub();

const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers: {
    Query,
    Mutation,
    Subscription, // Add Subscription
    User,
    Post,
    Comment,
  },
  context: {
    db,
    pubsub, // Add pubsub in the context
  },
});

server.start(() => {
  console.log('The server is up!');
});
```

```js
// src/resolvers/Subscription.js
const Subscription = {
  count: {
    subscribe(parent, args, { pubsub }, info) {
      let count = 0;

      setInterval(() => {
        count++;
        pubsub.publish('count', {
          count,
        });
      }, 1000);

      return pubsub.asyncIterator('count');
    },
  },
};

export { Subscription as default };
```
