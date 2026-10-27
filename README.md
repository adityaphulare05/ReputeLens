from pathlib import Path

readme = r"""# ReputeLens

**AI-powered reputation intelligence for exploring sentiment, narratives, and potential crises.**

ReputeLens is a web application organized around reputation-analysis views, including **Sentiment**, **Narratives**, **Network**, and **Crisis**. Its repository contains a React-based frontend, an Express API server, shared TypeScript packages, and configuration for deploying the application as a single web service on Render.

> **Project status:** This README documents the repository structure and deployment configuration. Exact analysis methods, supported data sources, and feature behavior should be confirmed against the implementation before being described as production capabilities.

## Contents

- [Overview](#overview)
- [Application areas](#application-areas)
- [Technology stack](#technology-stack)
- [Architecture](#architecture)
- [Repository structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Getting started](#getting-started)
- [Environment variables](#environment-variables)
- [Development commands](#development-commands)
- [Build](#build)
- [Deployment on Render](#deployment-on-render)
- [Alternative frontend deployment](#alternative-frontend-deployment)
- [Security notes](#security-notes)
- [Troubleshooting](#troubleshooting)

## Overview

ReputeLens brings together a frontend dashboard and an API server in a pnpm monorepo. The interface includes routes/views for sentiment, narratives, network, and crisis. The API server is built with Express, and the repository includes integrations for language-model providers, shared API validation/client packages, and database-related code.

The checked-in configuration supports building the frontend and backend separately and deploying them together through the included `render.yaml` blueprint.

## Application areas

The repository's deployment configuration references these dashboard areas:

- **Sentiment** — a dedicated view for sentiment-related analysis.
- **Narratives** — a dedicated view for exploring narratives.
- **Network** — a dedicated network view.
- **Crisis** — a dedicated crisis view.

These names reflect routes called out in the repository configuration. Refer to the source code and in-app documentation for the precise behavior, inputs, outputs, and analytical methods of each view.

## Technology stack

| Area | Technologies evidenced in the repository |
|---|---|
| Frontend | React 19, TypeScript, Vite |
| UI and visualization dependencies | Tailwind CSS, Radix UI components, Recharts, Framer Motion, Lucide React |
| API server | Node.js, Express 5, TypeScript |
| API validation and client | Zod, shared API client and schema packages, Orval-based API code generation |
| Data layer | Drizzle ORM, PostgreSQL client (`pg`), PGlite |
| Language-model integrations | Groq SDK and OpenAI SDK |
| Workspace and package management | pnpm workspaces |
| Deployment configuration | Render Blueprint (`render.yaml`); Netlify configuration is also included |

The presence of a dependency does not necessarily mean every feature or provider is enabled in a given environment.

## Architecture

At a high level, the repository is organized as a monorepo:

1. **Frontend (`artifacts/reputelens`)** — the web application, built to `artifacts/reputelens/dist/public` according to the Netlify configuration.
2. **API server (`artifacts/api-server`)** — the Express service, built to `artifacts/api-server/dist`; the Render start command launches `dist/index.mjs`.
3. **Shared libraries (`lib/`)** — workspace packages for API client code, API schemas, and database functionality.
4. **Workspace configuration** — the root `package.json`, `pnpm-workspace.yaml`, lockfile, and TypeScript configuration coordinate package installation, type checking, and builds.

For the configured Render deployment, the frontend and API server are built in the same service. The server's health-check path is `/api/system/status`.

## Repository structure

The following is a high-level map based on the repository's visible configuration. Some folders may contain additional files and packages.

```text
ReputeLens/
├── artifacts/
│   ├── api-server/       # Express API server
│   ├── reputelens/       # Main web frontend
│   └── mockup-sandbox/   # Additional UI workspace
├── backend/              # Additional backend folder in the workspace
├── data/                 # Local data directory; do not commit private data
├── lib/
│   ├── api-client-react/ # Shared React API client
│   ├── api-spec/         # API specification/code generation
│   ├── api-zod/          # Shared validation schemas
│   └── db/               # Database package
├── scripts/              # Workspace scripts
├── .env.example          # Example environment configuration (placeholders only)
├── .gitignore
├── .npmrc
├── netlify.toml          # Optional frontend deployment configuration
├── package.json          # Root scripts and tooling
├── pnpm-lock.yaml        # Dependency lockfile
├── pnpm-workspace.yaml   # Workspace package definitions
├── render.yaml           # Render deployment blueprint
├── tsconfig.base.json
└── tsconfig.json
```

Keep the actual repository as the source of truth if your folder names differ from this overview.

## Prerequisites

- **Node.js 20** is specified in `render.yaml` for the Render deployment.
- **pnpm 10.33.0** is specified in the deployment configuration.
- Git, if you plan to clone the repository.

The root `package.json` contains a preinstall check that expects pnpm and removes `package-lock.json` and `yarn.lock` if present. Use pnpm rather than npm or Yarn to install dependencies.

### Install pnpm

If pnpm is not already available:

```bash
npm install --global pnpm@10.33.0
```

Check the installed versions:

```bash
node --version
pnpm --version
```

## Getting started

### 1. Clone the repository

Replace the URL with your own GitHub repository URL:

```bash
git clone https://github.com/YOUR_USERNAME/ReputeLens.git
cd ReputeLens
```

### 2. Install dependencies

Run this from the repository root:

```bash
pnpm install
```

For a clean install that strictly follows the committed lockfile, use:

```bash
pnpm install --frozen-lockfile
```

### 3. Configure environment variables

Review the [Environment variables](#environment-variables) section and configure the values required by the code paths you intend to run. Do not put real credentials in source control.

### 4. Start the API server

The repository's operational notes list this development command:

```bash
pnpm --filter @workspace/api-server run dev
```

The project notes identify port `5000` for the API development server. Check the server output and source configuration for the actual port and any additional startup requirements.

### 5. Start the frontend

The exact frontend development script should be confirmed in `artifacts/reputelens/package.json`. From the repository root, inspect its available scripts with:

```bash
pnpm --filter @workspace/reputelens run
```

Then run the listed development script, commonly named `dev`, if it is present:

```bash
pnpm --filter @workspace/reputelens run dev
```

If the frontend requires the API server, start both processes and configure any API URL required by the frontend.

## Environment variables

The repository's `render.yaml` declares these deployment variables:

| Variable | Purpose / configured value |
|---|---|
| `GROQ_API_KEY` | Secret API key for the configured Groq provider. Set this securely in the hosting dashboard. |
| `LLM_PROVIDER` | Configured as `groq` in the Render blueprint. |
| `GROQ_MODEL` | Configured as `qwen/qwen3.8-27b` in the Render blueprint. |
| `PORT` | Configured as `10000` in the Render blueprint. The server should respect the hosting environment's port requirements. |
| `NODE_VERSION` | Configured as `20` for Render. |

The repository's operational notes also mention `DATABASE_URL` as a PostgreSQL connection string. Whether it is required for a particular run depends on the code path and current database configuration; check the server and database package before assuming it is optional.

### Local setup

Use the supplied `.env.example` as a starting point if it matches the current application code. Copy it to `.env` and fill in the values locally:

```bash
# Windows PowerShell
Copy-Item .env.example .env
```

```bash
# macOS / Linux
cp .env.example .env
```

Example placeholders only:

```dotenv
# Add only variables actually read by the application.
GROQ_API_KEY=replace_with_your_local_key
LLM_PROVIDER=groq
GROQ_MODEL=qwen/qwen3.8-27b

# Configure only if the application/database setup requires it.
DATABASE_URL=replace_with_your_database_connection_string
```

Do not commit `.env` or paste real API keys into issues, screenshots, documentation, or public repositories. Keep `.env.example` free of real credentials and update it when the required variables change.

## Development commands

Run commands from the repository root unless otherwise noted.

| Command | Purpose |
|---|---|
| `pnpm install` | Install workspace dependencies |
| `pnpm --filter @workspace/api-server run dev` | Run the API server in development mode |
| `pnpm run typecheck` | Type-check the shared libraries and applicable workspace packages |
| `pnpm run build` | Run type checking, then available package build scripts |
| `pnpm --filter @workspace/reputelens build` | Build the frontend |
| `pnpm --filter @workspace/api-server build` | Build the API server |

Additional commands referenced by the repository's operational notes:

```bash
pnpm --filter @workspace/api-spec run codegen
pnpm --filter @workspace/db run push
```

Use API code generation when you intentionally need to regenerate generated API hooks/schemas from the API specification. The database push command is documented for development use; review the schema and target database before running it.

## Build

To run the workspace type check and available builds:

```bash
pnpm run build
```

To build the frontend and API server individually:

```bash
pnpm --filter @workspace/reputelens build
pnpm --filter @workspace/api-server build
```

The configured Render deployment uses the two individual build commands after installing dependencies. A successful build does not by itself confirm that required API keys, database connections, or external integrations are configured correctly at runtime.

## Deployment on Render

The repository includes a `render.yaml` Blueprint that describes a single Node.js web service named `reputelens`.

### Deploy

1. Push the complete project to GitHub.
2. Sign in to [Render](https://render.com/).
3. Create a new **Blueprint** and connect the GitHub repository containing `render.yaml`.
4. Review the service configuration before deploying.
5. Add the real `GROQ_API_KEY` in Render's environment settings. Do not commit it to GitHub.
6. Confirm any database-related variables required by the implementation, including `DATABASE_URL` if applicable.
7. Deploy and review the build and runtime logs.

### Blueprint configuration

The checked-in blueprint specifies:

- Runtime: Node.js
- Region: Oregon
- Build: install pnpm 10.33.0, install from the frozen lockfile, then build the frontend and API server
- Start command: `node artifacts/api-server/dist/index.mjs`
- Health check: `/api/system/status`
- LLM provider: `groq`
- Model: `qwen/qwen3.8-27b`

If deployment fails, compare the logs with the commands in `render.yaml` and verify that every required environment variable is configured in Render. Do not assume that the configured health-check route will pass until the service has started successfully.

## Alternative frontend deployment

A `netlify.toml` file is also present. It specifies:

- Build command: `pnpm --filter @workspace/reputelens build`
- Publish directory: `artifacts/reputelens/dist/public`
- Node.js 20 and pnpm 10.33.0
- SPA fallback routing and an optional `/api/*` redirect using `BACKEND_URL`

This configuration is for the frontend build. If you deploy the frontend separately, you must also deploy the API server and configure the backend URL and any required environment variables. Confirm that `BACKEND_URL` is set in the Netlify environment when using the API redirect.

## Security notes

- Never commit `.env`, `.env.local`, API keys, passwords, database credentials, or private datasets.
- Keep `.env.example` limited to descriptive variable names and placeholder values.
- Review `git status` and staged changes before each push.
- If a secret was committed, removing it in a later commit is not enough: revoke or rotate it, then follow GitHub's guidance for removing exposed secrets from repository history.
- Treat user-generated data and logs as potentially sensitive.

## Troubleshooting

**Install fails with a message to use pnpm**

The root `package.json` explicitly checks the package-manager user agent. Install pnpm and run `pnpm install` instead of `npm install` or `yarn install`.

**Frozen-lockfile install fails**

Check that `pnpm-lock.yaml` is in sync with the workspace manifests. If you intentionally changed dependencies, update the lockfile with pnpm, review the changes, and commit both the manifests and lockfile.

**The frontend loads but API calls fail**

Confirm that the API server is running, that the frontend is using the correct API base URL, and that any separately deployed frontend has the correct `BACKEND_URL` configuration.

**Render health check fails**

Inspect the service logs, verify that the start command completed, and check whether `/api/system/status` responds successfully. Also verify runtime environment variables.

**Language-model requests fail**

Confirm that `GROQ_API_KEY` is configured in the runtime environment and that `LLM_PROVIDER` and `GROQ_MODEL` match the provider/model expected by the application.

---

## License

The root `package.json` declares the project license as **MIT**. Add the full MIT `LICENSE` file to the repository if you intend to distribute the project under that license.
"""

path = Path("/mnt/data/README.md")
path.write_text(readme, encoding="utf-8")
print(f"Created {path} ({len(readme.splitlines())} lines, {path.stat().st_size} bytes)")
