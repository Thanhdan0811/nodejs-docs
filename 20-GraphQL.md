# GraphQL ?
- Like REST and with higher query flexibility.
- Sẽ chỉ gửi POST request.
- POST sẽ chứa các query expression (định nghĩa data cần được return).

```
{
  query { /// operation type
    user { /// endpoint
      name, /// request fields
      age
    }
  }
}

```
- POST /graphql sẽ cần định nghĩa 4 thứ :
  + Type Definition : 
  + Query => Retrieve Data. Like Routes.
  + Mutation => Manipulate Data (POST, PUT, PATCH, DELETE). Like Routes.
  + Subscritption => Set up realtime connection via Websocket. Like Routes.

- Sau đó sẽ kết nối với Resolvers (Server-side logic).

- # Setup
- npm i graphql express-graphql
- Check GraphQL.org

```
/// graphql/schema.js
const { buildSchema } = require('graphql');
module.exports = buildSchema(`
  type TestData {
    text: String!
    views: Int!
  }

  type RootQuery {
    // hello: String!
    hello : TestData
  }
  
  schema {
    query: RootQuery
  }
`);

/// graphql/resolver.js
module.exports = {
  hello() {
    // return "Hello World!";
    return {
      text: 'Hello World!',
      views: 1234
    }
  }
}

/// app.js
const { graphqlHttp } = require('express-graphql');
const graphqlSchema = require('./graphql/schema');
const graphqlResolver = require('./graphql/resolver');

app.use('/graphql', graphqlHttp({
  schema : graphqlSchema,
  rootValue: graphqlResolver
}));


// => Data will send :

{
  "query" : "{ hello { text views } }"
}

// => response

{
  "data" : {
    "hello" : {
      "text" : "Hello World!",
      "views" : 1234,
    }
  }
}
```



- resolver need methods that match with "type RootQuery" in schema.js

# Mutation Schema
```
/// graphql/schema.js
const { buildSchema } = require('graphql');
module.exports = buildSchema(`
  type Post {
    _id: ID!
    title: String!
    content: String!
    imageUrl: String!
    creator: User!
    createdAt: String!
    updatedAt: String!
  }

  type User {
    _id: ID!
    name: String!
    password: String
    status: String!
    posts: [Post!]!
  }

  input UserData {
    email: String!
    name: String!
    password: String!
  }

  type RootMutation {
    createUser(userInput: UserInputData) : User!
  }
  
  schema {
    mutation: RootMutation
  }
`);
```
