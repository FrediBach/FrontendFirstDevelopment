# Frontend First Development

#projects/frontendfirstdevelopment

www.frontendfirstdevelopment.com

## TL;DR
Solve friction between backend and frontend with an API mocking server, GraphQL, automatic server side rendering and a schema first headless CMS of your choice.

## About
Back in the day, web development was a lot less complex. Those days the backend basically was the frontend, today the backend and frontend are two distinct and often rather complex entities. ::Syncing frontend templates and assets with backend rendered markup is often anything else than straight forward.:: Depending on the CMS driving your site, you get more or less container markup wrapping the data outputted by different modules and either you overwrite a lot of server templates (if that is even possible!) or you adapt your CSS (not always possible either with flex based layouts) to integrate your markup.

::But thankfully there’s finally a promising path out of this mess!:: By doing as much as possible on the frontend, ideally component based so you can abstract complexity into smaller more focused pieces, and than render the routes and markup with a server side rendering framework, you never again have to sync markup from different sources. And by additionally mocking the API server, we can flexibly and much faster tinker with the project till everything is perfect, without first implementing the feature on the backend, making us more responsive and our clients happy.

Let’s be honest, letting the backend render markup was never a great idea anyway. Not only does it create the described friction between frontend and backend, it also isn’t really the expertise of backend developers, especially when it comes to things like accessibility and above the fold performance optimisations. ::A frontend first development workflow can solve us all these problems:: and this place here will try to do it’s best to point you in the right directions.

## Why
Let’s first have an overview why we actually need to move to a frontend first development workflow by comparing its advantages and possible disadvantages.

### Advantages:

* ::Less friction between FE & BE::: Because a headless CMS only manages contend and delivers it to the frontend over ideally a GraphQL API, you never need to sync markup between frontend & backend and as GraphQL APIs are auto documenting, you always know exactly what’s available to you as a frontend developer.
* ::Enables tinkering::: As you are mocking the API, changes can be implemented much faster than actually building the backend service first. So fast actually, that you can do live tinkering together with the client, right on the product.
* ::Easier debugging::: Moving most of the business logic to the frontend and having a strongly typed auto documenting API makes it very clear where an error comes from. In 99% of all cases it will originate from your frontend code and you can debug it at one single place. Strongly typed languages like TypeScript or Reason ML or even Elm can tell you where bugs are even while you code.
* ::Client friendly::: Clients often don’t have the expertise to judge an abstract technical concept without something they can interact with. By developing frontend first, clients can judge a feature as realistically as possible before any backend is implemented, making sure that stakeholders involved make informed decisions.
* ::More focused backends:: Frontend first enables the backend to focus and optimise the right thing. By knowing exactly what the frontend queries are, for example with the help of Apollo Engine, they can add caching layers exactly where needed and use the freed up time to optimise the content management forms, delivering the best experience to clients.
* ::Skill targeting:: Frontend developers are the experts on writing HTML5, accessibility, optimising above the fold performance ... why should the backend developers ever render markup for them?
* ::Focus on the right thing::: With frontend first, you spend most of the development time where it counts, the thing people see and interact with, and automate as much of the rest as possible.

### Possible disadvantages:

* ::Frontend developer maturity::: Designing normalised data structures is traditionally a backend job, so frontend developers need to acquire this skill first so they can perfectly mock API services. But looking at what the frontend community achieved in recent years, I’m not at all worried that this will be a big issue. It is no surprise that the frontend community made the Flux paradigm popular again in favour of MVC, solving many issues of the past.
* ::Works better with a serial workflow::: Ideally, the backend starts to build the real GraphQL API right after the frontend has implemented all features. While there are often external API integration tasks the backend can focus in the meantime, it definitely can happen that the backend is blocked by the frontend, waiting for an implementation. But to be honest, up till now, the frontend was often blocked by the backend for the same reasons, so this is not exactly a new issue after all.

## Less friction between FE and BE
Let’s have a closer look at one of the pros, one that creates a lot of issues with current workflows, to get a better idea why frontend first is such a tempting idea.

### Usual developer workflow:

**Path 1:** Information architecture  -> technical specification -> backend / DB -> frontend integration
**Path 2:** Information architecture -> design -> design system -> CSS framework -> frontend implementation (prototype)

::This workflow creates friction when both paths meet!::

### Frontend First Workflow:

**One path:** Information architecture -> Design -> (Optional: Design System -> Component Library) -> Spec (API mocking) -> Frontend -> (Schema) -> Backend (Admin Forms and DB)

::Only one development path, no friction!::

- [ ] Create a timeline graph illustrating how the tasks can overlap and how client interaction happens and changes things.

Information architecture and technical specification is mostly a first draft. Than changes happen after the client can interact with the frontend or living style guide, while iterating till every stakeholder is happy. Without a frontend first approach, this leads to friction everywhere. Even content creation is more and more frontend first focused these days, just think of headlines that have to be limited to certain lengths to match the design and use fitting word lengths to break nicely in the limited space designed for that element.

## Key technologies
Thanks to some key technologies of recent years like GraphQL and all the tooling around it, we are now pretty close to make a truly frontend first workflow possible!

### API server mocking

The first key technology is API server mocking. On a frontend first workflow, you want to start working before any backend is set up. Traditionally we used hardcoded data and mocked JSON endpoints to mock our API endpoints, but ::these days it’s now possible to mock a whole REST or GraphQL server with just some sample data::. These kind of servers support all the CRUD operations (create, read, update and delete) and especially with GraphQL, can feel like the real thing, but with almost no effort. This is very important, as you can create prototypes that are ::so close to the real thing, that your client can get a very realistic impression of the final product, without ever having to first setup a backend CMS.:: The other important feature of this approach, is that you can design your sample data so realistically, that you can test frontend edge cases, for example different content lengths, right from the beginning. As you’re working with sample data, changes to your API are done in seconds, even changing normally complicated stuff like relationships, becomes really easy.

Let’s have a look at an example. The following sample data defines a blogging API where users have posts and posts can have comments and comments can be replies to other comments.

```
module.exports = {
    users: [
        {
            id: 123,
            name: "John Doe"
        },
        {
            id: 456,
            name: "Jane Doe"
        }
    ],
    posts: [
        {
            id: 1,
            title: "Lorem Ipsum",
            content: "Content of post 1 ...",
            views: 254,
            user_id: 123,
            date: '2018-03-23'
        },
        {
            id: 2,
            title: "Sic Dolor amet",
            content: "Content of post 2 ...",
            views: 65,
            user_id: 456
        }
    ],
    comments: [
        {
            id: 987, 
            post_id: 1, 
            user_id: 123, 
            body: "First commment ...", 
            date: '2018-03-24'
        },
        {
            id: 995,
            post_id: 1,
            user_id: 456,
            body: "Second comment ...",
            date: '2018-03-25'
        },
        {
            id: 452,
            post_id: 1,
            user_id: 456,
            comment_id: 987,
            body: "This is a reply",
            date: '2018-03-27'
        }
    ]
};
```

Of course in a real world app, you would add more realistic content (no lorem ipsum!) and more entries for each type to make it more realistic.

However, ::this small sample data file already defines quite a few features that would normally take quite a while to implement.:: For example there are multiple relationships between types, like that each user can have posts and comments assigned to him. Each post can have multiple comments and comments can reference another comment (replies). Some fields are optional, for example “comment_id” on the comment type as a post can have no comments.

Further, if we have to add a new field to our schema, all we need to do is add that field to the sample data, for example an email field on the user type:

```
...
    users: [
        {
            id: 123,
            name: "John Doe",
            email: "john.doe@mail.com"	
        },
        {
            id: 456,
            name: "Jane Doe",
            email: "jane.doe@mail.com"	
        }
    ],
...
```

Just restart the mocking server, and that’s it! Same thing for relationships, just add it to your sample data, restart the mocking server and you’re done. It couldn’t be easier and faster (well, beside adding a watcher to the mocking server so you don’t have to restart it after every single change), making it possible to ::dynamically alter your backend without actually writing any backend code, making it possible to iterate fast and even tinker live on the source together with your client!::

#### API mocking server tools:

* GraphQL:
	* JSON GraphQL Server (using sample data) -> https://github.com/marmelab/json-graphql-server
	* GarphQL Faker (faking data based on directives) -> [GitHub - APIs-guru/graphql-faker: 🎲 Mock or extend your GraphQL API with faked data. No coding required.](https://github.com/APIs-guru/graphql-faker)
* REST:
	* JSON Server -> [GitHub - typicode/json-server: Get a full fake REST API with zero coding in less than 30 seconds (seriously)](https://github.com/typicode/json-server)

Our recommendation is of course to use the GraphQL variant, as it has multiple advantages like having just one very flexible endpoint, no over and under fetching and auto documentation and autocomplete for all possible queries and fields.

### GraphQL

In a real world application, where performance counts, REST runs into problems pretty fast. What normally happens on every project, is that you start implementing custom endpoints for pretty much everything, to prevent over and under fetching and especially having to call multiple endpoints to get all the data you need. Thankfully GraphQL solves all these problems. ::Not having to write custom endpoints, makes the switch from a mocked API to the real thing extremely easy.:: In most cases all you need to change is the url to the real API. Here is an example query for the sample data posted above:

```
query recentPosts {
    allPosts (
        sortField: "date",
        sortOrder: "DESC",
        page: 0,
        perPage: 10
    ) {
        id
        title
        content
        views
        date
        User {
            id
            name
        }
    }
}
```

With a REST API, this simple query could already end up in a maximum of 11 requests to the server as you would have to do one extra request for each unique user, definitely not something you want to happen on a mobile connection! That’s why in any realistic real world REST setup, you would create a custom endpoint for this case, but with GraphQL that isn’t needed at all, allowing us to easily mock the API in a realistic way.

For consuming a GraphQL endpoint, I highly recommend Apollo. It hits a sweet spot between advanced features like caching, optimistic UI, refetching etc. and ease of use and it’s where the community is at right now, so you have nice documentation, tons of articles and a great community in case you run into problems. For very simple APIs and frontends, Lokka can be a great alternative.

* **Apollo:** [Apollo GraphQL | Learn about the Apollo platform: Client, Engine, GraphQL servers, GraphQL support, and more.](https://www.apollographql.com/)
* **Lokka:** https://github.com/kadirahq/lokka

I would personally stick with Apollo, as it integrates nicely with server side rendering frameworks, as we’ll see next and has it’s own caching proxy solution that you can use out of the box.

In case you don’t expect to use a CMS to manage the data, you can relatively easily setup a real GraphQL server. Have a look at GraphQL engines, that make it possible to setup real database driven endpoints without too much effort:

* **Prisma:** [Prisma | Open-Source GraphQL ORM for GraphQL Servers](https://www.prisma.io/)
* **Hasura:** [GraphQL on Postgres | Hasura](https://hasura.io/)
* **PostGraphile**: [Graphile | Powerful, Extensible and Performant GraphQL APIs Rapidly](https://www.graphile.org/postgraphile/)
* **GrandStack:** [GRANDstack · Build full stack graph applications with ease.](https://grandstack.io/)

Those are great options if you’re building all functionality by yourself (or your team), without a CMS managing your data, but I wouldn’t do it initially, as it makes tinkering with your data quite a bit more involved.

### Headless CMS

If you need a strong admin interface for content management, with approval workflows, the flexibility to integrate other services and other enterprise requirements, you normally don’t build everything yourself from scratch, you choose an enterprise level CMS that can handle all of that stuff already. ::Sadly all the currently most popular CMS solutions on the market aren’t very well suited for a frontend first workflow.:: But thankfully, new contenders are entering the market and are now realising the potential of a schema based GraphQL API approach. Here’s a list of possible options:

* **Contentful**: [Contentful: Content Infrastructure for Digital Teams](https://www.contentful.com/)
* **GraphCMS**: [GraphCMS - The GraphQL Headless CMS](https://graphcms.com/)
* **Mozaik**: [Mozaik | Next generation headless GraphQL CMS](https://www.mozaik.io/)
* **GENTICS Mesh**: [Gentics Mesh - The open source headless CMS for developers](https://getmesh.io/)
* **Canner:** [Canner - Make CMS simple](https://www.canner.io/)
* **Relax CMS:** [GitHub - relax/relax: New generation CMS on top of React, Redux and GraphQL](https://github.com/relax/relax)
* **Dato CMS:** [Querying the API - Content Delivery API - DatoCMS](https://www.datocms.com/docs/content-delivery-api/querying/)

### Server Side Rendering

When going frontend first, your CMS or API backend should only deliver the content and not render the frontend routes and markup. ::Now you could build everything as a single page app (SPA), but that has some serious disadvantages when it comes to search engine optimisation (SEO)::, a must for client projects. Thankfully we have great options now that can solve this issue. The idea is that you build your frontend as you would in a SPA, but than have a SSR framework that can extract the routes from your SPA and render them server side. Two options:

* **Rouge:** [GitHub - alidcastano/rogue.js: Rogue.js -  a nearly invisible framework for creating server-rendered React applications](https://github.com/alidcastano/rogue.js)
* **Reframe**: [GitHub - reframejs/reframe: Framework to create web apps.](https://github.com/reframejs/reframe)
* **Next.JS:** [Next.js](https://nextjs.org/)
* **After.js:** [GitHub - jaredpalmer/after.js: Next.js-like framework for server-rendered React apps built with React Router 4](https://github.com/jaredpalmer/after.js)
* **Razzle:** [GitHub - jaredpalmer/razzle: ✨ Create server-rendered universal JavaScript applications with no configuration](https://github.com/jaredpalmer/razzle)
* **ReactQL:** [ReactQL: Modern stack for React+GraphQL](https://reactql.org/)
* **Nuxt (Vue based):** [Nuxt.js - Universal Vue.js Applications](https://nuxtjs.org/)

The nice thing with Rogue is that it guesses your endpoints based on your React Router 4 setup. This way you can build your project like a normal single page app, and ::Rogue will build the server side rendering part automatically based on your apps routes::, meaning you don’t have to define anything twice and changes to your routes are immediately reflected to your server side routes.

The other ones are less automatic, but can be great options, as well. Next.JS for example is quite battle tested by now, so you probably won’t run into early adopter issues.

## The Frontend First Development workflow
As we had a look at the needed technologies now, let’s have a closer look at the frontend first workflow itself:

### 1. Create realistic sample data for your project

This is where everything starts. Initially build a simple sample data file that contains all the types, relationships and has at least one entry per type that only contains the required fields, so that the mocking server can guess the schema correctly. As the project goes on, start to add additional more realistic data. Work as long as possible with sample data, so you can implement changes as fast as possible.

### 2. Run the API mocking server

You can now run the mocking server using your sample data locally using either GraphQL JSON Server or less recommended, JSON Server for a REST API.

```
json-graphql-server my-sample-data.js
```

### 3. Build your server side rendered app

Setup your frontend with one of the server side rendering frameworks that can automate the server rendering part, for example Rogue. All those frameworks make it possible to render your routes server side in a SEO friendly way, without having to setup that part yourself, making it possible for you to focus on building your frontend.

::For bigger projects, it is recommended to build your frontend on top of a living style guide, design system and component library.:: Here are some libraries that can help you with that:

* **Catalog:** [Catalog](https://www.catalog.style/)
* **Storybook:** https://storybook.js.org/
* **React Styleguideist:** [React Styleguidist: isolated React component development environment with a living style guide](https://react-styleguidist.js.org/)
* **Styled System:**: [Styled System](https://jxnblk.com/styled-system/) 

### 4. Iterate with the client till the frontend is perfect

With the new powers and flexibility you get using a frontend first development workflow, use this chance to iterate often and improve the product together with your client. An agile project setup is a must in a project like this. While the one path workflow at first looks a lot like waterfall, your project actually profits most from an agile workflow, where you iterate fast and often.

### 5. Generate the schema for the backend and let them build the real API server

Using your final sample data, you can now easily generate the GraphQL schema for the backend. From the same guy that made JSON GraphQL Server, you can use this library to automatically generate the schema: [GitHub - marmelab/graphql-schema-from-json: Guess a GraphQL schema from json data](https://github.com/marmelab/graphql-schema-from-json)

### 6. The backend generates (automatically or not) the admin interface based on the schema

This is the point where you start implementing the CMS or the admin forms for the API server. Sadly on most current CMS options, this isn’t an automatic process where you just import the schema and you’re done. This step still involves clicking your backend types together, but things are starting to change, making this a one click process like it should. If you’re running you project on a GraphQL engine, things are a little bit easier, but your entry forms won’t be as client friendly as with a CMS solution.

### 7. Connect the SPA to the real backend

As soon as the backend is ready, switch your GraphQL endpoint in your SSR rendered app to the real thing. Deploy and you’re done.

… and lastly …

## Are we there yet?
Almost.

There are still a few things that need to be improved before this approach can really take off. For example the GraphQL API mocking server still has some missing features:

* Realistic error handling
* Query performance simulation by adding delays based on the complexity and depth of a query
* Sort, filter and pagination on sub queries
* Automatic id’s and support for non integer id’s
* Simulation of a auth permission system

On the server rendering side of things, many tools are still quite new and not extremely battle tested.

::But overall it’s a good time to start building frontend first and help the community improve the available tools as much as you can.::

Looking at the available CMS solutions, it would be nice if you could import your sample data directly, or at least the generated schema, for a real schema first experience.

Talking about schema first development. You may wonder why a sample data first and not schema first approach is described here. Using schema directives to tell the server how to generate sample data could look like this:

```
type Blog @fake(amount: 10) {
  title: String @fake(type: "headlineMedium")
	comments: [Comment!]! @fake(min: 0, max: 100, distribution: "normal")
  ...
}
```

Or how GraphQL Faker does it: [GitHub - APIs-guru/graphql-faker: 🎲 Mock or extend your GraphQL API with faked data. No coding required.](https://github.com/APIs-guru/graphql-faker)

Personally I believe that you get much better results with realistically mocked data than with fake computer generated fields. I do think GraphQL Faker has it’s uses, maybe even by combining it with a sample data server, but ::working with clients and design driven development, realistic data is a must have.::

## Frontend First Community
If you like that idea of frontend first development, please join our new community on Reddit: r/frontendfirstdevelopment

And join the newsletter where we monthly post all the latest tools and articles about this topic.

Most importantly, help the community improve the tools, either by using them and post bug issues or feature request or by directly getting involved in the development of these tools! :-)