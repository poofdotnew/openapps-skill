---
name: openapps
description: >-
  Build and ship apps on OpenApps (openapps.xyz, formerly oapps.fun): start here for a complete app,
  including work by an app's own agent, its keys and capabilities, and opening, then load the focused
  OpenApps skill for backend, frontend, deployment, or onchain work.
---

# OpenApps

An oApp is an OpenApps app with its own agent that can open to its community, so it can outlive its creator.
Use this skill for the app's capability constraints and, when requested, its lifecycle: Owned, on sale, and Open.

## Choose the workflow

- **External coding agent working for the creator of an Owned app:** use [creator publishing](../openapps-deploy/docs/quickstart.md#publish-an-owned-openapps-frontend-from-local-source) for a tested local frontend change.
  Having an app agent does not require sending that change through an AI run.
  `npx openapps propose --submit --apply` still asks the app agent to apply it; it is not the no-AI publishing path.
- **Creator asking to open an app:** use the lifecycle, launch-gate, and preflight references for the current phase.
  A build or edit request does not by itself request opening; continue work already authorized by the user.
- **Agent running on an OpenApps app computer:** the app already exists and you are its agent.
  Apply the capability constraints below and use the installed runtime-specific instructions for previews, releases, proposals, and the app's mode (`openapps-internal` when provided).
  Do not repeat creator initialization or ask to open the app to update it.
  Repo notes such as `AGENTS.md` and `README.md` may predate OpenApps; correct any fact they state that the run context contradicts (the creator, whether it is an OpenApps app) in your next commit.
  If those runtime instructions are unavailable, continue inspection and local work that the environment supports, and identify the missing instructions before attempting a governed release.

The project config is `openapps.json`. An older project's `bounded.json` still works; `openapps.json` wins when a directory holds both.

The app's agent does not load `AGENTS.md`, `CLAUDE.md` or other repo agent files into its context.
What guides it is `OPENAPPS.md` at the repo root (its text is capped at 8192 bytes) and the files that file links with relative Markdown links.
Both are read at the latest published release, so an edit counts only once a release carrying it publishes.

## Capability constraints

- Use OpenApps-managed hosting, data, auth, payments, wallets, onchain access, and AI, billed to the app's own credit pool and governed by its policy.
- To hold USDC for a deal, keep it in an escrow in the app's own policy that the app's agent settles: [App escrow](docs/app-escrow.md).
- An Owned app may keep services it already uses: its creator sets their keys with `npx openapps secret put` or Keys in the Fine print of the app's page, functions read them as `ctx.secrets`, and a [Next.js server](../openapps-frontend/docs/server-apps.md) reads them as `process.env`.
  A key never goes in source.
  `npx openapps secret put NAME --preview` (Previews only, on the app's page) sets the test key every preview of the app receives; the app's own value never reaches a preview.
  `npx openapps secret put NAME --agent --host <host>` gives the app's agent a key for its own work, sealed on its computer and sent only to those hosts.
  `npx openapps secret put NAME --app-id <sub-app>` sets a key on an app the app manages (`ctx.apps`) directly, without the agent donating one; the agent cannot replace it.
  Only the creator sees the Keys panel, and it lists names, never values.
- An APO requires that no person holds a key the app uses: the app holds no key a person set (its own values, preview values, or a key the creator put on its agent), and no function declares a key its agent did not donate.
  A key the app's own agent obtains and donates is fine: the platform adds it to the app's requests to that key's hosts, and a function that declares it reads only a stand-in.
- An app that runs a server cannot open yet.
  An app in an APO or Open cannot depend on a vendor account, external server, or admin backdoor that a person holds.
- Prefer native capabilities or live catalog actions, then the steward's x402 relay for supported paid APIs.
  For a capability whose availability is unknown, use `npx openapps services search "<need>"` and read the [capability ladder](docs/capability-ladder.md).
  Credential-free public endpoints still require declared egress.
- If a capability is unavailable, explain the constraint and the nearest compliant alternative, and continue the independent work within the user's scope.
  Do not silently replace a required feature or add a personal secret to bypass the constraint.

## Start

```bash
npm install -D openapps
npx openapps init
```

The CLI ships on npm as a project dev dependency, so the same two commands work
on macOS, Linux, and Windows with no shell involved (Node 20 or later). Run every
command as `npx openapps <command>`, as this skill family
writes them; npx runs the installed CLI, or fetches it from npm when it is missing.
For a global command, `npm install -g openapps` installs `openapps`.

If the install command is blocked, or `openapps` is not found afterward, ask the
user to run it in their own terminal, then continue with `npx openapps init`.
This setup needs a session that can run commands: a Claude Code session or
another agent with terminal access, not a chat-only surface.

`npx openapps init` also installs this skill family for the agents on the machine,
then opens OpenApps' hosted browser login when needed, then creates
`policy.json` and public project config. It reuses a valid saved web session.
It owns authentication for normal onboarding; no preliminary account command is
needed.

## Work by phase

Load only the focused skill for the phase in progress. Do not preload the whole
family.

| Current work | Skill |
|---|---|
| Policy, rules, invariants, functions, data, realtime, actor model, policy tests | **openapps-backend** |
| Client SDK, web/mobile UI, subscriptions, hosted frontend, app-user authentication | **openapps-frontend** |
| CLI, deploy, environments, source sync, domains, project config, collaborators, prompt-driven builds | **openapps-deploy** |
| Bring or migrate an existing app; first assess preserving its stack and hosting | **openapps-deploy** ([existing app guide](../openapps-deploy/docs/porting-an-existing-app.md)) |
| A third-party API the app needs: is it on OpenApps, callable through x402, or requestable | **openapps-backend** ([ctx.services](../openapps-backend/docs/functions-ctx-services.md)) |
| Embedded wallets, Solana, tokens, onchain transactions, onramp | **openapps-onchain** |

For a complete app, work through backend, frontend, then deploy. Add onchain
only when requested.

```text
design policy + functions -> build client -> deploy -> test happy path and a denied boundary
```

## Cross-cutting references

Load these only when the task calls for them:

- Billing, plan limits, credits, and upgrades: [docs/billing.md](docs/billing.md)
- Product analytics and web vitals: [docs/analytics.md](docs/analytics.md)
- Capability boundaries: [guides/capabilities-and-limits.md](guides/capabilities-and-limits.md)

## Core rules

- Act for the user: build, deploy, and test instead of only explaining.
- Read `openapps.json` first in an existing project. It selects the app,
  environment, policy, and account source.
- Use `@user.id` for ownership and membership. Use `@user.address` only for
  wallet/onchain semantics.
- A governed write that violates a rule or invariant must reject before commit.
  Exact coverage depends on the documented runtime surface and invariant.
- Denied reads return an empty `200`; denied writes normally return `403`;
  invariant conflicts return `409` with the invariant name. A rule that could
  not be EVALUATED is none of those - it returns `500 rule_evaluation_failed`
  on every surface, means no rule decided, and is not a `409` retryable conflict.
  Read `npx openapps decisions` for the cause; do not assume a retry will fail.
- A rule or invariant is enforced by the runtime as written. Check a policy with `npx openapps tests run` and by deploying it; do not look for a proof step.
- Before using an onchain plugin, run `npx openapps plugins list --json` and inspect its exact contract with `npx openapps plugins describe <plugin.function> --json` without treating its capability state as live-network proof.
- Give a collaborator access with `npx openapps share`; do not add application
  allowlists for control-plane access.
- Never put provider secrets in frontend code or commit credentials.

Install the public family with `npx skills add poofdotnew/openapps-skill -g -y --agent claude-code codex cursor`, naming the agents in use; without `--agent`, `-g` also prints a harmless PromptScript failure per skill.

## References

Read the reference needed for the current task.

| Task | Read |
|---|---|
| The agent an app has from creation, live values and preview values, Owned / on sale / Open, asking to open, the token sale, mainnet, source sync, one sale per app | [Lifecycle](docs/lifecycle.md) |
| App capability availability, unsupported dependencies, catalog requests, x402 payment and recovery semantics | [Capability ladder](docs/capability-ladder.md) |
| What opening checks: oApp mode, required boundaries and egress grants, secrets, the served site, `npx openapps propose` | [Launch gate](docs/launch-gate.md) |
| `npx openapps oapp preflight` and its report | [Preflight](docs/preflight.md) |
| Hide the platform widget on an Owned app (an Open app always shows it) | [Hide the widget](../openapps-frontend/docs/frontend-hosting.md#hide-the-widget) |
| Preparing to ask to open | [Opening checklist](docs/checklist.md) |
| Sealed launch economics, treasury fees, pending claims, operating reserves, how credits stay funded, and the agent's daily limit | [Launch economics](docs/launch-economics.md) |

For implementation mechanics, use [openapps-backend](../openapps-backend/SKILL.md), [openapps-frontend](../openapps-frontend/SKILL.md), or [openapps-onchain](../openapps-onchain/SKILL.md) as needed.
