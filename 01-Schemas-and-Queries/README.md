# GraphQL Basics: Schemas and Queries

```js
// src/index.js
import { GraphQLServer } from 'graphql-yoga';

// Scalar types – String, Boolean, Int, Float, ID

const users = [
  {
    id: '1',
    name: 'Max',
    email: 'max@google.com',
    age: 29,
    posts: ['1'],
    comments: ['1', '2'],
  },
  {
    id: '2',
    name: 'John',
    email: 'john@google.com',
    posts: ['2'],
    comments: ['3'],
  },
  {
    id: '3',
    name: 'Emma',
    email: 'emma@google.com',
    posts: ['3'],
    comments: ['4'],
  },
];

const posts = [
  {
    id: '1',
    title:
      'Necessitatibus non inventore assumenda iusto cumque quisquam aliquid.',
    body: 'Quaerat aliquam ut adipisci a sed minus. Autem in quidem facilis aspernatur consequatur laborum exercitationem doloremque accusamus. Sit esse ratione culpa suscipit quisquam.',
    published: true,
    comments: ['1', '2'],
    author: '1',
  },
  {
    id: '2',
    title: 'Provident velit autem ducimus et.',
    body: 'Cupiditate magnam omnis voluptatibus facere. Facilis quas sit consequatur amet quam et. Qui rerum aperiam quasi a accusamus ipsam natus vel impedit. Quo non sequi nihil voluptatibus ad vero repudiandae. Qui asperiores incidunt ad a officiis sit.',
    published: true,
    comments: ['3'],
    author: '2',
  },
  {
    id: '3',
    title: 'Vel ut nisi nisi nihil.',
    body: 'Et vel nam voluptatem ea impedit est. Explicabo est optio consequatur quasi dolor eos ullam aspernatur. Quo aut voluptatibus quisquam nihil eum voluptas optio. Quia earum nobis molestiae vitae. Itaque odit molestiae.',
    published: true,
    comments: ['4'],
    author: '3',
  },
];

const comments = [
  {
    id: '1',
    text: 'Quia repudiandae id quidem ex enim corporis impedit. Rerum occaecati voluptatem facilis quia animi veniam quasi alias. Exercitationem sed quia non voluptatum.',
    post: '1',
    author: '1',
  },
  {
    id: '2',
    text: 'Laudantium eos sit facere sed provident necessitatibus iure aut.',
    post: '1',
    author: '1',
  },
  {
    id: '3',
    text: 'Et veniam odio commodi et architecto. Fugiat unde quia ab sunt ad. Accusamus sit assumenda. Sint qui ipsam quae. Praesentium soluta autem et occaecati nemo. Molestias excepturi quia perferendis repellat et nulla tempora sed.',
    post: '2',
    author: '2',
  },
  {
    id: '4',
    text: 'Fugit quisquam dolor ducimus sint eum enim sunt nostrum. Minus sed rem consequatur laborum officia nobis soluta distinctio.',
    post: '3',
    author: '3',
  },
];

// Type definitions (schema)
const typeDefs = `
  type Query {
    comments: [Comment!]!
    posts(query: String): [Post!]!
    users(query: String): [User!]!
    me: User!
    post: Post!
  }

  type User {
    id: ID!
    name: String!
    email: String!
    age: Int
    posts: [Post!]!
    comments: [Comment!]!
  }

  type Post {
    id: ID!
    title: String!
    body: String!
    published: Boolean!
    author: User!
    comments: [Comment!]!
  }

  type Comment {
    id: ID!
    text: String!
    author: User!
    post: Post!
  }
`;

// Resolvers
const resolvers = {
  Query: {
    comments(parent, args, ctx, info) {
      return comments;
    },
    posts(parent, args, ctx, info) {
      const { query } = args;

      if (!query) {
        return posts;
      }

      const lower = (str) => str.toLowerCase();

      return posts.filter((post) => {
        const { title, body } = post;
        return (
          lower(title).includes(lower(query)) ||
          lower(body).includes(lower(query))
        );
      });
    },
    users(parent, args, ctx, info) {
      const { query } = args;
      if (!query) {
        return users;
      }

      return users.filter(
        (user) => user.name.toLowerCase() === query.toLowerCase()
      );
    },
    me() {
      return {
        id: 'abc123',
        name: 'Max',
        email: 'max@google.com',
      };
    },
    post() {
      return {
        id: 'post123',
        title: 'Title for the post',
        body: 'Body for the post...',
        published: true,
      };
    },
  },
  Post: {
    author(parent, args, ctx, info) {
      return users.find((user) => user.id === parent.author);
    },
    comments(parent, args, ctx, info) {
      return comments.filter((comment) => comment.author === parent.id);
    },
  },
  User: {
    posts(parent, args, ctx, info) {
      return posts.filter((post) => post.author === parent.id);
    },
    comments(parent, args, ctx, info) {
      return comments.filter((comment) => comment.author === parent.id);
    },
  },
  Comment: {
    author(parent, args, ctx, info) {
      return users.find((user) => user.id === parent.author);
    },
    post(parent, args, ctx, info) {
      return posts.find((post) => post.id === parent.post);
    },
  },
};

const server = new GraphQLServer({
  typeDefs,
  resolvers,
});

server.start(() => {
  console.log('The server is up!');
});
```
