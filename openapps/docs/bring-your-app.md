# Bringing an app in

The Launch page gives every user the same snippet, ending in `npx openapps deploy --create`. A folder with `openapps.json` is already deployed: deploy it without `--create` (a second app would orphan the first), with source synced.

## The link

The venue host changes (it is `beta.openapps.xyz` during the beta), so never write it yourself. Print the `bring it in:` link a READY `npx openapps oapp preflight` reports; it carries the app id, and the page preselects the app for the signed-in owner.

Until the CLI prints one: `https://openapps.xyz/launch`, then **An app I already have**, **Sign in**, pick the app.

## The two answers

The page asks two optional questions. Write them in its own voice: one plain sentence about what the app does for the people who use it. No stack, no file names.

| Question | Limit | The page's example |
|---|---|---|
| What is it? | 1200 characters | A trail map that weekend hikers use to plan and share routes. |
| What should its agent work on first? | 120 characters | Alerts when a favorite trail changes. |

Put them in `openapps.json` as `description` and `agent.firstTask` before the deploy that syncs the source; the page prefills from there. Until it does, paste them for the user.

## What to say

```
<App> is ready to bring in. Open this link, sign in, change the text if you want, and press Launch:
<link>
```

Attaching keeps the app Owned. Asking to open is a separate step ([lifecycle](lifecycle.md#from-owned-to-open)).
