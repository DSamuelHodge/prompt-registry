# Copilot Instructions for Prompt Registry Codebase

## Overview
- This repo manages a **Prompt Registry**: a system of record for prompt specifications used in production LLM systems.
- Prompts are versioned, reviewed, tested, and deployed as first-class artifacts, with strict governance and lifecycle policies.
- The codebase is organized for traceability, auditability, and safe prompt evolution.

## Key Structure
- `repo/prompts/specs/` — Main location for prompt specs, organized by domain and prompt name. Each prompt has:
  - `prompt.md` (Prompt IR + text)
  - `metadata.yaml` (registry metadata)
  - `changelog.md`
- `repo/evaluation/` — Test harnesses and datasets for prompt validation:
  - `harness/` — Scripts for running golden, variance, and adversarial tests
  - `datasets/` — Golden, adversarial sets
  - `reports/` — Test results
- `repo/governance/` — Policies (logging, risk, rollback) and review forms
- `repo/runtime/` — Runtime bindings, clients, and tool routing logic

## Developer Workflows
- **Register a new prompt:**
  1. Create a directory under `prompts/specs/<domain>/<prompt_name>/`
  2. Add required files (`prompt.md`, `metadata.yaml`, `changelog.md`)
  3. Set initial version to `0.1.0`
- **Review:** Use checklists in `governance/review_forms/` and follow risk-based review thresholds.
- **Test:**
  - Use scripts in `evaluation/harness/` (e.g., `run_golden.py`, `run_variance.py`)
  - Place test sets in `evaluation/datasets/`
  - Log results in `evaluation/reports/`
- **Deploy:**
  - Versioning: MAJOR for contract/intent, MINOR for structure, PATCH for wording
  - Bindings in `runtime/bindings/` (per environment)
- **Rollback:**
  - Follow `governance/policies/rollback_policy.md`
  - Rebind to last stable version, log event

## Project Conventions
- **No prompt enters production without registry entry and review.**
- **All prompt changes require version increment.**
- **High-risk prompts require acceptance tests.**
- **Rollback events must be logged and reviewed.**
- **Prompts are loaded by `id + version` at runtime, not embedded.**

## Examples
- See `repo/prompts/primitives/` for reusable prompt patterns.
- See `repo/governance/policies/` for non-negotiable rules.
- See `README.md` for full lifecycle and governance details.

## Integration Points
- Runtime clients in `runtime/clients/` handle prompt loading and tool routing.
- Bindings in `runtime/bindings/` map prompt versions to environments.

---
**For more, see the top-level `README.md` and `repo/governance/policies/`.**
