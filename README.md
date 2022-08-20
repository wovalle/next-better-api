# next-better-api

Opinionated helpers for building NextJS APIs, powered by [Zod](https://github.com/colinhacks/zod).

```ts
import { z } from 'zod';
import { endpoint, asHandler } from 'next-better-api';

const getUsers = endpoint(
  {
    method: 'get',
    responseSchema: z.object({
      users: z.array(
        z.object({
          id: z.string(),
          name: z.string(),
          email: z.string(),
          active: z.boolean(),
        })
      ),
    }),
  },
  async () => {
    const users = await getAllUsers();

    return {
      status: 200,
      body: {
        users,
      },
    };
  }
);

export asHandler([getUsers]);
```

## Installation:

`next-better-api` requires `zod` for schema validation. You can install both libraries with yarn or npm on your existing NextJS project:

```shell
$ yarn add zod next-better-api

// OR

$ npm i -S zod next-better-api
```

And import it from your API definitions:

```ts
import { endpoint, asHandler } from 'next-better-api';
// import { z } from 'zod'; // If you are defining schemas for your endpoints
```

Remember you always need to use `asHandler` to convert your `next-better-api` endpoints to NextJS-compatible handlers:

```ts
export default asHandler([getUsers, createUser, updateUser]);
```

## Features:

### Method handler support

```ts
const getUsers = endpoint({
  method: 'get',
  // ...
});

const deleteUser = endpoint({
  method: 'delete',
  // ...
});
```

### Schema validation & automatic type inference support

See [Zod](https://github.com/colinhacks/zod) for all available schema validation options.

```ts
const createUser = endpoint(
  {
    method: 'post',
    bodySchema: z.object({
      // Name must be at least 3 characters long
      name: z.string().min(3),
    }),

    responseSchema: z.object({
      userId: z.string(),
    }),
  },

  async ({ req }) => {
    // Type for `name` is automatically inferred based on `bodySchema`
    const { name } = req.body;

    const newUser = await createUser(req.body);

    return {
      status: 201,

      // Response body is also annotated based on `responseSchema`
      body: {
        userId: newUser.id,
      },
    };
  }
);
```

### Endpoint type inference helpers

Different utility types are available for handling endpoint type information.

```ts
import type { InferEndpointType } from 'next-better-api';
import type { getUsers } from '../api/users.ts';

type GetUsersResponseBody = InferEndpointType<typeof getUsers>['Response'];

// Can be combined with API calls, react-query, etc to provide end-to-end type annotation from the endpoint definition:

const response = await fetch('/api/users' /* ... */);

const newUser = (await response.json()) as GetUsersResponseBody;

newUser.userId; // #=> string
```

Additional helpers are available:

```ts
import type {
  InferEndpointType,
  InferQueryType,
  InferResponseBodyType,
  InferRequestBodyType,
} from 'next-better-api';
```

### Stuff

See license information under [`LICENSE.md`](/LICENSE.md).

Contributions are super welcome - in the form of bug reports, suggestions, or better yet, pull requests!
