```
name:  Defining Mutations
bulletPackage: free
```

In GraphQL, mutations is the way to allow GraphQL clients(or external parties) to modify your dataset.

Defining mutations is much similar to defining queries. Only difference is how you implement the logic inside the mutation.

Let's define some mutations.

*****

```
id:     setting-up
type:   text
points: 5
```


## Setting Up

> This lesson is the continuation of the previous lesson [Defining Queries](/basics/defining-queries). We assume, you've already completed that lesson.

Clone our GraphQL Sandbox:

~~~
git clone https://github.com/kadirahq/graphql-blog-schema.git
~~~

Then checkout `defining-mutations` branch:

~~~
cd graphql-blog-schema
git checkout defining-mutations
~~~

Now, install dependencies and start the sandbox:

~~~
npm install
npm start
~~~

This will start a local version of our GraphQL Sandbox and you can access it from <http://localhost:3000>

![](https://cldup.com/MnoG2RvAja.png)

This Sandbox has a schema which looks similar to what we used in first few lessons. But, it's a cut down version which we've built in the previous lesson.

You can look at it by inspecting the `src/schema.js` in the cloned repo. That's the place where, we also going to write mutation related code.

*****

```
id:     hello-muatations
type:   mcq
points: 20
```


## Hello Mutations

Now we are going to write our first mutation. It's a mutation to add a new post to our blog. So, this is the mutation request we are going to invoke.

~~~
mutation insertFirstPost() {
  post: addNewPost(
    title: "GraphQL is Awesome",
    content: "Yep, it's purely awesome."
  ) {
    _id,
    title
  }
}
~~~

So, defining a mutation is just like defining a root query field. This is how to do it:

~~~
const Mutation = new GraphQLObjectType({
  name: "BlogMutations",
  description: "Mutations of our blog",
  fields: () => ({
    addNewPost: {
      type: Post,
      args: {
        title: {type: new GraphQLNonNull(GraphQLString)},
        content: {type: new GraphQLNonNull(GraphQLString)}
      },
      resolve: function(source, args) {
        let post = _.clone(args);
        // Generate the _id
        post._id = Date.now() + Math.ceil(Math.random() * 9999999);
        // Assign a user
        post.author = "arunoda";

        // Add the Post to the data store
        PostsList.push(post);

        // return the new post.
        return post;
      }
    }
  })
});
~~~

Now we need to assign this to our Schema. So, update our schema to be like this:

~~~
const Schema = new GraphQLSchema({
  query: Query,
  mutation: Mutation
});
~~~

That's all. Now try to invoke following mutation:

~~~
mutation insertFirstPost {
  post: addNewPost(
    title: "GraphQL is Awesome",
    content: "Yep, it's purely awesome."
  ) {
    _id,
    title
  }
}
~~~

You can check whether this post has been added or not by querying our schema.

--- 

Now, we've a simple task for you. 
Try to invoke following query.

~~~
mutation insertFirstPost {
  post: addNewPost(
    title: "GraphQL is Awesome"
  ) {
    _id,
    title
  }
}
~~~

What was the result?

  - Successfully added the post.
  - Successfully added the post, but there is an warning mentioning content field is required.
  - **Got an error mentioning content field is required.**
  - Nothing happens, there is a javascript console error.

*****

```
id:     answer-hello-muatations
type:   text
points: 5
```

Once we invoked that mutation, we'll get an error mentioning content field is required. That's because we've defined our arguments as required. See:

~~~
const Mutation = new GraphQLObjectType({
  ...
      args: {
        title: {type: new GraphQLNonNull(GraphQLString)},
        content: {type: new GraphQLNonNull(GraphQLString)}
      },
  ...
});
~~~

*****

```
id:     finally
type:   text
points: 5
```

## Finally

Now we've understand how to define a mutation in our schema. As you know already, a mutation is very similar to a root query field, but it mutates your underline dataset.

And after the mutation, it very important to return the modified document. You can learn more about this in later lesson and while learning [Relay](https://facebook.github.io/relay/).