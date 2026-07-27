# Xelium API — Render.com Deployment

## Quick Start

### 1. Create a Render Web Service
- Go to render.com → New → Web Service
- Connect your repo OR use "Deploy from existing Docker/build"
- **Runtime**: Node
- **Build Command**: `npm install`
- **Start Command**: `npm start`

### 2. Add a PostgreSQL Database
- Render Dashboard → New → PostgreSQL
- Copy the **Internal Database URL** → set it as `DATABASE_URL`

### 3. Set Environment Variables in Render Dashboard
| Variable | Description |
|---|---|
| `DATABASE_URL` | PostgreSQL connection string (from Render Postgres or external) |
| `GROQ_KEY_DEFENDER` | Groq API key for the defender AI agent |
| `GROQ_KEY_ATTACKER` | Groq API key for the attacker AI agent |
| `FRONTEND_URL` | Your Netlify URL, e.g. `https://your-app.netlify.app` |
| `SESSION_SECRET` | Any long random string for session signing |
| `LOG_LEVEL` | Optional — `info` (default), `debug`, `warn`, `error` |
| `ADMIN_SECRET` | Optional — overrides default admin password |

> **PORT is set automatically by Render — do not add it.**

### 4. Run DB Migration (first deploy only)
After first deploy, open Render Shell and run:
```
node -e "
import('./dist/index.mjs').catch(()=>{});
" 
```
Or push the schema manually using drizzle-kit from your local machine:
```
DATABASE_URL=<render-db-url> npx drizzle-kit push --force
```

### 5. Set Netlify Frontend Variable
In Netlify → Site Settings → Environment Variables:
| Variable | Value |
|---|---|
| `VITE_API_BASE_URL` | Your Render service URL, e.g. `https://xelium-api.onrender.com` |

Then trigger a Netlify redeploy.

## No Persistent Disk Needed
All data is stored in PostgreSQL (users/sessions) and Firebase (theories, stories, reports, profiles).
The server is fully stateless — no disk persistence required.
