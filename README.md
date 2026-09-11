# devx Labs — Client Account Plans

A dashboard + per-client account plan pages, styled to the devx Doctrine. Pure static HTML/CSS/JS — no build step, no backend.

## Structure

```
devx-account-plans/
  index.html            ← the dashboard (client roster)
  clients/
    comet.html           ← Comet's account plan
    (one file per client goes here)
  README.md
```

Every page is self-contained (styles and script inline), so any single file can also be opened directly, emailed, or printed on its own.

## Adding a new client

Three steps, every time:

1. **Duplicate the template.** Copy `clients/comet.html` to `clients/<slug>.html` (e.g. `clients/acme.html`). Use a lowercase, hyphen-free slug — it becomes the filename and the URL.
2. **Swap the data.** Inside the new file, find the `const CLIENT = { ... }` object near the top of the `<script>` block and replace every field with that client's info: stats, overview, goals, stakeholders, whitespace, and action items. Nothing below that object needs to change — it's generic render logic shared by every client page.
3. **Add it to the roster.** Open `index.html`, find the `const CLIENTS = [ ... ]` array, and add one entry:
   ```js
   {
     slug: "acme",
     name: "Acme Co",
     domain: "acme.com",
     industry: "SaaS",
     mrr: "₹1.2L / mo",
     status: { label: "Healthy", tone: "ok" }   // tone: "ok" | "warn" | "accent" | "muted"
   },
   ```

That's it — the dashboard, the search box, and the summary counts all update automatically from that array.

### Fastest way to do this with Claude

Paste your notes for the next client into a new chat in this project (or this same chat) and ask for the account plan. Once you're happy with it, ask Claude to "add this client to the dashboard the same way as Comet" — point it at this file structure and it can produce the new `clients/<slug>.html` and the matching roster entry directly.

## Running it locally

No install needed. Either:
- Open `index.html` directly in a browser, or
- From inside the folder, run a tiny local server so relative links behave exactly like production:
  ```
  npx serve .
  ```

## Deploying to Vercel

This is a static site, so Vercel needs zero configuration — no `vercel.json`, no build command.

**Option A — Vercel CLI (fastest, no GitHub needed)**
```bash
npm i -g vercel        # one-time install
cd devx-account-plans
vercel                 # first deploy — creates the project, gives you a preview URL
vercel --prod          # promotes it to your production URL
```
Follow the prompts (log in, confirm the project name, accept the defaults — it will auto-detect this as a static site).

**Option B — GitHub + Vercel (better once you're maintaining 30+ client files)**
1. Push this folder to a new GitHub repo.
2. Go to vercel.com → **Add New → Project** → import that repo.
3. Leave the framework preset as "Other" and the build command blank — Vercel will serve the files as-is.
4. Every future `git push` redeploys automatically, and you get a preview URL on every pull request, which is useful if a teammate is drafting a client's plan before it goes live.

Either way, `index.html` becomes your root URL (e.g. `https://your-project.vercel.app`) and each client plan is at `/clients/<slug>.html`.

## Notes on the current build

- The action-plan checkboxes and progress bar are session-only (no backend to persist them), same as the original Comet page. If you want checked state to survive a refresh or be shared across a team, that needs a small backend or a service like Supabase/Firebase — worth doing once you're past a handful of clients if this becomes a real workflow tool rather than a reference doc.
- The roster's "Needs attention" count is driven purely by whichever clients you mark with `tone: "warn"` in `index.html` — it's not computed from anything else, so keep that in sync with each client's actual status.
