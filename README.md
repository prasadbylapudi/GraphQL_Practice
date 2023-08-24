# GraphQL_Practice

# GraphQL

# GraphQL is a powerful query language.

Allows for more flexible and efficient approach than REST.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/12bdb03c-ae32-45b0-8350-cf968ac15cd9/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ed9bcfb8-58b0-4c98-b779-8b1085737817/Untitled.png)

If you want only few things

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9cf50e14-fa81-45de-b423-f738f934fa89/Untitled.png)

# REST API Drawbacks

### Over Fetching:

Getting back more data than we need.

### Under Fetching:

Getting back less data than we need.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d70a24de-9395-4d6e-afa9-e1a6f4c01994/Untitled.png)

# GraphQL( graph query language)

### Single Endpoint

mygraphqlsite.com/graphql

```graphql
Query{
	Courses:{
		id,
		title,
		thumbnail_url
	}
}
```

```graphql
Query{
  Course: {
    id,
    title,
    thumbnail_url,
    author: {
      id,
      name,
      courses: {
      id,
      title,
      thumbnail_url
	    }
    }
   
  }
}

```

we can do Mutations 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b93691bc-f65c-4256-8a0a-bb44a53b5a8f/Untitled.png)

# Install Dependencies

`npm install @apollo/server graphql`

Applications that run Apollo Server require two top-level dependencies:

- `[graphql](https://npm.im/graphql)` (also known as `graphql-js`) is the library that implements the core GraphQL parsing and execution algorithms.
- `[@apollo/server](https://www.npmjs.com/package/@apollo/server)` is the main library for Apollo Server itself. Apollo Server knows how to turn HTTP requests and responses into GraphQL operations and run them in an extensible context with support for plugins and other features.

### Define a Resolver

We've defined our data set, but Apollo Server doesn't know that it should *use* that data set when it's executing a query. To fix this, we create a **resolver**.

```graphql
// Resolvers define how to fetch the types defined in your schema.
// This resolver retrieves books from the "books" array above.
const resolvers = {
  Query: {
    books: () => books,
  },
};
```

## **[Step 6: Create an instance of `ApolloServer`](https://www.apollographql.com/docs/apollo-server/getting-started#step-6-create-an-instance-of-apolloserver)**

We've defined our schema, data set, and resolver. Now we need to provide this information to Apollo Server when we initialize it.

Add the following to the bottom of your `index.ts` file:

```graphql
// The ApolloServer constructor requires two parameters: your schema
// definition and your set of resolvers.
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Passing an ApolloServer instance to the `startStandaloneServer` function:
//  1. creates an Express app
//  2. installs your ApolloServer instance as middleware
//  3. prepares your app to handle incoming requests
const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
});

console.log(`🚀  Server ready at: ${url}`);
```

### [Field nullability](https://www.apollographql.com/docs/apollo-server/schema/schema#field-nullability)

By default, it's valid for any field in your schema to return `null` instead of its specified type. You can require that a particular field *doesn't* return `null` with an exclamation mark `!`, like so:

```graphql
type Author {
  name: String! # Can't return null
  books: [Book]
}
```

## [Supported types](https://www.apollographql.com/docs/apollo-server/schema/schema#supported-types)

Every type definition in a GraphQL schema belongs to one of the following categories:

- [Scalar](https://www.apollographql.com/docs/apollo-server/schema/schema#scalar-types)
- [Object](https://www.apollographql.com/docs/apollo-server/schema/schema#object-types)
    - This includes the three special **root operation types**: `[Query](https://www.apollographql.com/docs/apollo-server/schema/schema#the-query-type)`, `[Mutation](https://www.apollographql.com/docs/apollo-server/schema/schema#the-mutation-type)`, and `[Subscription](https://www.apollographql.com/docs/apollo-server/schema/schema#the-subscription-type)`.
- [Input](https://www.apollographql.com/docs/apollo-server/schema/schema#input-types)
- [Enum](https://www.apollographql.com/docs/apollo-server/schema/schema#enum-types)
- [Union](https://www.apollographql.com/docs/apollo-server/schema/schema#union-and-interface-types)
- [Interface](https://www.apollographql.com/docs/apollo-server/schema/schema#union-and-interface-types)

# Scalar Types:

GraphQL's default scalar types are:

- `Int`: A signed 32‐bit integer
- `Float`: A signed double-precision floating-point value
- `String`: A UTF‐8 character sequence
- `Boolean`: `true` or `false`
- `ID` (serialized as a `String`): A unique identifier that's often used to refetch an object or as the key for a cache. Although it's serialized as a `String`, an `ID` is not intended to be human‐readable.

# Object Types:

```graphql
type Book {
  title: String
  author: Author
}

type Author {
  name: String
  books: [Book]
}
```

## Writing Queries

```graphql
query GamesQuery {
    reviews {
      id,
      rating
    }
}
```

## What if we want a single author?

```graphql
type Query{
        reviews: [Review]
        review(id: ID!): Review
        games: [Game]
        authors: [Author]
    }
```

in the resolver

```graphql
const resolvers = {
    Query: { 
        games() {
            return db.games
        },
       reviews() {
        return db.reviews
        },
        //takes three parameters, 1st - parent, 2nd- args, 3rd- context object.
        review(_,args){
            return db.reviews.find((review)=>review.id===args.id)
        },
        authors() {
        return db.authors
        },
         

    },
}
```

//frontend queryt making way

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/33d1ef7b-d197-4f16-befd-0864a6fb1b5d/Untitled.png)

```graphql
query ReviewsQuery($id: ID!) {
   review(id: $id) {
     rating,
     content
   }
}
```

## Finding Reviews for a particular Game:

we have to write  resolver for this also.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1f97ef04-92e4-4935-a87e-325faf4761e4/Untitled.png)

Here we have to mention the relations here.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f29e2d9d-ccaf-4b3e-8552-a07191881acb/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a6e1f8a-5a99-4fdf-a83c-4c6801995640/Untitled.png)

## Resolver chain.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c47042be-cbe2-4355-b1e1-317dc629f85f/Untitled.png)

# Mutations:

```graphql
type Mutation {
    addGame(title: String!, platform: [String!]!): Game
    addReview(rating: Int!, content: String!, author_id: ID!, game_id: ID!): Review
    addAuthor(name: String!, verified: Boolean!): Author
    deleteGame(id: ID!): [Game]
    deleteReview(id: ID!): Review
    deleteAuthor(id: ID!): Author

  }
```

### we have to write resolvers for the deleteGame mutations

```graphql
Mutation:{
      deleteGame(_, args){
            db.games = db.games.filter((game) => game.id !== args.id)
            return db.games
      }
  }
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b7f2cec6-881f-40a9-8524-6dbaf94295a0/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/390cb479-0299-4148-9e26-0d1ed2e82210/Untitled.png)

# Updating Existing Resource:
