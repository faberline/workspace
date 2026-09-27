# workspace

Shared tooling for the [faberline](https://github.com/faberline) repositories:
the agent skills, hooks and dispatch every repository's agents run, the fleet
templates and their renderer, the git/gh/build/release routes, the metadoc
contracts, and the cross-repository GCP acceptance harness. It was split out of
the `faberline/axiom` monorepo with its history; each app now lives in its own
repository, and the monorepo is archived.

## Repositories

| Repository | What it is |
|------------|------------|
| [core](https://github.com/faberline/core) | The shared Rust libraries (`crates/<name>`): transport, server runtime, consensus, storage, auth, observability, Kubernetes integration, and the CLI standard commands. Consumers depend on it by git tag. |
| [arena](https://github.com/faberline/arena) | N-target competitive comparison runner — fan one workload across targets, ratio + ratchet-gate, one agent-readable JSON report. |
| [aw](https://github.com/faberline/aw) | The AW workflow engine and its Typer CLI. |
| [beam](https://github.com/faberline/beam) | Beam is the GPU vector database in the service stack. |
| [cap](https://github.com/faberline/cap) | `cap` keeps heavy local commands (`cargo test`, `uv run`, `pnpm build`, …) from eating the whole machine. |
| [cgdb](https://github.com/faberline/cgdb) | Cgdb is a local graph database for agentic codebase understanding. |
| [courier](https://github.com/faberline/courier) | `courier` is a stateless, GCP-hosted proxy that centralizes GitHub-issue access for every CLI. |
| [cube](https://github.com/faberline/cube) | Cube is the OLAP service in the service stack. |
| [defer](https://github.com/faberline/defer) | Defer is the Cloud Tasks-like delayed push-queue dispatch service. |
| [guard](https://github.com/faberline/guard) | Security posture gate. |
| [jet](https://github.com/faberline/jet) | Jet is a Rust-native frontend toolchain. |
| [keep](https://github.com/faberline/keep) | Cloud-native, multi-core key-value / claim-check store — the loom/relay data plane and a Redis / Dragonfly replacement. |
| [loom](https://github.com/faberline/loom) | Loom is the workflow scheduler in the service stack. |
| [lumen](https://github.com/faberline/lumen) | Lumen is a Kubernetes-native search service. |
| [mamba](https://github.com/faberline/mamba) | Force-typed Python compiler. |
| [mesh](https://github.com/faberline/mesh) | Mesh is the relationship/property-graph service in the service stack. |
| [meter](https://github.com/faberline/meter) | Local resource measurement for agent-driven Rust development. |
| [pgpool](https://github.com/faberline/pgpool) | Kubernetes-native PostgreSQL connection pooler. |
| [pm](https://github.com/faberline/pm) | Local, agent-first project management MCP server. |
| [preview](https://github.com/faberline/preview) | `preview` manages MR-scoped UAT preview environments for GKE. |
| [relay](https://github.com/faberline/relay) | `relay` is the online single-cast pull work-queue broker (RabbitMQ/SQS-shaped). |
| [rig](https://github.com/faberline/rig) | Declarative test-scenario and load harness engine. |
| [sift](https://github.com/faberline/sift) | Sift is the GCP/GKE-first operational event platform. |
| [som](https://github.com/faberline/som) | The Snippet-Oriented Model: the `som-core` engine and CLI and the `som-code-python` corpus. |
| [tape](https://github.com/faberline/tape) | Tape is the topic replay journal in the service stack. |
| [truce](https://github.com/faberline/truce) | Forward-only PostgreSQL schema evolution control plane; planned, holding its backlog only. |
| [vat](https://github.com/faberline/vat) | `vat` is a headless local development test runner for coding/ML agents. |
| [workbench](https://github.com/faberline/workbench) | Terminal-first desktop workbench that launches Claude Code, Codex, or AGY through their native CLIs in a real PTY. |

Apps depend on `core` by git tag and on sibling apps by git revision.

## Working across repositories

Clone the repositories you work on side by side:

```
~/faberlines/workspace
~/faberlines/aw
~/faberlines/<app>
```

Install the `aw` CLI with `uv tool install git+https://github.com/faberline/aw`;
the skills and agent roles run it as `aw <group> …`. The gates under
`.claude/aw/verification/` measure the aw engine in the checkout that
`FABERLINE_AW` names, else in `../aw`.

## Layout

| Path | What it holds |
|------|---------------|
| `.agents/`, `.claude/`, `.codex/`, `.gemini/` | Skills, hooks, dispatch, output styles and the rendered agent fleet. Each app repository carries its own `<app>-*` roles. |
| `.claude/aw/` | The AW verification gates and migrations. |
| `scripts/agents/` | Fleet templates and `render_fleet.py`. |
| `scripts/git/`, `scripts/gh/`, `scripts/build/`, `scripts/release/` | Git, GitHub, build and release routes. |
| `scripts/meta/` | README and project-docs contracts. |
| `acceptance/` | GKE acceptance harness for the services. |
| `benchmarks/gcp-managed-services/` | GCP managed-service calibration. |

## Known gaps after the split

- `.github/workflows/gke-acceptance.yml` calls the test-image workflows that
  moved into the keep, defer, relay and loom repositories, and
  `acceptance/gke-harness/scripts/run-app.sh` copies `apps/<app>/k8s`; the
  harness needs to check out those repositories first.
- `scripts/agents/render_fleet.py` renders the whole fleet into this tree; the
  `<app>-*` roles it writes belong to the app repositories.
- `CONTRIBUTING.md` still describes the monorepo layout (`apps/<app>/…`).

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the authoring contract shared by
every repository: how to shape files, paths, and names so the tree stays
legible to agents and tooling, the shared **service archetype**, and the
**CLI convention** every binary follows (`llm` / `upgrade` / `issue`).

## License

MIT
