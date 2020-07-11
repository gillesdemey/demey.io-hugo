---
title: Getting started with React & TypeScript
author: Gilles De Mey
date: "2019-05-01T12:59:26.162Z"
teaser: "A minimalist guide to quickly get your journey started with React and TypeScript 🏃‍♂️"
---

I'm writing this down for my future self, but maybe it helps you navigate the
JavaScript tooling jungle.

I've currently not found a faster way to get started with React and TypeScript,
but if you have any suggestions hit me up on [Twitter](https://twitter.com/gdemey)!

The goal here is to quickly get started with setting up a React + TypeScript combo without copy and pasting a ton of code and setting up complicated tools like [webpack](https://webpack.js.org/) — so here is my minimalist guide to get up and running.

# Dependencies

Let's install a few necessary dependencies first:

`yarn add --dev parcel-bundler react react-dom`

# Directory layout

Using the following directory layout is the best way to get your tools working properly since we can rely on *convention over configuration*.

```bash
$ tree -L 2
.
├── package.json
├── public
│   └── index.html
├── src
│   ├── App.tsx
│   └── index.tsx
└── tsconfig.json
```

# Configuration files

We're all familiar with the `package.json` file, we're adding the following script to it to instruct the [Parcel bundler](https://parceljs.org/) on how to bundle our application. It will automatically install any missing dependencies too!

```json
{
  "scripts": {
    "dev": "parcel public/index.html"
  }
}
```

Next, let's take a look at the `tsconfig.json` file contents (I've omitted a few common compilerOptions for brevity):

```json
{
  "compilerOptions": {
    "jsx": "react"
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules"
  ]
}

```

We're instructing TypeScript to only compile files in the `src/` directory and to ignore our dependencies. We're also instructing the compiler to transform the `jsx` syntax to `React.createElement` functions.[^ts-jsx]

# Source files

`public/index.html`

We can point to our index file from here, make sure to include the `script` *after* your root DOM node.

```html
<html>
  <head></head>
  <body>
    <div id="root"></div>
    <script src='../src/index.tsx'></script>
  </body>
</html>
```

`src/index.tsx`

This file simply bootstraps our React application, instructing `react-dom` which element to use to create our React application.

```typescript
import React from 'react'
import ReactDOM from 'react-dom'

import App from './App'

const container = document.querySelector('#root')
ReactDOM.render(<App />, container)
```

`src/App.tsx`

Not much going on here, we're just creating a functional component that renders some JSX.

```typescript
import React from 'react'

export default function App (props) {
  return (
    <div>Hello, world!</div>
  )
}
```

And we're done! If you're already familiar with React and TypeScript you can take it from here.

Simply run `yarn dev` in your terminal and start coding, Parcel will take care of most of the heavy lifting like setting up React HMR, watching for file changes, compiling your source code to bundled files and generating source maps.

# Closing thoughts

Check out the documentation for the amazing [Parcel Bundler](https://parceljs.org/getting_started.html) which takes away most of the pain of setting up any modern front-end application.

[^ts-jsx]: https://www.typescriptlang.org/docs/handbook/jsx.html
