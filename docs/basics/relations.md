---
sidebar_position: 3
---

# Relations

A relation is defined by the `@Relation()` decorator.

## Writing a simple relation

### The hard way

```ts
// src/models/Profile.ts
import { Model, Property, Relation } from "decotix";

export class Profile {
  // ...

  @Property()
  @Relation({ fields: ["userId"], references: ["id"] })
  user?: User;

  @Property()
  userId?: string;
}
```

```ts
// src/models/User.ts
import { Model, Property } from "decotix";
import { Profile } from "./Profile";

@Model()
export class User {
  // ...

  @Property(() => Profile)
  profile: Profile;
}
```

Output:

```prisma
model User {
  // ...
  profile Profile
}

model Profile {
  // ...
  user   User   @relation(fields: [userId], references: [id])
  userId String
}
```

### The easy way

Because this kind of relation is redundant to type out every time.

When you pass no arguments to the `@Relation()` decorator, it automatically makes the relation for you:

```ts
// src/models/Profile.ts

export class Profile {
  // ...

  // automates the relation, `userId` field is also automated so we can remove that from our code.
  @Relation()
  @Property(() => User)
  user: User;
}
```

**_`src/models/User.ts` remains the same_**  
**_Output remains the same_**

:::danger
Automated relations do not work if the target model (in this case `User`) has an ID field that is named something other than `id`. It also doesn't work with models if they only have unique fields, coumpund uniques or composite IDs. If that is the case for you, use this shortcut instead:

```ts
@Relation(["id1", "id2"])
@Property(() => User)
user?: User;
```

The array passed is the `references: []` part of the relation. The rest (including the `userId1` and the `userId2`) will be autofilled.

```prisma
user    User   @relation(fields: [userId1, userId2], references: [id1, id2])
userId1 String
userId2 String
```

:::