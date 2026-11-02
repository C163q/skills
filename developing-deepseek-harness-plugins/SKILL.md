---
name: developing-deepseek-harness-plugins
description: Use when creating, extending, packaging, or troubleshooting a DeepSeek Harness (dsh) plugin, including Host plugins and Web UI extensions.
---

# Developing DeepSeek Harness Plugins

DeepSeek Harness composes its capabilities as Cordis plugins. Build on the target version's extension points; use the documentation and source below instead of assuming an API or patching the agent loop.

## Find the authoritative material

The documentation site is <https://deepseek-harness.github.io/deepseek-harness/>. Replace a page's `.html` suffix with `.md` for raw Markdown. English pages live under `/en/`; the unprefixed site is Chinese.

| Need | Start here |
| --- | --- |
| First plugin, tool, configuration | [Basic tutorial](https://deepseek-harness.github.io/deepseek-harness/en/develop/basic/index.md); follow its tool and configuration links |
| Dependencies, events, disposal | [Cordis lifecycle](https://deepseek-harness.github.io/deepseek-harness/en/develop/framework/index.md); follow its services and events links |
| Bundle manifests, profiles, installation | [Packaging guide](https://deepseek-harness.github.io/deepseek-harness/en/develop/basic/publish.md) |
| Architecture and extension ownership | [Architecture](https://deepseek-harness.github.io/deepseek-harness/en/reference/index.md) |
| Browser entry and dependencies | [Client Modules](https://deepseek-harness.github.io/deepseek-harness/en/reference/subsystems/client-modules.md) |
| UI placement, hooks, shared state | [Web Client Slots](https://deepseek-harness.github.io/deepseek-harness/en/reference/subsystems/slots.md) |

Use a source checkout supplied by the user or already identified in the task context. If its location is unknown, ask the user where it is, or inspect the upstream repository directly. Clone into an appropriate development directory when a local checkout is needed for builds or tests.

Harness upstream: <https://github.com/deepseek-ai/deepseek-harness>.

Prefer these official examples from that repository:

- Minimal tool plugin: [`greet` tutorial](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/user/develop/basic/tool.md).
- Minimal external bundle: the packaging guide above includes `hello-plugin`, its manifest, patch, and install flow.
- Web UI plugin: [`packages/client/ui-settings-web-search`](https://github.com/deepseek-ai/deepseek-harness/tree/master/packages/client/ui-settings-web-search); inspect its manifest, Host entry, browser entry, and tests alongside the [settings-card cookbook](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cookbook/adding-a-settings-card.md).

The tutorials contain examples to build; the Web UI package is a maintained in-tree implementation. Adapt its workspace dependencies and build tooling for an external package rather than copying them unchanged.

Paths below are relative to the relevant repository root. Read applicable `AGENTS.md` files. Record the target Harness version/commit: APIs are pre-stable, and online documentation may describe a different release. When browsing upstream, select the matching tag or commit where available.

## Navigate from source to a plugin

1. Define the surface: Host command/tool/service, Web UI contribution, or both. Identify session scope, persistence needs, configuration, and compatibility targets.
2. In the Harness checkout, read `docs/architecture.md` and the relevant `docs/subsystems/` page. Tutorials are in `docs/user/develop/`; `docs/cordis-primer.md` explains Cordis. Generated catalogs and subsystem pages point to current service/event definitions. `website/docs.ts` maps source documents to site routes.
3. Trace the owning package's public types, implementation, callers, and focused tests. Packages generally live at `packages/<group>/<package>/`; inspect their `package.json` exports. Use `rg` for the exact service, event, or Slot name. For browser packaging, also inspect `packages/client/modules/README.md` and `packages/client/tsdown.client.ts`. Do not infer dispatch, waterfall, or approval behavior from an audit log or an old example.
4. Start with the official example matching the feature, then compare a nearby built-in plugin for production behavior and focused tests. Use the packaging guide for external bundle structure. Treat examples as patterns to verify against the target release, not templates to copy wholesale. Community plugins can supplement these sources when their version compatibility is established.
5. Implement through services, events, Slots, and configuration composition. If no suitable extension exists, document the missing seam before proposing a Harness change.

For source execution, follow the checkout's README: install with its declared package manager, build the artifacts, then launch through `pnpm dsh web`. The source launcher does not automatically rebuild browser artifacts.

## Organize the package

Use a small external package; grow it only as the feature requires:

```text
dsh-example/
  package.json          # ESM, exports, supported engines, package files, dsh manifest
  cordis.patch.yml      # bundle contribution with stable, unique row IDs
  src/
    index.ts            # Host entry and composition
    config.ts           # schema and resolved options, when needed
    client/             # browser-only entry/components, when needed
  test/                 # focused behavior and composition tests
  README.md             # install, configuration, behavior, limits, file responsibilities
  lib/                  # generated artifacts for a compiled package
```

Plain JavaScript can ship directly. Keep domain logic separate from registration code. Add `scripts/` or `docs/` when useful; split service definition, provider, and consumer into packages only when they need independent replacement or evolution.

## Requirements to preserve

- Declare required services with `inject`. Own registrations and custom resources through Cordis lifecycle APIs; verify cleanup on unload/reload. A waterfall delegates with `next()`; omitting it intentionally intercepts the chain.
- Expose deployment choices as validated configuration and fail visibly on invalid values. Keep runtime state scoped to its owning instance/session.
- Keep Host code and secrets out of browser bundles. For Web plugins, follow the current `./client` export, `dsh.client`, shared-module, and bundle-format contracts. Host-only plugins need none of that browser machinery.
- Use existing UI components, localization, and theme conventions. Handle loading, failure, session switching, and reconnect states where the feature depends on them.
- A distributable bundle declares `dsh.bundle.patch`; an ordinary dependency does not activate a plugin layer. Bundle and profile manifests serve different purposes. Check composition: replacing a row's `config` replaces the whole value, not individual keys.
- Declare compatibility ranges from tested releases. Shared Host package instances generally require peer dependencies plus development dependencies; independently owned runtime libraries belong in dependencies. Avoid publishing local checkout paths or workspace-only dependency specs.
- For authorization, filesystem, network, or RPC changes, identify the trust boundary and use the owning security seam. Authenticate and validate boundary inputs; preserve policy ordering and fail closed. Security controls belong on the Host. Follow the session-format contract for persisted events.

## Verify the delivered experience

Use a disposable `DSH_HOME` and a dedicated workspace; leave the user's daily profile untouched. Read the current CLI help/reference for profile initialization and overlay flags. Local source overlays need absolute module paths because the overlay does not change the profile's module-resolution directory. Inspect `--dump-config` before diagnosing loading failures.

Run checks appropriate to the change: behavior tests, type/syntax checks, build, and unload/reload checks. For browser-visible changes, launch the actual Web profile and exercise it with Playwright and Chromium; inspect console/network failures and capture screenshots. For model-facing features, verify the real tool/session outcome when needed, not just a visible button. The Harness browser-picker overlay is `apps/web/tests/pin-browse-picker.overlay.yml`.

Pack the plugin and install the tarball into a clean test profile. Verify exports, patch files, browser assets, runtime dependencies, and startup without local symlinks or ancestor `node_modules`. Git installs may require build-script permission and a self-contained `prepare`; consult the packaging guide rather than assuming builds run. CI must install locked dependencies before checks and tests. Report the versions, commands, artifacts, and any unverified surfaces.
