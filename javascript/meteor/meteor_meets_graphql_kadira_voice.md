# Meteor Meets GraphQL -- KADIRA VOICE

[Original URL](https://voice.kadira.io/meteor-meets-graphql-3cba2e65fd00#.pzdd33ozl)

> We have been using GraphQL at Kadira for about four months. Everyone at Kadira loves it. We introduced Learn GraphQL online course in October, which allows you to master the basics of GraphQL in...

We have been using [GraphQL](http://graphql.org/) at [Kadira](https://kadira.io/) for about four months. Everyone at Kadira loves it. We introduced [Learn GraphQL](https://learngraphql.com/) online course in October, which allows you to [master](https://learngraphql.com/reviews) the basics of GraphQL in under 2 hours. Now, this is our attempt to bring GraphQL to **Meteor**.

> **This was supposed to be Kadira's Christmas gift to Meteor developers. Anyway, we couldn't deliver this on time.**

> **So, this as our New Year's gift for 2016\. Have fun!**

In my post on [**Why Meteor needs a new data layer**](https://kadira.io/blog/meteor/why-meteor-needs-a-new-data-layer), I pointed out why we need to think about Meteor's data stack again. MongoDB is **good** enough to get started. But when we are scaling our app, we need to integrate with **different kinds of databases** and other data sources.

That's where GraphQL is going to help us. It's an [application-layer query language](https://learngraphql.com/basics/introduction) that is independent of any database or backend. So, we can easily integrate it with **any database** we like.

Additionally in the client, we can define data requirement for our UIs declaratively. That's pretty useful when building large applications.

> If you are new to GraphQL, look at the [first six lessons](https://learngraphql.com/basics/introduction) of our Learn GraphQL course.

Before we look at the GraphQL Meteor integration, check out this demo app. It's a simple blog app that uses GraphQL as the data layer. It has all the essential features of a modern JavaScript app:

- Querying in client

- Client-side caching

- Mutations

- Optimistic updates

- Authorization

See it live at [http://graphql-blog.meteor.com](http://graphql-blog.meteor.com/) or watch [this](https://www.youtube.com/watch?v=rTgk7cDbRG0) video:

> Here's the [source code](https://github.com/kadira-samples/meteor-graphql-demo) of the app.

Meteor is all about the developer experience. GraphQL also comes with a great developer experience with it's **declarative data fetching** and it's in-browser IDE, [GraphiQL](https://github.com/graphql/graphiql). We wanted to bring both of these experiences together.

Add the [**kadira:graphql**](https://github.com/kadirahq/meteor-graphql) package into your app with:

```
meteor add kadira:graphql
```

Then start your app and visit <http://localhost:3000/graphql> to see your GraphQL schemas (We assume your app runs in port 3000).

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*ACeeR_WMgLg1LiUhsWs3vg.png)

> This page is available in the development version of your app only.

We've a default schema called **SysInfo**, which exposes some system information to your app. You can play with the schema by opening the in-browser GraphQL IDE.

> Simply click on the schema name of the above page: <http://localhost:3000/graphql>.

In that, try querying the following schema:

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*ZiXcwJmjOU3DBrT7Sn2hGQ.gif)

If you create a GraphQL schema then you can simply register it with Meteor with the API [**GraphQL.registerSchema()**](https://github.com/kadirahq/meteor-graphql/#graphqlregisterschema-server-only) on the server. Then you can access it via the above IDE and inside the client.

[Click here](https://github.com/kadira-samples/meteor-graphql-demo/blob/e17d8ed5907ea9790ee7e421cfe13ae3b40bd13e/server/schema.js#L90) on how to do it.

For the client-side data fetching, we are going to use [Lokka](https://github.com/kadirahq/lokka). It's a GraphQL client that works everywhere (inside the client, the server and React Native). Lokka is already bundled with **kadira:graphql**.

Now we are going to display our app's system information in the UI. For that, we are going to use **React**. So add React to your app with:

```
meteor add react
```

Then create a file called **client/app.jsx** and let's start writing our app.

> First, we need to create a Lokka client that is linked with our SysInfo schema:

```
SysInfoSchema = GraphQL.createLokkaClient('SysInfo');
```

> Then we need to create a simple React component to display the system information:

Here we use React's [stateless components](https://medium.com/@joshblack/stateless-components-in-react-0-14-f9798f8b992d#.w4enkzapw), which come with React 0.14.3.

> Then let's define our data requirements for the above component:

Here we create a [GraphQL fragment](https://learngraphql.com/basics/fragments) on type SysInfo in our schema.

> Now it's time to bind the query with the above component:

Here we define a GraphQL query with a fragment to fetch data. This is very similar to what we've done in the in-browser IDE.

Then we use a React utility called [**GraphQL.bindData()**](https://github.com/kadirahq/meteor-graphql/#graphqlbinddata-client-only), which watches for changes in a query and re-renders the component with the data. It's **not related** to GraphQL in any way. It's a general purpose React utility for binding data to a component.

Now let's render our **SysInfoContainer** to the UI. For that, do this:

It's good if we can see the latest system information. For that, we can do something like this:

Now, every 2 seconds, we'll ask Lokka to refetch the query. Then it'll update the cache and eventually, the UI will be updated.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*7qEk7yzjC8pfxfGfzHC8cQ.gif)

Checkout [this repo](https://github.com/kadira-samples/hello-graphql-meteor) for the complete app.

This is just a simple use of GraphQL. You can use GraphQL to build a real app with authentication and other features. For that, refer to the following links:

- [Learn GraphQL](https://learngraphql.com/)

- [Simple Blog App](https://github.com/kadira-samples/meteor-graphql-demo)written with Meteor & GraphQL

- [Meteor GraphQL integration](https://github.com/kadirahq/meteor-graphql)

- [Lokka API](https://github.com/kadirahq/lokka)

This is just the start. We are going to develop GraphQL support for Meteor in **two** ways.

On the server side, we'll have easy ways to define new schemas. But most of them will be **NPM packages**. We'll also work on support for **real-time** GraphQL. That'll have some Meteor-specific stuff.

Real-time support will be based on **application-specific events**. So, we could plug in any database with a queue like Kafka (or even Redis) to make our apps real-time.

> This is the how we scale non-Meteor apps. We may need to do a bit more than how we handle real-time behavior in Meteor. But, it's totally worthwhile in the long run.

The client-side version of GraphQL won't be Meteor specific at all. Most of the development will be improvements to [Lokka](https://github.com/kadirahq/lokka). We'll also invest in some tools specific to React and Tracker.

You can even use [Facebook's Relay](https://facebook.github.io/relay/) for much deeper React integration.

We always had ways to integrate Meteor with different databases. That's with custom publications and Meteor methods.

With GraphQL, we've a proper way to do this. We can reuse schemas available in NPM or even use schemas written in other languages. This will **redefine** microservices as **micro-schemas**. I'll talk more them about in a different post.

Imagine a situation like this:

- Backend developers write GraphQL schemas and types for different use-cases (blogs and chat apps). Anyone can get them via NPM.

- We can even access third-party APIs via GraphQL. We could use them inside our apps, just like above schemas.

- Client-side developers can build great user interfaces for GraphQL schemas. They'll also be available via NPM.

> This will lead to better products, increased productivity and more collaboration.

The best thing is, this is not the future. **It's already started.** That's why we need to focus more on **GraphQL**.
