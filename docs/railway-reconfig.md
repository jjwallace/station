# Railway reconfig: moving Bird Trivia onto station

## Does triviabird.com keep working?

**Yes.** A custom domain belongs to a Railway **service**, not to a GitHub repo.
We keep the same service and change only where it pulls code from. The
domain, DNS records, TLS certificate and environment variables all stay put.

Checked 2026-10-08: `triviabird.com` resolves to `69.46.46.88` (Railway,
`RLWY-HIKARI-01`) and answers with `server: railway-hikari`.

**The one way to break it:** creating a *new* service for Bird Trivia instead
of repointing the existing one. The domain would stay on the old service, and
you'd have to move it and update DNS. Don't do that.

## What changes

| Setting (Bird Trivia service → Settings) | Before | After |
|---|---|---|
| Source repo | `jjwallace/bird-trivia` | `jjwallace/station` |
| Branch | `main` | `main` |
| Root Directory | *(repo root)* | `/platforms/bird-trivia` |
| Watch Paths | *(none)* | `/platforms/bird-trivia/**` |
| Custom domain | `triviabird.com` | **unchanged** |
| Variables | — | **unchanged** |

The build config moves with the code: `nixpacks.toml` and `package.json`
are now in `platforms/bird-trivia/`. Railway builds inside the Root Directory,
so it picks them up there with no edits.

## Steps

1. **Before you start:** open the Bird Trivia service and note its custom
   domain(s) and current deployment, so you know what "working" looks like.
2. **Settings → Source:** disconnect `jjwallace/bird-trivia` and connect
   `jjwallace/station`, branch `main`. If station doesn't show up, give the
   Railway GitHub app access to it (GitHub → Settings → Applications →
   Railway → Repository access).
3. **Settings → Root Directory:** `/platforms/bird-trivia`
4. **Settings → Watch Paths:** `/platforms/bird-trivia/**`
5. **Deploy** (or push any commit to station) and watch the build logs. You
   should see it run `cd backend && npm install` / `cd frontend && npm install`
   from `nixpacks.toml`.
6. **Verify:**
   ```bash
   curl -sI https://triviabird.com | head -3   # expect HTTP/2 200
   ```
   Then open the site and join a room, which checks that the Socket.IO
   connection works too.
7. **Archive the old repo** once it's been stable for a few days:
   ```bash
   gh repo archive jjwallace/bird-trivia
   ```
   Archiving keeps it readable. Don't delete it.

## Rollback

Point **Settings → Source** back at `jjwallace/bird-trivia` and clear the Root
Directory and Watch Paths. Nothing else changed, so that fully restores the old setup.
Or redeploy the previous deployment from the Deployments tab.

## Adding Rallly to the same project

See [`platforms/rallly/README.md`](../platforms/rallly/README.md). In short:

1. **+ New → Docker Image →** `lukevella/rallly:4`
2. Rallly needs a Postgres. Bird Trivia has none, so add one (Railway
   Postgres, or Neon/Supabase free tier) and create a database `rallly` in it.
3. Set the variables from `platforms/rallly/.env.example`.
4. Replicas 1, Serverless on.
5. Add its domain (e.g. a subdomain like `meet.<your-domain>`) under
   **Settings → Networking**, then add the CNAME record Railway shows you.
