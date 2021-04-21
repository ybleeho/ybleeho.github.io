---
title: Why graphql
date: 2020-07-04 14:03:00
tags:
---



## What is GraphQL?

GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools.

## Why use GraphQL?

I often encounter such a situation ...

 "Hey , Hu bro there is xx field missing in your xx api..."

 "Hey, there is too many field in your api, i just need xx and xx field, please modify it..."

"Hey, i have to request three api in this page, would you please combine those in one API?..."

"Em....."

"Hey, Hu bro  the Api doc has not been updated...What paramater do i need to pass?"

What i want to say is the above problem solved by GraphQL.

## What is apollo Graphql?

GraphQL is just a query language. And in order to use it easily, we need to use a platform that will do all the heavy lifting for us. One such platform is provided by Apollo.

## Main process?

Describe your data

```
type Project {
  name: String
  tagline: String
  contributors: [User]
}
```

Ask for what  u What

```
{
  project(name: "GraphQL") {
    tagline
  }
}
```

Get predictable results

```
{
  "project": {
    "tagline": "A query language for APIs"
  }
}
```

## How to use?

1. Build a schema
   
   ```
   const userSchema = gql`
       type Query {
           getUser(id: ID): User
           getUsers: [User]
       }
   
       type Mutation {
           createUser(userInfo: CreateUserInput): User
       }
       
       input CreateUserInput {
           name: String
           age: Int
           address: String
       }
       
       type User {
           id: ID
           name: String
           age: Int
           address: String
           number: String
       }
   `;
   ```

2. Write query resolvers
   
   Briefly, `Query` for get request, `Mutation` for update request.
   
   ```
   const userResolvers = {
       Query: {
           getUser: async (parent, args, context, info) => await userLoader.getUser(args.id),
           getUsers: async (parent, args, context, info) => await userLoader.getUsers()
       },
       Mutation: {
           createUser: async (_, args, { dataSources }) => {
               return await userLoader.createUser(args.userInfo)
           }
       }
   };
   ```

3. Intergrating with your node middleare
   
   ```
   const apolloServer = new ApolloServer({
       typeDefs: schema,
       resolvers,
   });
   apolloServer.applyMiddleware({ app: app, path: '/graphql' });
   const port = process.env.PORT || 3333
   const server = app.listen(port, () => console.info(`Listening to http://localhost:${port}/graphql 🚀`));
   ```

[The entire code can be viewed here](https://github.com/ybleeho/koa_playground)

## Q&A

[How to  Modularizing your GraphQL?]([https://www.apollographql.com/blog/modularizing-your-graphql-schema-code-d7f71d5ed5f2](https://www.apollographql.com/blog/modularizing-your-graphql-schema-code-d7f71d5ed5f2))

[Error Handling](https://www.apollographql.com/docs/apollo-server/data/errors/)

[Security](https://www.apollographql.com/docs/apollo-server/security/authentication/)

[Intergration testing](https://www.apollographql.com/docs/apollo-server/testing/testing/)








