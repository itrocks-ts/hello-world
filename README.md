# hello-world

A minimal Hello World application built with the [it.rocks](https://it.rocks) framework.

This repository is a step-by-step tutorial to create your first `it.rocks` application,  
understand what each file is for, and run it locally in your browser.

## Prerequisites

- Git: ```apt install git```
- Node.js 24+: download from [nodejs.org](https://nodejs.org/en/download)
- npm 11.6+: bundled with Node.js

## Quick install and run

```bash
# download:
git clone https://github.com/itrocks-ts/hello-world
cd hello-world
# install dependencies:
npm install
# compile TypeScript to runnable JavaScript:
npm run build
# start the app:
npm start
```

When starting, the server prints its listening address.
Open `http://localhost:3000` to see “Hello World!”.

## Project layout

```
.
├─ app/
│  └─ main.ts         # App code: framework bootstrap + your first user action
├─ config.yaml        # Access rules and routing
├─ node_modules/      # Installed dependencies (generated)
├─ package.json       # Metadata, scripts, dependencies
├─ README.md          # This tutorial
└─ tsconfig.json      # TypeScript compiler configuration
```

### 1) `app/main.ts` — your first user action

```ts
import '@itrocks/framework'

export function helloWorld()
{
	return 'Hello World!'
}
```

- `import '@itrocks/framework'` bootstraps the framework when the module loads.  
  It typically:
  - loads configuration files from installed packages,
  - initializes framework inter-dependencies and services,
  - prepares routing and access control.
- The exported `helloWorld` function is a user action.
  The framework receives its return value and sends it to the browser.

You may export multiple user actions in this module (or in others under `app/`) and map them through `config.yaml`.

### 2) `config.yaml` — access control and routing

```yaml
access:
  free: '*'

routes:
  /: /app/main
```

- `access` defines who can use the application.  
  Professional apps usually require authentication; here we keep it open with `free: '*'` for demonstration purposes.
- `routes` maps URLs to modules.  
  `/: /app/main` means the site root calls the default/first exported user action from `app/main`.

To explicitly target a user action:

```yaml
routes:
  /: /app/main                    # calls the default or first exported user action
  /hello: /app/main:helloWorld    # explicitly call the exported user action named helloWorld
```

The suffix `:functionName` selects a specific [Action class](https://github.com/itrocks-ts/action) or function.

### 3) `package.json` — scripts and dependencies

```json
{
  "dependencies": {
    "@itrocks/framework": "latest"
  },
  "devDependencies": {
    "typescript": "5.9"
  },
  "name": "hello-world",
  "scripts": {
    "build": "tsc",
    "start": "node app/main"
  }
}
```

- `dependencies` brings in `@itrocks/framework`.
- `devDependencies` includes `typescript` for compilation.
- Scripts:
  - `npm run build` compiles TypeScript to JavaScript.
  - `npm start` runs the compiled entry point (`app/main.js`).

### 4) `tsconfig.json` — TypeScript configuration

A minimal, recommended configuration for `it.rocks` backend modules.  
It defines the module system, output directory, and compiler options used by `npm run build`.

### 5) `node_modules/` — installed dependencies

Automatically generated after `npm install`.  
You do not need to modify it manually.

## Extend the example: add a new route

1. Add a new user action:
   ```ts
   import { Request } from '@itrocks/action-request'

   export function hello(request: Request)
   {
     const name = request.data.name ?? 'you'
     return `Hello ${name}!`
   }
   ```

2. Map it in `config.yaml`:
   ```yaml
   routes:
     /: /app/main
     /hello: /app/main:hello
   ```

3. Rebuild an restart the app:
   ```bash
   npm run build
   npm start
   ```

4. Visit:  
   `http://localhost:3000/hello?name=Baptiste`

## Troubleshooting

- **Blank page**
  - Check the terminal output for errors.
  - Ensure you ran `npm run build` before starting.

- **Port already in use**  
  Stop the conflicting service or configure another port (the framework prints the one it selects).

## Where to go next

- Create one module per feature under `app/`.
- Use separate config files per environment (dev/prod).
- Remove `access.free: '*'` and enable proper user management with  
  [`@itrocks/user`](https://github.com/itrocks-ts/user).

Happy coding with [it.rocks](https://it.rocks)!
