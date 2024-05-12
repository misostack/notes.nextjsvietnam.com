# Casbin

## Context

- In a system, you have to build an authorization policy based on users, roles and resources. Most of time, you created your own model and check permissions depends on resources/features or even rest api endpoints.
- There is a quick and reliable solution named "casbin". It is tool that support a lot of programming languages. Also, it is easy to understand and apply to your project.
- 2 pillars of casbin: **model**(rules for validate user's permissions) and **policy**(user's permissions on resources, user's roles,... - data)

It sounds cool right, but why do we need casbin? Let's dive in real world examples to perceive its benefits

## Real world examples

### Authorization with users, roles and permissions

![image](https://user-images.githubusercontent.com/31009750/219676885-1fad3ad1-3cf8-41f0-83dd-069105b911c0.png)

With this model, if using traditional ways, when a user logged in, we can create a JWT token which includes their userId.

Each request that required authentication, their token will be extracted to retrieve these informations: roleId, permissions(which come from role) and attach them into the current request

Then in the authorization process, the request that has the authorization information will be checked with the current request object, in this case it it the specific endpoint. We must define the required permissions for each endpoints for group of end endpoints.

If the request has the required permissions to access the resource, than, we can return the approriate resource.

Several cases, you may have to face with:

- Case 1: Some endpoints require the composition of several permissions
- Case 2: Some endpoints require at least of one of the specific permissions
- Case 3: Some endpoints require only one permission (an edge of case 2)
- Case 4: Some endpoints require permission or owner's permission(Eg: an article can be edit by the author or admin user)
- Case 5: Some endpoints applied restrictions/exceptions for specific role

And many ...

Therefore in every cases, we have to write the logic code to adapt the business rules.

In this situation, **casbin** has the built-in logic code inside to solve our business rules.
We only need to define the model(business rule - followed casbin format), policy(the data includes: authorization informations of request, the permissions). **casbin** provide us the library to load the model and policy, and we can use it to check the request is allowed or denied.
It supports almost popular programming languages. So that you can bring your model to anywhere that matched without dependency on a specific language.

Let's dive in more details

```js
const roles = [
  { roleId: 1, roleName: "sysadmin" },
  { roleId: 2, roleName: "admin" },
  { roleId: 3, roleName: "manager" },
  { roleId: 4, roleName: "staff" },
];
const users = [
  { userId: 1, username: "Super Admin", roleIds: [1, 2] },
  { userId: 2, username: "System Admin", roleIds: [1] },
  { userId: 3, username: "Manager 1", roleIds: [3, 4] },
  { userId: 4, username: "Manager 2", roleIds: [3, 4] },
  { userId: 5, username: "Staff 1", roleIds: [4] },
  { userId: 6, username: "Staff 2", roleIds: [4] },
];
```

### 1.Request definition : parameters of the request that we will use it to check with policy

- Request: PermissionKey, RoleId

```c
[request_definition]
r = permissionKey, roleId
```

### 2.Policy definition : policy in this case is permission(which is combination of users,roles,actions,resources)

Based on what we have, our policy should be:

- Permission: Key, RoleId

```cs
p, USERS_RETRIEVE, 1
p, USERS_CREATE, 1
p, USERS_UPDATE, 1
p, USERS_DELETE, 1
p, USERS_RETRIEVE, 2
p, STAFF_RETRIEVE, 3
p, PROJECTS_RETRIEVE, 3
p, PROJECTS_RETRIEVE, 4
p, PROJECTS_UPDATE, 3
```

```c
[policy_definition]
p = permissionKey, roleId
```

### 3.Policy effect : to define whether the request should be accepted if multiple policy rules matchs the request

- Policy effect: at least one rule is matched

```c
[policy_effect]
e = some(where (p.eft == allow))
```

### 4.Matchers : to define how policy rules are evaluated against the request

```c
[matchers]
m = r.permissionKey == p.permissionKey && r.roleId == p.roleId
```

### Sample

> Sample Code, you can visit [here](https://github.com/misostack/typescript-examples) for full example

```ts
// import http from 'http';

// const hostname = '127.0.0.1';
// const port = 3000;

// const server = http.createServer((req, res) => {
//   res.statusCode = 200;
//   res.setHeader('Content-Type', 'text/plain');
//   res.end('Hello World 123');
// });

// server.listen(port, hostname, () => {
//   // eslint-disable-next-line no-console
//   console.log(`Server running at http://${hostname}:${port}/`);
// });

import express from "express";
import { newEnforcer } from "casbin";
import path from "path";
import { AuthorizationModel, CustomAdapter } from "./casbin/custom-adapter";

const PORT = process.env.PORT ?? 1337;

const app = express();

app.use((req, res, next) => {
  Reflect.set(req, "user", {
    id: req.query["userId"] || "",
    roleId: req.query["roleId"] || "",
  });
  next();
});
app.get("/", async (req, res) => {
  res.send("Index");
});

app.get("/members", async (req, res) => {
  // custom Adapter
  const enforcer = await newEnforcer(
    path.join(__dirname, "casbin/basic_model.conf"),
    // path.join(__dirname, 'casbin/basic_policy.csv'),
    new CustomAdapter(AuthorizationModel.BASIC)
  );
  const user: { id: string } = Reflect.get(req, "user");
  const userId = user.id;
  const resource = "users";
  const action = "read";
  if (await enforcer.enforce(userId, resource, action)) {
    res.send("User Data");
  } else {
    res.status(403).send("Unauthorized");
  }
});

app.get("/users", async (req, res) => {
  // custom Adapter
  const enforcer = await newEnforcer(
    path.join(__dirname, "casbin/rbac_model.conf"),
    // path.join(__dirname, 'casbin/rbac_policy.csv'),
    new CustomAdapter(AuthorizationModel.RBAC)
  );
  const user: { id: string; roleId: string } = Reflect.get(req, "user");
  const roleId = user.roleId;
  const permissionKey = "USERS_RETRIEVE";
  if (await enforcer.enforce(permissionKey, roleId)) {
    res.send("User Data");
  } else {
    res.status(403).send("Unauthorized");
  }
});

app.listen(PORT, () => {
  const data = `
  p, USERS_RETRIEVE, 1
  p, USERS_CREATE, 1
  p, USERS_UPDATE, 1
  p, USERS_DELETE, 1
  p, USERS_RETRIEVE, 2
  p, STAFF_RETRIEVE, 3
  p, PROJECTS_RETRIEVE, 3
  p, PROJECTS_RETRIEVE, 4
  p, PROJECTS_UPDATE, 3
  `;
  console.log(
    data
      .split("\n")
      .map((l) => l.trim())
      .filter((l) => l.length > 0)
  );
  console.log(`App listening on http://localhost:${PORT}/`);
});

// casbin/custom-adapter
import { Model, Adapter, Helper } from "casbin";
// Define a custom adapter that can load the model and policy from your database.
export enum AuthorizationModel {
  BASIC,
  RBAC,
}
export class CustomAdapter implements Adapter {
  private authorizationModel: AuthorizationModel;
  constructor(authorizationModel: AuthorizationModel) {
    // Initialize a connection to your database.
    // ...
    this.authorizationModel = authorizationModel;
  }

  async loadPolicy(model: Model) {
    if (this.authorizationModel === AuthorizationModel.BASIC) {
      const p1 = ["p", "1", "users", "read"].join(",");
      const p2 = ["p", "2", "users", "read"].join(",");
      Helper.loadPolicyLine(p1, model);
      Helper.loadPolicyLine(p2, model);
    } else {
      const data = `
      p, USERS_RETRIEVE, 1
      p, USERS_CREATE, 1
      p, USERS_UPDATE, 1
      p, USERS_DELETE, 1
      p, USERS_RETRIEVE, 2
      p, STAFF_RETRIEVE, 3
      p, PROJECTS_RETRIEVE, 3
      p, PROJECTS_RETRIEVE, 4
      p, PROJECTS_UPDATE, 3
      `;
      data
        .split("\n")
        .map((l) => l.trim())
        .filter((l) => l.length > 0)
        .forEach((l) => {
          Helper.loadPolicyLine(l, model);
        });
    }
  }

  async savePolicy(model) {
    // Save the policies to your database.
    // const policies = model.getPolicy();
  }

  async addPolicy(sec, ptype, rule) {
    // Add a policy to your database.
    // await this.connection.query(
    //   'INSERT INTO policies VALUES (?, ?, ...)',
    //   rule,
    // );
  }

  async removePolicy(sec, ptype, rule) {
    // Remove a policy from your database.
    // await this.connection.query('DELETE FROM policies WHERE ...');
  }

  async removeFilteredPolicy(sec, ptype, fieldIndex, ...fieldValues) {
    // Remove policies from your database that match the specified criteria.
    // await this.connection.query('DELETE FROM policies WHERE ...');
  }
}
```
