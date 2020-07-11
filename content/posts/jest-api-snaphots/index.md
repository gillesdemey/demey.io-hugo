---
title: Bulletproof REST APIs ,  with Jest Snapshots
author: Gilles De Mey
date: "2018-02-19T23:00:00.000Z"
teaser: "How to automatically test your REST API, so you can avoid nasty surprises by accidentally breaking your interface 😱."
---

Accidentally breaking your REST API is the worst possible outcome when refactoring your application.

There are already many techniques that document how to test you REST interfaces, and I’d like to present my own using [Jest Snapshots](https://facebook.github.io/jest/docs/en/snapshot-testing.html).

I’m not going to go into detail on how snapshots work (you can read about that [here](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html)) — but instead walk you through the setup process and briefly talk about the potential benefits.

## Setup

Add our [jest-snapshots-json-rest-api](https://github.com/gillesdemey/jest-snapshots-json-rest-api) serializer and register it in Jest.

`yarn add --dev jest-snapshots-json-rest-api`

Let's enable our snapshot serialiser.

```json
{
  "name": "my-awesome-app",
  "jest": {
    "snapshotSerializers": [
      "jest-snapshots-json-rest-api"
    ]
  }
}
```

And create a snapshot assertion in your test.

```javascript
const app = require('./app')
const request = require('supertest')

test('get a user', async () => {
  const response = await request(app)
    .get('/users/1')

  expect(response.status).toBe(200)
  expect(response).toMatchSnapshot()
})
```

This will generate for us the following snapshot file:

```javascript
exports[`get a user 1`] = `{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "properties": {
    "created_at": {
      "type": "string"
    },
    "email": {
      "type": "string"
    },
    "first_name": {
      "type": "null"
    },
    "full_name": {
      "type": "string"
    },
    "id": {
      "type": "string"
    },
    "last_name": {
      "type": "null"
    },
    "status": {
      "type": "string"
    },
    "updated_at": {
      "type": "string"
    }
  },
  "type": "object"
}
`;
```

The API response is transformed to a very simple JSON schema representation of the object.

This will ensure that it will work with tests that use timestamps or ISO dates in the response body or tests that are simply not idempotent.

Now each time the test suite is executed, it will compare the test output format with the snapshot it created and throw an error when the JSON schema differs from the snapshot.

If this was your intention, all you have to do is update the snapshot using the correct Jest command — if not fix the tests until Jest no longer complains about it.

![In this example, the “fullname” property has mysteriously disappeared.](https://cdn-images-1.medium.com/max/2000/1*Kf_2M0vIsX755NWjlgY0XQ.png)
> In this example, the `fullname` property has mysteriously disappeared.

## Benefits

This technique can be part of your CI workflow (and it should!) since it’s ran every time you run your test suite. No end-to-end testing with headless browsers or expensive SaaS subscriptions required.

Not every API test is idempotent, so instead the schema is compared and not the data.

* A nice visual diff helping you understand what needs fixing.
* Near effortless setup, and hardly no test code required.
* No more broken API interfaces.

Thanks Jest! 🃏
