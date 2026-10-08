# Rallly

[Rallly](https://github.com/lukevella/rallly) is open-source scheduling
polls ("when works for everyone?"). This folder holds **configuration only**.
Railway runs the official image `lukevella/rallly:4`, so there's no fork to
maintain and updates mean bumping the image tag.

> Rallly is AGPL-3.0. Running the unmodified image is fine. If we ever
> vendor and change its source, the changed source has to be offered to users.
> Since station is public, that would happen automatically.

## What it needs

| Need | Notes |
|---|---|
| **Postgres** | Bird Trivia has no database, so there's nothing to share. Add a Railway Postgres to the project, or use a free external one (Neon, Supabase). Give Rallly its own database named `rallly`. |
| **SMTP** | Required. Sign-in is by emailed magic link. A free tier from Resend or Brevo works. |
| **S3-compatible storage** | Rallly v4's compose stack wires `S3_*` variables (bundled Garage by default). Cloudflare R2's free tier fits. Check whether the app runs without it before paying for anything. |
| **Memory** | Upstream recommends a 2 GB server for the *whole* stack (app + db + proxy + storage). The app alone needs less. Watch Railway metrics. |

## Railway service settings

- **Source:** Docker image `lukevella/rallly:4` (not this repo)
- **Replicas:** 1
- **Serverless (app sleeping):** on. Polls get occasional traffic, and a few
  seconds of cold start is fine.
- **Variables:** copy from [`.env.example`](.env.example)
