# Optional: deployment tips

You don't have to host this on Cloudflare, but we recommend it, especially if you're new to this and need to get up
and running quickly. It has nearly everything this project needs in one place, it's fast, and it's extraordinarily
cheap: we built a fairly large version of this tool for **$5/month or less**. We've had no problems with it.

## What you'll use
- **Workers** runs the app: the website, the search, and the AI endpoints.
- **D1** is the database, holding the drafts and events tables from Stage 3.
- **Workers AI** creates the embeddings for search. It runs the same small model you use locally, so search behaves
  the same on your laptop and in production.
- **Cloudflare Access** is the login in front of the whole thing.

That's all. You don't need a separate database provider, hosting service, or login system.

## Why Access is the best part
Keeping a private app private is usually where things get hard: login pages, passwords, sessions, and the chance of
getting something subtly wrong with constituent data behind it. Access removes that work. You list who's allowed in
(for example, your team's email addresses), and Cloudflare handles sign-in before anyone reaches your app. Your app
receives a verified email address for each request and never has to think about login.

It's very secure because Cloudflare handles all the tricky parts. The only thing your app does is confirm that each
request really came through Access, and your coding agent can write that check in a few lines (see the non-negotiables
in the [README](README.md)).

## Getting started
1. Create a Cloudflare account and choose the $5/month Workers paid plan.
2. Put your domain on Cloudflare, or buy one there.
3. Tell your coding agent you're deploying to Cloudflare Workers with D1, Workers AI, and Access. It can do most of the
   setup from the command line with Cloudflare's `wrangler` tool.
4. In the Cloudflare dashboard, add an Access application for your domain and a policy listing who's allowed in.
