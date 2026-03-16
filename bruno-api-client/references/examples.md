# Bruno Collection — Complete Examples

## environments/Development.bru

```bru
vars {
  baseUrl: http://localhost:3000/api
  authToken:
  userId: 1
}

vars:secret [
  authToken
]
```

## environments/Production.bru

```bru
vars {
  baseUrl: https://api.example.com
  authToken:
  userId:
}

vars:secret [
  authToken
]
```

## users/folder.bru

```bru
meta {
  name: Users
}
```

## users/get-users.bru

```bru
meta {
  name: Get Users
  type: http
  seq: 1
}

get {
  url: {{baseUrl}}/users
  body: none
  auth: bearer
}

auth:bearer {
  token: {{authToken}}
}

query {
  page: 1
  limit: 10
}

headers {
  Accept: application/json
}

docs {
  Retrieve a paginated list of users.

  ## Query Parameters
  - page: Page number (default: 1)
  - limit: Items per page (default: 10, max: 100)
}
```

## users/get-user.bru

```bru
meta {
  name: Get User by ID
  type: http
  seq: 2
}

get {
  url: {{baseUrl}}/users/{{userId}}
  body: none
  auth: bearer
}

auth:bearer {
  token: {{authToken}}
}

headers {
  Accept: application/json
}

docs {
  Retrieve a single user by their ID.
}
```

## users/create-user.bru

```bru
meta {
  name: Create User
  type: http
  seq: 3
}

post {
  url: {{baseUrl}}/users
  body: json
  auth: bearer
}

auth:bearer {
  token: {{authToken}}
}

headers {
  Accept: application/json
  Content-Type: application/json
}

body:json {
  {
    "name": "John Doe",
    "email": "john@example.com",
    "role": "user"
  }
}

docs {
  Create a new user account.
}
```

## users/update-user.bru

```bru
meta {
  name: Update User
  type: http
  seq: 4
}

put {
  url: {{baseUrl}}/users/{{userId}}
  body: json
  auth: bearer
}

auth:bearer {
  token: {{authToken}}
}

headers {
  Accept: application/json
  Content-Type: application/json
}

body:json {
  {
    "name": "John Updated",
    "email": "john.updated@example.com"
  }
}
```

## users/delete-user.bru

```bru
meta {
  name: Delete User
  type: http
  seq: 5
}

delete {
  url: {{baseUrl}}/users/{{userId}}
  body: none
  auth: bearer
}

auth:bearer {
  token: {{authToken}}
}

headers {
  Accept: application/json
}
```

## auth/login.bru

```bru
meta {
  name: Login
  type: http
  seq: 1
}

post {
  url: {{baseUrl}}/auth/login
  body: json
  auth: none
}

headers {
  Accept: application/json
  Content-Type: application/json
}

body:json {
  {
    "email": "user@example.com",
    "password": "password123"
  }
}

script:post-response {
  if (res.body.token) {
    bru.setEnvVar("authToken", res.body.token);
  }
}

docs {
  Authenticate user and receive access token.
  On successful login, the token is automatically saved to the authToken environment variable.
}
```

## Pre/Post Request Scripts

```bru
script:pre-request {
  const timestamp = Date.now();
  bru.setVar("requestId", `req-${timestamp}`);
}

script:post-response {
  if (res.body.token) {
    bru.setEnvVar("authToken", res.body.token);
  }
  if (res.body.id) {
    bru.setEnvVar("userId", res.body.id);
  }
  console.log(`Status: ${res.status}`);
  console.log(`Response time: ${res.responseTime}ms`);
}
```

## Tests in Bruno

```bru
tests {
  test("should return 200", function() {
    expect(res.status).to.equal(200);
  });

  test("should return array of users", function() {
    expect(res.body.data).to.be.an("array");
  });

  test("should include pagination", function() {
    expect(res.body.meta).to.have.property("page");
    expect(res.body.meta).to.have.property("total");
  });
}
```
