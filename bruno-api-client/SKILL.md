---
name: bruno-api-client
description: >
  Generates Bruno collection files (.bru) from API route definitions in Express, Next.js, Fastify,
  or any other framework. Creates organized collections with environments, authentication, and folder
  structure for the open-source Bruno API client. Use when users request "generate bruno collection",
  "bruno api testing", "create bru files", "bruno import", or when they want to test their API with
  Bruno, set up API client files, or migrate from Postman/Insomnia to Bruno.
license: MIT
metadata:
  author: https://github.com/dotuan9x
  version: "1.0.0"
  triggers: bruno, .bru, bruno collection, api testing, generate collection, postman alternative
---

# Bruno Collection Generator

Generate Bruno collection files for the open-source, Git-friendly API client.

## Reference Files

| Reference | Load When |
|-----------|-----------|
| `references/generator.md` | User wants a TypeScript/Node.js script to generate the collection programmatically |
| `references/examples.md` | Need complete `.bru` file examples, pre/post scripts, or test assertions |

## Core Workflow

1. **Scan routes** — find all API route definitions in the codebase
2. **Extract metadata** — methods, paths, params, request bodies, auth requirements
3. **Create collection** — initialize `bruno.json` manifest
4. **Generate `.bru` files** — one file per endpoint
5. **Organize folders** — group by resource (users/, auth/, products/, etc.)
6. **Add environments** — Dev, Staging, Production with `vars` and `vars:secret`

## Collection Structure

```
collection/
├── bruno.json
├── environments/
│   ├── Development.bru
│   ├── Staging.bru
│   └── Production.bru
├── users/
│   ├── folder.bru
│   ├── get-users.bru
│   ├── create-user.bru
│   └── ...
└── auth/
    ├── folder.bru
    └── login.bru
```

## bruno.json Manifest

```json
{
  "version": "1",
  "name": "My API",
  "type": "collection",
  "ignore": ["node_modules", ".git"]
}
```

## .bru File Syntax

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
}
```

## Best Practices

1. **Git-friendly** — Bruno stores everything as plain text files; commit the collection alongside code
2. **Use environments** — store base URLs and tokens in environment files, never hardcode them
3. **Secret variables** — mark sensitive vars with `vars:secret`
4. **Add docs** — document each request with a `docs` block
5. **Pre/post scripts** — automate token extraction and setup (see `references/examples.md`)
6. **Add tests** — include assertions in `tests` blocks (see `references/examples.md`)
7. **Sequence numbers** — order requests logically with `seq`

## Output Checklist

- [ ] `bruno.json` manifest created
- [ ] Environment files for dev/staging/prod
- [ ] Folder structure by resource with `folder.bru` in each folder
- [ ] Request `.bru` files with correct syntax
- [ ] Path parameters use `{{param}}` syntax
- [ ] Query parameters in `query` block
- [ ] Request bodies in `body:json` block
- [ ] Authentication configured
- [ ] `docs` block on each request
- [ ] Pre/post scripts for token handling (if needed)

