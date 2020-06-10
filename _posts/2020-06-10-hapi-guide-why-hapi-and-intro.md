---
title: "Hapi.js Guide: Why use Hapi.js as your Node.js framework and a quick intro to it"
date: 2020-06-10T13:13:00-3
author: Vinicius Teixeira
tags:
  - node.js
  - hapi.js
  - typescript
  - javascript
---

When starting a Node.js web project from scratch you have probably gone through the exercise of selecting a http framework. There is a myriad of options to choose from and this can be quite a challenging task. The framework's getting started guides tell just a fraction of the story and it is hard to assess the framework's flexibility, long-term maintainability or ecosystem.

I'll not write another framework comparison article, there are plenty of those already. Instead, I'll write a guide to my framework of choice, which is [Hapi.js](). We'll explore why Hapi is a good option as a Node.js framework, how to get started and how to organize your codebase for a medium to large application. Along the way we will introduce several modules (plugins in Hapi lingo) that are part of Hapi's ecosystem. We will use TypeScript from the get go and will develop a small app from scratch.

These are the planned and released articles:

- [Why use Hapi.js as your Node.js framework and a quick intro to it]() _published June 10, 2020_ **You are here**
- <ins>Request Lifecycle and Routing</ins> _coming soon_
- <ins>The HTTP interface is not your application - Extracting the core logic out of the controllers</ins> _coming soon_
- <ins>Testing the core logic and the controllers with Lab</ins> _coming soon_
- <ins>Adding a Database with Objection.js</ins> _coming soon_
- <ins>Configuring your app with Confidence</ins> _coming soon_
- <ins>Authentication</ins> _coming soon_
- <ins>Deep-dive into Plugins</ins> _coming soon_

## Why Hapi.js

Hapi was initially developed to handle Walmart's traffic on Black Friday scale. It has been around since around 2011 and it claims to be "trusted for simplicity, security and satisfaction".

So far, so good, but if you look at some numbers you might have second thoughts about using Hapi. It is not in the top three most popular frameworks either by npm weekly downloads or by Github start count. And some Node.js framework performance benchmarks show Hapi towards the bottom of the list.

Still, I firmly believe Hapi.js is one of (if not the) best options out there. Let's see why:

### Cohesive ecosystem

Despite of not figuring on the top of popularity ranks for Node.js frameworks, Hapi has an incredible ecosystem of modules that make developing web applications a breeze. Many of these modules are maintained by core contributors to Hapi itself and are part of the Hapi Github organization and Npm's Hapi namespace.

This ecosystem makes it much easier when adding a new functionality to you application. Are you serving static files? [Inert](https://github.com/hapijs/inert) will do it for you. Need to add websockets functionality, [Nes](https://github.com/hapijs/nes) gets you covered. It's time to add some caching to your app? [Catbox](https://github.com/hapijs/catbox) will help you, regardless if you are using [in-memory](https://github.com/hapijs/catbox-memory), [redis](https://github.com/hapijs/catbox-redis) or [memcached](https://github.com/hapijs/catbox-memcached) cache.

In my experience with other frameworks, just picking a library to add some functionality can take from a day to full weeks of testing and comparing competing packages. In Hapi it is the contrary - it is very common to find what you need in a well-maintained plugin.

### Sane request lifecycle

Some of the most popular Node.js frameworks out there use the [middleware](https://dzone.com/articles/understanding-middleware-pattern-in-expressjs) pattern for handling the requests. The middleware pattern can become a problem in mid to large sized apps for the following reasons:

- Hard to tell which middlewares will be executed for a given request
- Order of middleware registration can break the application
- Hard to tell where a property or method is being set or modified on the request object.

This [gist](https://gist.github.com/nfroidure/3c0a5858518663be186a1a64d160ee31) exemplifies these issues concisely.

Hapi has a completely different approach to this. It has a well defined [request lifecycle](https://hapi.dev/api/?v=19.1.1#request-lifecycle), with pre-defined steps (that are configurable at the server or route level) that are always run in the same order. And if you need to add some functionality exactly at some stage of the lifecycle, there are explicit extension points that allow intercepting and handling the request exactly where needed. This makes the request flow easier to understand and debug.

### Up to date

Since Hapi has reached 1.0 in 2013 a lot has happened in JavaScript and Node.js ecosystems. And Hapi has not lagged behind in adopting new trends and patterns, even when that meant a complete re-write of the framework.

The latest rewrite (for version 17) was made to adopt `async/await` constructs that had landed in Node.js 8.

Another recent addition to the lib that shows how it keeps up to date with current trends were the TypeScript definition files that were added to most (if not all) of the main modules, including of course the main Hapi module.

### A word on performance benchmarks

Performance benchmarks should always be taken with a grain of salt. They can be useful to understand how frameworks compare in specific situations, but it is usually wrong to just take their results and expand to every situation.

Some of the benchmarks that put Hapi below other frameworks are simplistic tests, rendering static data or a `Hello World` string. This kind of benchmark only tells us the overhead of the framework for that specific path. That is hardly a fair representation of an actual application. If we add database queries or network requests to the mix it is very likely that the framework overhead will become negligible.

If your application is very sensitive to performance and the framework overhead is important, you should benchmark the frameworks yourself, using flows that are expected to be the critical paths in your app.

## Intro to Hapi.js

In this series we will build an application from scratch, exploring several concepts related to Hapi and web development in detail.

To get us started, I want to rewrite the Getting Started example from Hapi docs using TypeScript. We will build the foundation from which to expand in the upcoming articles.

Let's create a new directory for our project and run npm init:

```shell
$ mkdir hapi-guide
$ cd hapi-guide
$ npm init -y
```

We can now install TypeScript and init the configuration.

```shell
$ npm i -D typescript
```

Let's add a `tsc` script in the `package.json` scripts section:

```json
{
  // ...
  "scripts": {
    "tsc": "tsc"
  }
}
```

This allows us to run the TypeScript compiler as a npm script. Let's generate the `tsconfig.json` file

```shell
$ npm run tsc -- --init
```

The current Hapi version (v19) only supports Node.js >= 12. Node 12 [supports](https://node.green/#ES2018) ES2018 fully. We are going to change the `tsconfig.json` to take advantage of that. Change the following values (uncommenting where appropriate) in the `tsconfig.json` file, leaving the rest untouched:

```json
{
  "target": "es2018",
  "lib": ["es5", "es6"],
  "outDir": "./build"
  // other fields as generated by tsc
}
```

We can now move on to reproducing the hello world example from Hapi [docs](https://hapi.dev/tutorials/?lang=en_US#-adding-routes) with TypeScript. Let's install `@hapi/hapi` and `@types/hapi__hapi`.

```shell
$ npm i -S @hapi/hapi
$ npm i -D @types/hapi__hapi
```

And the write a `server.ts` file with the following content:

```typescript
import Hapi from "@hapi/hapi"

async function init() {
  const server = Hapi.server({
    port: 3000,
    host: "localhost",
  })

  server.route({
    method: "GET",
    path: "/",
    handler: (request, h) => {
      return "Hello World!"
    },
  })

  await server.start()
  console.log(`Server running on ${server.info.uri}`)
}

process.on("unhandledRejection", err => {
  console.error(err)
  process.exit(1)
})

init()
```

Now if we run `npm run tsc` the code will be complied and put under the `./build` folder. And we can start it with `node ./build/server.js`.

On development, we don't want to have to compile and start the server like that every time we change something on our app. Let's fix that.

To get rid of the compilation step, we can use [ts-node](https://github.com/TypeStrong/ts-node) that will compile and run the TypeScript files for us. And to avoid having to restart the server every time we change something, we will use [nodemon](https://nodemon.io/). We will also add `cross-env` so it is easy to pass ENV VARS to our scripts, regardless of the OS we are using.

```shell
$ npm i -D ts-node nodemon cross-env
```

Let's add a `start` script to our `package.json` so it is easy to start our development server.

```json
{
  "scripts": {
    "tsc": "tsc",
    "start": "cross-env NODE_ENV=development nodemon --watch . --exec 'ts-node' server.ts"
  }
}
```

Don't get carried away by the details of the start script, we are only telling `nodemon` to watch the current directory and run the `server.ts` script with `ts-node` whenever something changes. Let's take it for a spin:

```shell
$ npm start
```

Now if you visit `http://localhost:3000` on your browser you should see the string `Hello World!` from our controller. Any changes to our `server.ts` should restart the server.

## Wrapping up
