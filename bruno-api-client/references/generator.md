# Bruno Collection Generator Script

A TypeScript script for programmatically generating Bruno collections from API route definitions.

## TypeScript Generator

```typescript
// scripts/generate-bruno.ts
import * as fs from "fs";
import * as path from "path";

interface RouteInfo {
  method: string;
  path: string;
  name: string;
  description?: string;
  body?: object;
  queryParams?: { name: string; value: string }[];
  auth?: boolean;
}

interface BrunoOptions {
  collectionName: string;
  outputDir: string;
  baseUrl: string;
  authType?: "bearer" | "basic" | "apikey";
}

function generateBrunoCollection(
  routes: RouteInfo[],
  options: BrunoOptions
): void {
  const { outputDir, collectionName } = options;

  if (!fs.existsSync(outputDir)) {
    fs.mkdirSync(outputDir, { recursive: true });
  }

  const manifest = {
    version: "1",
    name: collectionName,
    type: "collection",
    ignore: ["node_modules", ".git"],
  };
  fs.writeFileSync(
    path.join(outputDir, "bruno.json"),
    JSON.stringify(manifest, null, 2)
  );

  generateEnvironments(outputDir, options);

  const groupedRoutes = groupRoutesByResource(routes);

  for (const [resource, resourceRoutes] of Object.entries(groupedRoutes)) {
    const folderPath = path.join(outputDir, resource);

    if (!fs.existsSync(folderPath)) {
      fs.mkdirSync(folderPath, { recursive: true });
    }

    const folderBru = `meta {\n  name: ${capitalize(resource)}\n}\n`;
    fs.writeFileSync(path.join(folderPath, "folder.bru"), folderBru);

    let seq = 1;
    for (const route of resourceRoutes) {
      const fileName = generateFileName(route);
      const content = generateBruFile(route, seq++, options);
      fs.writeFileSync(path.join(folderPath, `${fileName}.bru`), content);
    }
  }
}

function generateBruFile(
  route: RouteInfo,
  seq: number,
  options: BrunoOptions
): string {
  const lines: string[] = [];

  lines.push("meta {");
  lines.push(`  name: ${route.name}`);
  lines.push("  type: http");
  lines.push(`  seq: ${seq}`);
  lines.push("}");
  lines.push("");

  const method = route.method.toLowerCase();
  const urlPath = route.path.replace(/:(\w+)/g, "{{$1}}");

  lines.push(`${method} {`);
  lines.push(`  url: {{baseUrl}}${urlPath}`);

  if (["post", "put", "patch"].includes(method) && route.body) {
    lines.push("  body: json");
  } else {
    lines.push("  body: none");
  }

  if (route.auth && options.authType) {
    lines.push(`  auth: ${options.authType}`);
  } else {
    lines.push("  auth: none");
  }

  lines.push("}");
  lines.push("");

  if (route.auth && options.authType === "bearer") {
    lines.push("auth:bearer {");
    lines.push("  token: {{authToken}}");
    lines.push("}");
    lines.push("");
  } else if (route.auth && options.authType === "basic") {
    lines.push("auth:basic {");
    lines.push("  username: {{username}}");
    lines.push("  password: {{password}}");
    lines.push("}");
    lines.push("");
  }

  if (route.queryParams?.length) {
    lines.push("query {");
    for (const param of route.queryParams) {
      lines.push(`  ${param.name}: ${param.value}`);
    }
    lines.push("}");
    lines.push("");
  }

  lines.push("headers {");
  lines.push("  Accept: application/json");
  if (["post", "put", "patch"].includes(method)) {
    lines.push("  Content-Type: application/json");
  }
  lines.push("}");
  lines.push("");

  if (["post", "put", "patch"].includes(method) && route.body) {
    lines.push("body:json {");
    lines.push(JSON.stringify(route.body, null, 2));
    lines.push("}");
    lines.push("");
  }

  if (route.description) {
    lines.push("docs {");
    lines.push(`  ${route.description}`);
    lines.push("}");
  }

  return lines.join("\n");
}

function generateEnvironments(outputDir: string, options: BrunoOptions): void {
  const envsDir = path.join(outputDir, "environments");
  if (!fs.existsSync(envsDir)) fs.mkdirSync(envsDir, { recursive: true });

  const environments = [
    { name: "Development", baseUrl: "http://localhost:3000/api" },
    { name: "Staging", baseUrl: "https://staging-api.example.com" },
    { name: "Production", baseUrl: "https://api.example.com" },
  ];

  for (const env of environments) {
    const content = `vars {\n  baseUrl: ${env.baseUrl}\n  authToken:\n}\n\nvars:secret [\n  authToken\n]\n`;
    fs.writeFileSync(path.join(envsDir, `${env.name}.bru`), content);
  }
}

function generateFileName(route: RouteInfo): string {
  return route.name.toLowerCase().replace(/\s+/g, "-");
}

function groupRoutesByResource(routes: RouteInfo[]): Record<string, RouteInfo[]> {
  const groups: Record<string, RouteInfo[]> = {};
  for (const route of routes) {
    const parts = route.path.split("/").filter(Boolean);
    const resource = parts[0] || "api";
    if (!groups[resource]) groups[resource] = [];
    groups[resource].push(route);
  }
  return groups;
}

function capitalize(str: string): string {
  return str.charAt(0).toUpperCase() + str.slice(1);
}
```

## CLI Script

```typescript
#!/usr/bin/env node
// scripts/bruno-gen.ts
import * as fs from "fs";
import { program } from "commander";

program
  .name("bruno-gen")
  .description("Generate Bruno collection from API routes")
  .option("-f, --framework <type>", "Framework type", "express")
  .option("-s, --source <path>", "Source directory", "./src")
  .option("-o, --output <path>", "Output directory", "./bruno-collection")
  .option("-n, --name <name>", "Collection name", "My API")
  .option("-b, --base-url <url>", "Base URL", "http://localhost:3000/api")
  .option("-a, --auth <type>", "Auth type (bearer|basic|apikey)")
  .parse();

const options = program.opts();

async function main() {
  const routes = await scanRoutes(options.framework, options.source);
  generateBrunoCollection(routes, {
    collectionName: options.name,
    outputDir: options.output,
    baseUrl: options.baseUrl,
    authType: options.auth,
  });
  console.log(`Generated Bruno collection in ${options.output}`);
  console.log(`Open with: bruno run ${options.output}`);
}

main();
```
