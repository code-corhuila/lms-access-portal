# lms-access-portal

> Access bounded context: web UI (remote)

Part of the **LMS Library** distributed system — team `lms-library`, Grupo 2.
Governance and documentation live in [`library-docs`](https://github.com/code-corhuila/library-docs).

Built in **Vue 3**, a deliberate exception to the rest of the system's React stack — see
`library-docs/05-architecture/decisions/records/ADR-011-access-service-node-vue-exception.md`.

## Migration scope

**Functional port of** `lms-library` → `LoginPage.tsx` (React) — same fields, same guard, same
generic-error handling, reimplemented in Vue per `ADR-011`.

Consume the shared HTTP client and session from `lms-front` (`shell/apiClient`, `shell/session`
via Module Federation); do not re-implement them here.

## Structure

```
src/
├── main.ts        → standalone-dev entry (mounts App directly outside the shell)
├── App.vue        → trivial wrapper, only used in standalone-dev mode
├── router.ts      → single route, only meaningful in standalone-dev mode
├── mount.ts        → exposed to lms-front via Module Federation (federation({ exposes }))
├── views/
│   └── LoginView.vue  → the actual login screen
├── composables/
│   └── useSession.ts  → isolates the federated 'shell/session' import to one file
└── shims/
    └── shell.d.ts      → ambient types for 'shell/apiClient' / 'shell/session'
```

**Interop note:** a Vue component tree can't be exported as a React `RouteObject[]` the way the
other three (React) portals are. This portal exposes `mount(el, onAuthenticated): () => void`
instead — `lms-front` wraps it in a small adapter component. See `ADR-011` for the full
rationale.

The full map lives in `library-docs`.

---

## Branching

Three permanent branches. **None of them accepts a direct commit** — you enter through a child
branch and leave through a Pull Request.

```
develop  <--PR--  feat/... fix/... chore/...
qa       <--PR--  qa/...
main     <--PR--  release/...  hotfix/...
```

Promotion happens **by re-application** (`git cherry-pick -x`), never by merging one permanent
branch into another: `merge develop -> qa` and `merge qa -> main` do not exist in this model.

`main` requires **1 approval from `ariel5253`**. On `develop` and `qa` the team sets its own review
rule.

Full policy: `00-governance/branching-policy.md` in `library-docs`.
