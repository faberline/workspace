---
name: rust-arch-check
description: "Check Rust repos under ~/faberlines against the structure, DDD, and large-file contract (scripts/meta/rust_arch_contract.py), draft a repo's ddd.toml, or explain a finding. Use when the user asks for a rust-arch check, an architecture or DDD baseline, a large-file audit, or to onboard a repo onto ddd.toml."
---

# rust-arch-check

The checker is the contract. `scripts/meta/rust_arch/policy.toml` holds every
organisation-wide constant; each repo's `ddd.toml` declares its own contexts,
layers, and allowlists. Your job is to run the checker, relay what it found,
and change configuration only when the user asks.

## Goal

Return the checker's verdict for the requested repos: the status, the exit
code, and the findings grouped by rule. Each finding keeps its path and line.

## How

1. Run from the workspace repo root. uv resolves the tree-sitter
   dependencies from the script header.

```bash
scripts/meta/rust_arch_contract.py check --baseline               # every repo under ~/faberlines, report only
scripts/meta/rust_arch_contract.py check --repo lumen             # one repo; enforces only if ddd.toml says so
scripts/meta/rust_arch_contract.py check --repo lumen --base origin/main   # adds the R1 ratchet
scripts/meta/rust_arch_contract.py check --strict --json out.json # treat every Rust repo as enforced
scripts/meta/rust_arch_contract.py scaffold --repo lumen          # print a draft ddd.toml; writes nothing
```

2. Read the exit code:
   - `0` means no repo failed.
   - `1` means an enforced repo has at least one error.
   - `2` means the tool could not read a repo (for example, invalid TOML).
     Relay the `note:` line.
3. Relay the summary table and the findings for the rules the user cares
   about. Pass `--verbose` to see every finding. The JSON report (schema
   `rust-arch-report/1`) is the machine-readable form.
4. The rule families:
   - **P**: `ddd.toml` drift from the canonical policy, or an invalid entry.
   - **A**: tests layout, Cargo shape (D2), the `mod_module_files` lint, the
     root `.md` whitelist, and the docs skeleton.
   - **B**: context coverage, domain purity, layer direction, cross-context
     access, thin `lib.rs`, and thin binaries.
   - **C**: files over `size_warn` or `size_error`, and `[[large_files]]`
     allowlists.
   - **R**: the ratchet against the merge-base `ddd.toml`.

   A repo without `ddd.toml` is `report`. It gets one B1 summary per crate,
   and B2–B4 are skipped.
5. To onboard a repo, pipe `scaffold` output to the user as a draft. The
   draft starts with `enforce = false`. Every `TODO` reason must be replaced
   by a real one before the draft is committed.

## Acceptance

- The command and its exit code are quoted as they ran.
- Every claim about a repo points at a finding (rule, path, line) or a note.

## Never

- Never edit `policy.toml` or a `[policy]` table to make a repo pass. A
  policy change applies to every repo and needs the user's decision.
- Never raise `max_lines`, add a `[[large_files]]` entry, or add an
  `[[exceptions]]` entry without the user's reason. R1 rejects all three
  against the base anyway.
- Never write or change `ddd.toml`, source, or Cargo files in another repo
  unless the user asked for that repo.
- Never report a repo as clean from `--baseline` alone. Baseline exits `0`
  by design.
