---
title: "Introduction to GraphQL"
description: "My Stripe writing assignment"
lead: "Your first steps with GraphQL services."
date: 2020-11-28T08:48:57+00:00
lastmod: 2020-11-28T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "stripe written project"
weight: 100
toc: true
---

## Getting started

GraphQL is an API query language and runtime environment. Backed by your existing code and data, GraphQL is both database and storage engine agnostic.

This guide introduces core GraphQL concepts and shows how to create a GraphQL service. 

{{< alert icon="👉" text="No prior knowledge of GraphQL is required, though some familiarity with query languages will benefit you throughout this guide." />}}

## Your first GraphQL Service

GraphQL services require [object types, fields](https://graphql.org/learn/schema/#object-types-and-fields), and functions. With these three building blocks, you can create a GraphQL service by following these steps:

1. Define an object type.
2. Define fields on the object type.
3. Provide functions for each field and type.

### Defining object types

For example, suppose you wanted to create a GraphQL service that identifies logged-in users and their usernames. You'd begin by defining two object types, `Query` and `User`:

```
type Query {
}

type User {
}
```

Next, you'll create fields for these two objects.

### Defining object type fields

GraphQL object types require at least one field, which you'll nest within the object type's brackets.

Add the `me` field to the `Query` object and the `id` and `name` fields to the `User` object, like in this example:

```
type Query {
  me: User
}

type User {
  id: ID
  name: String
}
```

### Adding functions

With your object types and fields defined, you can now create functions for each field type. For example, the following functions will validate and execute queries that refer to the types you created in the previous step:

```
function Query_me(request) {
  return request.auth.user;
}

function User_name(user) {
  return user.getName();
}
```

## Executing queries

Your first service is now ready to receive, validate, and execute queries.

Once the service receives a query, it first verifies that the query refers to the right object types and fields. After verification, it runs the service's function to provide the query's result.

For example, suppose the service receives the following query:

```
{
  me {
    name
  }
}
```

The service could return a JSON result like the following:

```json
{
  "me": {
    "name": "Paul Wooley"
  }
}
```

## Next Steps

Having just created your first GraphQL service, you're now ready to explore ways to add greater complexity to services that will best suit your use case.

To learn more about fields and arguments you can use in your queries, read the [GraphQL Queries and Mutations documentation](https://graphql.org/learn/queries/).

If you already have a project in mind that you'd like to implement using GraphQL, view our [language and tools page](https://graphql.org/code/) to select a GraphQL-supported library.

## Need help?

Reach out to us with any questions you may have. [Help →](https://graphql.org/foundation/contact/).
