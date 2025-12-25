# Prompt Registry README

## Purpose

The Prompt Registry is the system of record for **prompt specifications** used in production.
It governs how prompts are **created, reviewed, versioned, deployed, monitored, and rolled back**.

Prompts are treated as **first-class artifacts**, equivalent to APIs or configuration schemas.

---

## Scope

This registry applies to:

* All prompts used in production systems
* All prompts that route, transform, or generate user-visible or system-consumed outputs
* All prompts that call tools or trigger downstream actions

Ad-hoc prompts used for exploration are out of scope.

---

## Prompt Lifecycle Overview

```
Design → Create → Review → Test → Deploy → Monitor → Revise / Rollback
```

Each stage has explicit entry and exit criteria.

---

## 1. Create

### Objective

Translate intent into a structured Prompt IR and register it as a versioned artifact.

### Steps

1. Identify task type and risk level
2. Select appropriate Prompt Primitive(s)
3. Determine minimum IR level
4. Create a new prompt directory:

   ```
   prompts/specs/<domain>/<prompt_name>/
   ```
5. Populate required files:

   * `prompt.md` (Prompt IR + final prompt text)
   * `metadata.yaml` (registry metadata)
   * `changelog.md`

### Requirements

* Prompt MUST include:

  * Intent
  * Inputs
  * Outputs
  * Constraints
  * Control Policy
* Risk level MUST be declared
* Owners and reviewers MUST be assigned

### Exit Criteria

* Prompt IR is complete
* Metadata is valid
* Initial version set to `0.1.0`

---

## 2. Review

### Objective

Catch structural, logical, and risk issues **before execution**.

### Review Types

* **Prompt Review** (mandatory)
* **Domain Review** (for medium+ risk)
* **Trust / QA Review** (for high+ risk)

### Process

1. Reviewer completes Prompt Review Checklist
2. Scores recorded in PR comments or review artifact
3. Red flags must be resolved or explicitly waived

### Minimum Review Thresholds

| Risk Level | Minimum Score | Mandatory Dimensions           |
| ---------- | ------------- | ------------------------------ |
| Low        | 9             | Contract                       |
| Medium     | 15            | Contract, Constraints, Control |
| High       | 18            | + Representation               |
| Critical   | 21            | All                            |

### Exit Criteria

* Review score meets threshold
* Required approvers sign off
* Acceptance tests defined for high+ risk prompts

---

## 3. Test

### Objective

Empirically validate behavior and prevent regressions.

### Required Tests by Risk Level

| Risk     | Tests Required                  |
| -------- | ------------------------------- |
| Low      | Manual spot check               |
| Medium   | Golden set                      |
| High     | Golden + variance               |
| Critical | Golden + variance + adversarial |

### Test Artifacts

* Golden input/output pairs
* Invariant checks (schema, constraints)
* Variance bounds (where applicable)

### Exit Criteria

* All required tests pass
* Results logged in `evaluation/reports/`

---

## 4. Deploy

### Objective

Safely make the prompt available to runtime systems.

### Deployment Model

* Prompts are deployed **by reference**, not embedded
* Runtime loads prompt by `id + version`

### Steps

1. Increment version:

   * MAJOR: contract or intent change
   * MINOR: constraints or structure change
   * PATCH: wording, formatting, or clarification
2. Tag release in version control
3. Register version in Prompt Registry
4. Bind prompt to environment (`dev`, `staging`, `prod`)
5. Deploy using canary or A/B strategy (high+ risk)

### Exit Criteria

* Prompt version active in target environment
* Rollback path confirmed

---

## 5. Monitor

### Objective

Detect failures, drift, or regressions in production.

### Required Telemetry

* Prompt ID + version per execution
* Input/output samples (per logging policy)
* Validation failures
* User/system feedback signals

### Key Metrics

* Acceptance test pass rate
* Schema violation rate
* Output variance
* Human correction rate
* Downstream system errors

### Review Cadence

* Medium risk: monthly
* High risk: weekly
* Critical: continuous monitoring

---

## 6. Revise

### Objective

Improve prompt behavior safely and audibly.

### Triggers

* Metric degradation
* New failure mode
* Changed requirements
* Model or tool changes

### Rules

* Never patch by adjusting temperature alone
* Diagnose structural cause first
* Update Prompt IR
* Add or update regression tests
* Increment version appropriately

---

## 7. Rollback

### Objective

Restore last known good behavior quickly.

### Rollback Conditions

* Acceptance metrics below threshold
* Increased hallucinations
* User harm or compliance risk
* Downstream system failure

### Rollback Procedure

1. Disable current prompt version
2. Rebind runtime to last stable version
3. Log rollback event and rationale
4. Open incident review

### Post-Rollback

* Root cause analysis
* Prompt IR update
* New tests added
* Controlled redeploy

---

## 8. Deprecation

### When to Deprecate

* Prompt superseded by newer version
* Task no longer required
* Model/runtime incompatibility

### Process

1. Mark status as `deprecated`
2. Set deprecation date
3. Update dependents
4. Archive after retention period

---

## 9. Governance Rules (Non-Negotiable)

* No prompt enters production without registry entry
* No prompt change without version increment
* No high-risk prompt without acceptance tests
* No rollback without incident record

---

## 10. Guiding Principle

> **Prompts are living specifications.
> Treat them with the same rigor as code that touches users.**

---

If you want, the next practical artifact would be:

* a **Prompt CI pipeline definition**, or
* a **prompt lint ruleset with examples**, or
* a **worked example** showing a prompt moving through this lifecycle end-to-end.

Say which one you want next.
