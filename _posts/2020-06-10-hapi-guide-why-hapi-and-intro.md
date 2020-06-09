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

### Sane request lifecycle

### Up to date

### A word on performance benchmarks

## Intro to Hapi.js
