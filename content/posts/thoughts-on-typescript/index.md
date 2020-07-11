---
title: Thoughts on TypeScript
author: Gilles De Mey
date: "2019-01-07T00:00:00.000Z"
teaser: "Some of my thoughts on TypeScript, type safety and the promise of a better world. 🌍"
---

I've recently been getting my hands dirty with writing some TypeScript, insofar I've had both positive and negative experiences.

At [waylay.io](https://www.waylay.io/) we wanted to evaluate TypeScript, so when the opportunity arose to write a new service we decided to write it in TypeScript to get our feet wet and see what advantages it might offer on larger projects.

This service we wrote is:

* Not a browser application (Node)
* Quite small
* Team of 2
* Important, deals with authorization and access control

Let's talk about some of the good, the bad and the ugly parts, in reverse! 🌀

## The Ugly

### Transpilation

Node eventually runs code that is transpiled from TypeScript to Javascript. The `tsc` compiler supports outputting source maps, but support for source maps in Node is not built-in and the performance impact is unknown.

This complicates handling post-mortems and investigating crashes since the stack trace from the machine won't be automatically mapped to your source code.

### IO Boundaries

TypeScript does not help with handling and validating types at [runtime boundaries](https://lorefnon.tech/2018/03/25/typescript-and-validations-at-runtime-boundaries/).

Most of the existing solutions are complicated and feel very foreign to our JavaScript developers.

We've settled on using an additional tool[^typescript-json-schema] to convert our TypeScript definitions to JSON schemas and use [ajv](https://github.com/epoberezkin/ajv) to validate incoming HTTP and RPC requests.

Admittedly this is a non-goal for the TypeScript project, but I'd rather not define my type definitions more than once, sadly we still need to invoke *an additional tool* to keep our JSON schemas in sync with our type definitions.

## The Bad

Getting started with TypeScript was not trivial. There's a good [boilerplate project](https://github.com/Microsoft/TypeScript-Node-Starter) but lacks *a lot* of additional comments and explanations.

> What do these values in `tsconfig.json` mean?
> How do I debug this application?
> What about watching for file changes?

I'm not criticising the starter project per se, some of these critiques can be fixed — but getting started with TypeScript required more effort and googling than we initially thought.

### Tooling

TypeScript requires different and additional tools.

We use [standard](https://standardjs.com/) for linting our source code, but unfortunately it's not compatible with TypeScript.

The [ts-lint](https://palantir.github.io/tslint/) project seems to be the obvious choice; but if we want to keep our `standard` rules we need to install another 7 packages or so[^ts-lint-standard] (there's a trend here).

Using our favourite testing framework (Jest) requires yet another tool, [`ts-jest`](https://github.com/kulshekhar/ts-jest) and even more configuration and tweaking[^ts-jest-config].

As you've probably noticed, there's almost an entirely different ecosystem that has to be explored.

Using your existing tools and libraries will require some digging around and *a ton* of additional setup, configuration and plugins.

## The Good

Refactoring the application was easy and simple, the compiler helped us out when we were moving stuff around or splitting things into multiple layers.

TypeScript somewhat twisted our arm and forced us to think about our data types and how they should flow through the application — a somewhat unpleasant experience, but we feel like it contributed to a better design.

## Additional thoughts

### Functional vs Object Oriented

To some folks TypeScript seems to be mostly analogous to Object Oriented programming. It's not.

Simply because you can now use Interfaces and Generics does not mean that you should. We write mostly functional code, unless we have a good reason to break that rule.

We went in not caring too much about the type safety of TypeScript.
Our services are built to fail fast, properly handle failure of other services and are replicated a number times across our Kubernetes cluster.

We've never really had prior issues that could be attributed to JavaScript's lack of type safety so this isn't something we were looking to fix either. 🙂

## Closing thoughts

We like it, and we might build more service in TypeScript.
Try it out for your self and see if it works for your team.

[^typescript-json-schema]: https://github.com/YousefED/typescript-json-schema
[^ts-lint-standard]: https://github.com/mightyiam/eslint-config-standard-with-typescript
[^ts-jest-config]: https://kulshekhar.github.io/ts-jest/user/install
