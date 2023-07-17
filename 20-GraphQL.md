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
  rootValue: graphqlResolver,
  graphiql: true // => access graphql in localhost playground with it.
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
- Store data in database
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

  type RootQuery {
    hello: String
  }

  type RootMutation {
    createUser(userInput: UserInputData) : User!
  }
  
  schema {
    query: RootQuery
    mutation: RootMutation
  }
`);

/// resolvers.js
const User = require("../models/user");
module.exports = {
  createUser: async function ({ userInput }, req) {
    const existingUser = await User.findOne({email: userInput.email});
    if(!existingUser) {
      const error =  new Error('User exists already');
      throw error;
    }
    const hashedPw = await bcrypt.hash(userInput.password,12);
    const user = new User({
      email: userInput.email,
      name: userInput.name,
      password: userInput.hashedPw,
    });
    const createdUser = await user.save();
    return { ...createdUser._doc, _id: createdUser._id.toString() };
  }
}


/// localhost/graphiql

mutation {
  createUser(userInput : {email: "tesst@test.com", name: "Dan", password: "test123"}),
  {
    _id
    email
  }
}
```

# Adding Input validation.
- Validate in resolver
- npm i validator

```
/// resolver
const validator = require('validator');
module.exports = {
  createUser: async function ({ userInput }, req) {
    const errors = [];
    if(!validator.isEmail(userInput.email) {
      errros.push({message: 'E-mail is invalid.'});
    }
    if(validator.isEmpty(userInput.password) || !validator.isLength(userInput.password, { min: 5 })) {
      errors.push({message: 'Password too short'});
    }
    if(errors.length > 0) {
      const error = new Error('Invalid Input');
      error.data = errors;
      error.code = 422;
      throw error;
    }

    const existingUser = await User.findOne({email: userInput.email});
    if(!existingUser) {
      const error =  new Error('User exists already');
      throw error;
    }
    const hashedPw = await bcrypt.hash(userInput.password,12);
    const user = new User({
      email: userInput.email,
      name: userInput.name,
      password: userInput.hashedPw,
    });
    const createdUser = await user.save();
    return { ...createdUser._doc, _id: createdUser._id.toString() };
  }
}



```

```
/// app.js
app.use('/graphql', graphqlHttp({
  schema : graphqlSchema,
  rootValue: graphqlResolver,
  graphiql: true // => access graphql in localhost playground with it.
  formatError(err) {
    if(!error.originalError) {
      return err;
    }
    const data = err.originalError.data;
    const message = err.message || 'An Error occurred';
    return {message, status: code, data};
  }
}));

```

# Connect FE
```
/// APP.js
const graphqlQuery = {
  query: `
    mutation {
      createUser(userInput: {
        email: "${authData.signupForm.email.value}"},
        name: "${authData.signupData.name.value}",
        password:"${authData.signupForm.password.value}"}
      ) {
        _id
        email
      }
    }
  `
}
fetch('url', {
  method: 'POST',
  headers: {
    "Content-Type': 'application/json'
  },
  body: JSON.stringify(graphqlQuery)
})
.then(resData => {
  if(resData.errors && resData.errors[0].status === 422) {
    throw new Error()
  }
});

/// back end app.js
app.use((req, res, next) => {
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader(
    'Access-Control-Allow-Methods',
    'OPTIONS, GET, POST, PUT, PATCH, DELETE'
  );
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
  if(req.method === "OPTIONS") {
    return res.sendStatus(200);
  }
  next();
})

```

- Graphql automatically declines anything which is not a post or get request.
- So options request iss denied. Browser usually send OPTIONS request for check method is allowed.

# Add Login Query.

```
/// schema.js

type AuthData {
  token: String!
  userId: String!
}

type RootQuery {
    login(email: String!, password: String!) : AuthData!
}

/// Resolver.js
const jwt = require('jsonwebtoken');

login: asycn function({email, password}) {
  const user = await User.findOne({email: email});
  if(!user) {
    const error = new Error('User not found!');
    error.code = 401;
    throw error;
  }
  const isEqual = await bcrypt.compare(password, user.password);
  if(!isEqual) {
    const error = new Error('Pass is incorrect!');
    error.code = 401;
    throw error;
  }
  const token = jwt.sign({
    userId : user._id.toString(),
    email: user.email
  }, 'somesecretkey', {expiresIn: '1h'});
  return { token : token, userId: user_id.toString() };
}


/// FE Login
const graphqlQuery = {
  query: `
    {
      login(email: "${authData.email}", password: "${authData.}") {
        token
        userId
      }
    }
  `
}

fetch('url', {
  method: "POST",
  headers: {
    'Content-Type: 'application/json'
  },
  body: JSON.stringify(graphqlQuery)
})
.then(res => res.json())
.then(resData => {
  if(resData.errors && resData.errors[0].status === 422) {
    throw new Error()
  }
})

```
# Upload image 
- Graphql only work with json.

```
app.put('/post-iamge', (req, res, next) => {
  if(!req.file) {
    return res.status(200).json({message: 'No file provided'});
  }
  if(req.body.oldPath) {
    clearImage(req.body.oldPath);
  }
  return res.status(200).json({message: 'File stored', filePath: req.file.path});
})

```

# Using Variables.


```
/// FE
const graphqlQuery = {
  query : `
    query FetchPost($page: Int) {
      posts(page: $page) {
        _id
        title
        content
        imageUrl
        creator {
          name
        }
        createdAt
      }
      totalPosts
    }
  `,  
  variables : {
    page: page
  }
}


```















