# TASKS — model-cards-oncology

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## How these tasks map to Hee-Lee Oss

Each task below becomes a Hee-Lee Oss **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- `id` — stable slug ID from the tables (e.g. `model-cards-oncology-schema-001`).
- `title` — the table's Title.
- `project` — `model-cards-oncology`.
- `type` — one of `code | research | writing | data | design-spec | maintenance` (per table). Card
  authoring = `writing`; gates/standards = `design-spec`; tooling = `code`; triage/outreach = `research`;
  drift/refresh = `maintenance`. We never use `data` (we produce no datasets).
- `lane` — `donated` for all tasks here (no funded escrow). A funded task would add `fundedBudgetUsd`.
- `priority` — `high | medium | low`.
- `domain` — array, e.g. `["cancer","oncology","ml","model-documentation","open-science"]`.
- `riskTier` — `low | medium | high`. Foundational template/tooling = `low`; license/data/bias
  judgement = `medium`; patient-facing plain-language = `high` (oncologist + advocate sign-off).
- `urgent` — boolean; `false` for all current tasks.
- `deliverable` — `pr | dataset | document | translation`. Cards/standards/triage = `document`;
  tools = `pr`; translations = `translation`. We never deliver `dataset`.
- `tokenEstimate` — `small | medium | large` (Size column).
- `status` — `open | in-progress | review | delivered | done`; all start `open`.
- `context`, `objective`, `acceptanceCriteria[]`, `resources[]`, `output` — per task.
- `requestor` — TO BE SECURED until a model maintainer/hub partner is confirmed.
- `verifiedNeed` — **`false`** for all tasks until a named maintainer/hub agrees to accept a card
  (the general need is real; per-task delivery need is unproven). Flips to `true` per task only when
  a named partner confirms acceptance.
- `outputLicense` — `CC-BY-4.0` for cards/standards/documentation; `MIT` for code (validator,
  adapters, harness).

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-schema-001 | Canonical model-card schema + template (Model Cards + Healthsheet + TRIPOD+AI/CLAIM) | writing | small | low | document | — | Technical, Data-License+Privacy |
| model-cards-oncology-reviewer-002 | Name/secure the Data-License+Privacy reviewer (blocking gate role) | research | small | low | document | — | Maintainer |
| model-cards-oncology-gate-003 | Model + cancer-data license/de-identification gate (blocking checklist) | design-spec | small | medium | document | schema-001 | Data-License+Privacy |
| model-cards-oncology-ncpolicy-004 | NC / share-alike acceptance policy (COSMIC/OncoKB, cBioPortal) | design-spec | small | medium | document | gate-003 | Data-License+Privacy |
| model-cards-oncology-bias-005 | Bias & subgroup reporting standard (required breakdowns + gap statements) | design-spec | small | medium | document | schema-001 | Data-License+Privacy, Technical |
| model-cards-oncology-validator-006 | Schema validator + Hugging Face model-card metadata emitter | code | medium | low | pr | schema-001 | Technical |
| model-cards-oncology-expert-007 | Identify/secure oncology expert + patient-advocate reviewers (for high-risk layer) | research | small | low | document | — | Maintainer |
| model-cards-oncology-outreach-008 | Model-maintainer / hub / consortium outreach + candidate model shortlist | research | small | low | document | — | Maintainer |
| model-cards-oncology-pilot-009 | End-to-end model card for one pilot open cancer model | writing | medium | medium | document | schema-001, gate-003, ncpolicy-004, bias-005, validator-006, reviewer-002, outreach-008 | Data-License+Privacy, Technical |

**Acceptance criteria — key tasks**

- **schema-001 (canonical schema + template)**
  - [ ] Canonical model-card schema documents every field in PLAN's solution section (identity,
        versioning, provenance, model + dataset licenses with `permitsDerivatives`/`permitsUse`,
        access tier, de-identification, intended use, `outOfScopeUse[]`, training/eval data,
        headline + subgroup `metrics` with a `verified` enum, calibration, limitations, failure
        modes, clinical-validation status, regulatory status, ethical considerations, disclaimer,
        `citations[]` provenance index, `specVersions`, `completenessScore`).
  - [ ] Markdown + YAML front-matter template projects cleanly to the Hugging Face model-card format.
  - [ ] Template hard-codes a mandatory **research-use-only / not-medical-advice** disclaimer block
        and a required `outOfScopeUse` section; states output is licensed CC-BY-4.0.
  - [ ] Integrates Healthsheet (data) + TRIPOD+AI/CLAIM (reporting) items, with `specVersions` pinned.
  - [ ] At least one filled-in worked-example skeleton (synthetic model) included.
  - [ ] `pnpm build && pnpm test && pnpm lint` pass for any committed tooling; commit DCO signed-off.

- **gate-003 (model + cancer-data gate)**
  - [ ] Checklist enforces the binding cancer guardrails: **open-access / aggregate / de-identified
        data only**; controlled-access (dbGaP, EGA, individual-level biobanks) and identifiable data
        are auto-EXCLUDE (or stub-only for models trained on them).
  - [ ] Objective license criterion: PASS only if model `permitsDerivatives:true` **and** every
        dataset `permitsUse:true`, each from a cited clause/URL; missing/unparseable = FLAG/EXCLUDE
        (no default-allow). Defers NC/share-alike to `ncpolicy-004`.
  - [ ] Requires recording each license id + URL + snapshot reference (committed copy + SHA-256 +
        Wayback), the access tier, and the de-identification method **as documented by the provider**
        (we never de-identify).
  - [ ] Encodes the model & eval-data access protocol (text artifacts primary; reproduce only on open
        de-identified eval data; local/ephemeral; commit aggregates only; halt on access barrier/PII).
  - [ ] Produces a committed PASS/FLAG/EXCLUDE artifact per model recording which checks ran and what
        fired.

- **bias-005 (bias & subgroup reporting standard)**
  - [ ] Enumerates required subgroup dimensions where available (site/scanner, sex, age band,
        ancestry/race when ethically reported) and the headline + calibration metrics expected.
  - [ ] Mandates that every card either reports subgroup metrics **or** carries an explicit, sourced
        "subgroup performance not reported" gap statement — no silent omission.
  - [ ] Defines the `verified` enum (`reported | reproduced | reported-unverified | not-reported`) and
        when reproduction (via the harness) is required vs. citing the authors' numbers.

- **pilot-009 (pilot model card, end-to-end)**
  - [ ] Pilot selected on a realistic acceptance path first: an informal channel (author agreed to
        review a model-card PR) or a self-serve fallback (Zenodo metadata record/DOI we publish) so a
        real *accepted* outcome is reachable.
  - [ ] Model + every dataset passed `gate-003` (open/de-identified; license permits use) with the
        committed gate artifact; controlled-access/identifiable data confirmed absent.
  - [ ] Complete card produced: intended + out-of-scope use, data provenance, headline + subgroup
        metrics (reported or gap-flagged), limitations, clinical-validation + regulatory status, and
        the not-medical-advice disclaimer; **every claim cited** in `citations[]`; completeness
        before/after recorded (after ≥ 90/100).
  - [ ] Provenance + license snapshots recorded (committed copy + SHA-256 + Wayback) for model and
        each dataset.
  - [ ] Card **accepted** via the informal channel or self-serve fallback with the Steward's
        `outcomes/<model-id>.json` acceptance artifact recorded — or **submitted** with the blocker
        surfaced.

**M0 Definition of Done:** Data-License+Privacy reviewer named and filled (blocking role before the
pilot review); oncology + advocate reviewers identified; canonical schema + template + model/data
gate + NC policy + bias standard published (NC policy merged before any triage); schema validator +
HF emitter green in CI with golden fixtures; one pilot model card produced end-to-end and **accepted**
via informal channel or Zenodo self-serve fallback (evidence artifact recorded) — or submitted with
the blocker surfaced; ≥ 1 maintainer-outreach thread opened.

---

## Milestone M1 — Gate hardened + first acceptances + candidate catalog

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-snapshot-010 | License + data-provenance snapshot capture in card flow | code | small | low | pr | schema-001, gate-003 | Technical |
| model-cards-oncology-catalog-011 | Build `MODEL-CATALOG.md` (license/data-classified candidate models) | research | medium | medium | document | gate-003, ncpolicy-004 | Data-License+Privacy |
| model-cards-oncology-triage-012 | Triage 5 candidate models through the gate | research | medium | medium | document | gate-003, ncpolicy-004, catalog-011 | Data-License+Privacy |
| model-cards-oncology-card-013 | Model card for accepted model #2 | writing | medium | medium | document | pilot-009, triage-012 | Data-License+Privacy, Technical |
| model-cards-oncology-card-014 | Model card for accepted model #3 | writing | medium | medium | document | pilot-009, triage-012 | Data-License+Privacy, Technical |
| model-cards-oncology-partner-015 | Secure first confirmed model/consortium/hub partner | research | small | low | document | outreach-008 | Steward |

**Acceptance criteria — key tasks**

- **snapshot-010 (license + provenance snapshot capture)**
  - [ ] Implements the decided format: committed local copy of the license text/page + SHA-256 hash +
        Wayback save URL for both the model artifact and each dataset; `snapshotRef` records committed
        path + hash + Wayback timestamp. A bare license URL is insufficient.
  - [ ] Code MIT-licensed; tests + CI green; no credentials embedded.

- **catalog-011 (candidate catalog)**
  - [ ] ≥ 15 real open cancer ML models classified with columns: modality, weights URL, model
        license, training-data sources + openness/access tier, PII risk, first-pass disposition.
  - [ ] Each entry's columns are first-pass triage signals only — no entry is pre-approved; each still
        requires its own `gate-003` artifact before becoming a card task.
  - [ ] Models depending on controlled-access data are marked EXCLUDE/stub-only with the reason.

- **triage-012 (triage 5 candidates)**
  - [ ] Five models evaluated with a committed gate artifact each (PASS/FLAG/EXCLUDE + rationale),
        applying `ncpolicy-004`.
  - [ ] Each PASS records model + dataset license ids/URLs/snapshots and `permitsDerivatives`/
        `permitsUse = true` with cited evidence; data confirmed open/de-identified.
  - [ ] Any controlled-access/identifiable data, NC-conflict, or unclear license → FLAG/EXCLUDE with
        the concern surfaced.

- **partner-015 (first confirmed partner)**
  - [ ] A named model maintainer/hub/consortium confirms they will review and accept contributed cards.
  - [ ] Contribution mechanism documented (HF model-card PR / repo PR / Zenodo / portal).
  - [ ] That partner's tasks updated to `verifiedNeed: true` with `requestor` set.

**M1 Definition of Done:** gate applied to ≥ 5 models with committed artifacts; `MODEL-CATALOG.md`
created (≥ 15 classified candidates); ≥ 2 cards accepted onto their host (per-channel evidence
artifacts recorded); ≥ 1 confirmed partner; license + data snapshot capture working (committed copy +
SHA-256 + Wayback).

---

## Milestone M2 — Tooling & scale

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-mct-016 | Model Card Toolkit JSON + Croissant eval-dataset adapter | code | medium | low | pr | validator-006 | Technical |
| model-cards-oncology-harness-017 | Reproducible subgroup-metric harness (open de-identified eval data only) | code | medium | medium | pr | bias-005, validator-006 | Technical, Data-License+Privacy |
| model-cards-oncology-scale-018 | Model cards for models #4–#6 via tooling | writing | large | medium | document | card-013, mct-016, harness-017, partner-015 | Data-License+Privacy, Technical |

**Acceptance criteria — key tasks**

- **mct-016 (Model Card Toolkit + Croissant adapter)**
  - [ ] Emits valid Google Model Card Toolkit JSON (pinned release) and Croissant ML v1.0 metadata for
        the model's open eval datasets; versions recorded in `specVersions`.
  - [ ] Ships committed golden canonical→output fixtures diffed in CI; output validated against the
        pinned specs. Code MIT; CI green; no credentials.

- **harness-017 (subgroup-metric harness)**
  - [ ] Recomputes reported metrics **only** on open-access/de-identified eval data via a pinned,
        containerized environment (hash recorded); never touches controlled-access/identifiable data.
  - [ ] Reproduces a committed **synthetic** eval fixture with known ground-truth metrics exactly
        (CI); commits only aggregate metrics + provenance pointers, never patient images/rows.
  - [ ] Verifies ≥ 1 real delivered card's subgroup metric; results written back with
        `verified: reproduced`. Code MIT; CI green.

- **scale-018 (models #4–#6)**
  - [ ] All three models gate-passed with committed artifacts; cards emitted via the adapters and
        accepted upstream, with per-channel acceptance evidence artifacts recorded.
  - [ ] Per-model effort recorded (AI-session minutes + human-review cycles) to show reduction vs. the
        recorded M0/M1 baseline median.

**M2 Definition of Done:** Model Card Toolkit + Croissant adapter shipped (golden fixtures + pinned
specs); subgroup harness shipped (synthetic fixture reproduced + ≥ 1 real card verified); ≥ 5 cards
accepted cumulatively; measurable median per-model effort reduction vs. the M0/M1 baseline.

---

## Milestone M3 — Patient-facing layer (high-risk) + reuse outcomes & sustainability

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-plainlang-019 | Plain-language "what this model is / is not" summary (education-only) | writing | medium | high | document | card-013, expert-007 | Oncology expert, Patient advocate, Data-License+Privacy |
| model-cards-oncology-reuse-020 | Track and verify external reuse/improvement events | research | small | low | document | card-013, card-014, scale-018 | Steward |
| model-cards-oncology-refresh-021 | Staleness/refresh + model-version drift process | maintenance | small | low | document | validator-006 | Maintainer |

**Acceptance criteria — key tasks**

- **plainlang-019 (patient-facing summary — HIGH RISK)**
  - [ ] Derived only from an already-accepted technical card; states in plain language what the model
        does, what it is **not** for, and its key limitations/biases.
  - [ ] Prominent **"not medical advice"** + research-use-only framing; no diagnostic/prognostic/
        treatment guidance; directs readers to qualified clinicians.
  - [ ] **Credentialed oncologist sign-off AND patient-advocate sign-off recorded** before
        publication (both mandatory; zero ungated patient-facing output). `riskTier: high`.
  - [ ] Every factual claim traces to the parent technical card's `citations[]`.

- **reuse-020 (reuse/improvement tracking)**
  - [ ] ≥ 3 externally verifiable reuse/improvement events recorded (citation / merged model-card PR /
        upstream author acknowledging a documented limitation), with evidence links — no self-reported reuse.

**M3 Definition of Done:** ≥ 1 patient-facing summary shipped with oncologist + advocate sign-off and
"not medical advice" framing (zero ungated); ≥ 3 verifiable reuse/improvement events; ≥ 6 cards
accepted cumulatively; staleness/refresh + drift process documented and a steward named.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-zenodo-022 | Zenodo metadata adapter + DOI provenance for self-hosted cards | code | medium | low | pr | Powers the self-serve acceptance fallback |
| model-cards-oncology-citationcff-023 | CITATION.cff emitter from canonical schema | code | small | low | pr | Improves citability of documented models |
| model-cards-oncology-checklist-024 | TRIPOD+AI / CLAIM conformance checklist generator | code | medium | medium | pr | Auto-scores a card against reporting items |
| model-cards-oncology-i18n-025 | Translate a delivered patient-facing summary (domain reviewer) | translation | small | high | translation | Widens reach; needs oncology + language reviewer (stays high) |
| model-cards-oncology-dash-026 | Outcome dashboard for accepted cards + reuse events | code | medium | low | pr | Reads the outcome ledger; supports success metrics |

---

## Generated task index

> Auto-generated by Hee-Lee Oss task-decomposition agent on 2026-06-29. All 26 tasks validated against
> `packages/schema` (validateTask). Seed tasks 001–013 were already present; tasks 014–026 are
> newly generated in this pass. No fan-out dimensions applied (each backlog row maps to exactly
> one task file).

| ID | Title | Type | Size | Risk | Deliverable | Status | outputLicense |
| --- | --- | --- | --- | --- | --- | --- | --- |
| model-cards-oncology-schema-001 | Canonical model-card schema + template | writing | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-reviewer-002 | Name/secure the Data-License+Privacy reviewer | research | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-gate-003 | Model + cancer-data license/de-identification gate | design-spec | small | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-ncpolicy-004 | NC / share-alike acceptance policy | design-spec | small | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-bias-005 | Bias & subgroup reporting standard | design-spec | small | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-validator-006 | Schema validator + Hugging Face model-card metadata emitter | code | medium | low | pr | open | MIT |
| model-cards-oncology-expert-007 | Identify/secure oncology expert + patient-advocate reviewers | research | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-outreach-008 | Model-maintainer / hub / consortium outreach + candidate model shortlist | research | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-pilot-009 | End-to-end model card for one pilot open cancer model | writing | medium | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-snapshot-010 | License + data-provenance snapshot capture in card flow | code | small | low | pr | open | MIT |
| model-cards-oncology-catalog-011 | Build MODEL-CATALOG.md | research | medium | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-triage-012 | Triage 5 candidate models through the gate | research | medium | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-card-013 | Model card for accepted model #2 | writing | medium | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-card-014 | Model card for accepted model #3 | writing | medium | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-partner-015 | Secure first confirmed model/consortium/hub partner | research | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-mct-016 | Model Card Toolkit JSON + Croissant eval-dataset adapter | code | medium | low | pr | open | MIT |
| model-cards-oncology-harness-017 | Reproducible subgroup-metric harness | code | medium | medium | pr | open | MIT |
| model-cards-oncology-scale-018 | Model cards for models #4–#6 via tooling | writing | large | medium | document | open | CC-BY-4.0 |
| model-cards-oncology-plainlang-019 | Plain-language summary (education-only) | writing | medium | high | document | open | CC-BY-4.0 |
| model-cards-oncology-reuse-020 | Track and verify external reuse/improvement events | research | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-refresh-021 | Staleness/refresh + model-version drift process | maintenance | small | low | document | open | CC-BY-4.0 |
| model-cards-oncology-zenodo-022 | Zenodo metadata adapter + DOI provenance | code | medium | low | pr | open | MIT |
| model-cards-oncology-citationcff-023 | CITATION.cff emitter from canonical schema | code | small | low | pr | open | MIT |
| model-cards-oncology-checklist-024 | TRIPOD+AI / CLAIM conformance checklist generator | code | medium | medium | pr | open | MIT |
| model-cards-oncology-i18n-025 | Translate a delivered patient-facing summary | writing | small | high | translation | open | CC-BY-4.0 |
| model-cards-oncology-dash-026 | Outcome dashboard for accepted cards + reuse events | code | medium | low | pr | open | MIT |

**Notes:**
- Tasks 001–013 were seed tasks already present in the repo before this decomposition pass.
- Tasks 014–026 are generated from explicit backlog rows in this TASKS.md; no rows were fabricated.
- `i18n-025` uses `type:writing` + `deliverable:translation` per schema spec (translation is not a
  valid `type` value; the spec maps translation tasks to `type:"writing"`).
- All tasks carry `verifiedNeed:false` and `requestor:"TO BE SECURED"` until a named
  model-maintainer/hub partner is confirmed (see partner-015).
- Patient-facing tasks (`plainlang-019`, `i18n-025`) are `riskTier:high`; both require credentialed
  oncologist sign-off AND patient-advocate (or domain+language reviewer) sign-off in
  acceptanceCriteria, and zero ungated output ships.

---

## Example task JSON

```json
{
  "id": "model-cards-oncology-schema-001",
  "title": "Canonical model-card schema + template (Model Cards + Healthsheet + TRIPOD+AI/CLAIM)",
  "project": "model-cards-oncology",
  "type": "writing",
  "lane": "donated",
  "priority": "high",
  "domain": ["cancer", "oncology", "ml", "model-documentation", "open-science"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "small",
  "status": "open",
  "context": "Open cancer ML models (digital pathology, radiomics, genomics/transcriptomics) are published on Hugging Face, Zenodo, GitHub, and model zoos with little or no structured reporting of intended use, training/eval data provenance, subgroup/bias performance, limitations, or research-use-only status. Before documenting any specific model, the project needs one reusable canonical model-card schema and template that all outputs (Hugging Face model-card YAML, Model Card Toolkit JSON, Croissant eval metadata, patient-facing summary) are projections of. The deliverable is documentation about models, never the model, weights, or any patient data, and never medical advice.",
  "objective": "Create the reusable canonical model-card schema and Markdown+YAML template that every per-model card task will use, integrating Model Cards (Mitchell et al.), Healthsheet, and TRIPOD+AI/CLAIM reporting items, with a mandatory research-use-only/not-medical-advice disclaimer and provenance on every field.",
  "acceptanceCriteria": [
    "Canonical schema documents all fields: id, name, version, authors, modality, task, provenance {paperDoi, weightsUrl, codeUrl, retrievedAt}, modelLicense {id, url, permitsDerivatives, snapshotRef}, intendedUse, outOfScopeUse[], intendedUsers[], trainingData[]/evalData[] {name, source, license {id,url,permitsUse,snapshotRef}, accessTier, deidentification {method, source}, composition, gaps[]}, metrics {headline[], subgroups[] {dimension, group, metric, value, verified, source}}, calibration, limitations[], failureModes[], clinicalValidation, regulatoryStatus, ethicalConsiderations, disclaimer, citations[] {claim, source}, specVersions, completenessScore {before, after}.",
    "Markdown + YAML front-matter template projects cleanly to the Hugging Face model-card format and hard-codes a mandatory research-use-only / not-medical-advice disclaimer block and a required out-of-scope-use section.",
    "Schema integrates Healthsheet (data documentation) and TRIPOD+AI/CLAIM (reporting) items; specVersions block pins Model Cards, HF model-card spec, Croissant v1.0, Model Card Toolkit, TRIPOD+AI, CLAIM, and Healthsheet versions.",
    "Template explicitly states the deliverable is documentation (not the model/data) and that documentation output is licensed CC-BY-4.0; metrics carry a verified enum (reported|reproduced|reported-unverified|not-reported) so numbers are never fabricated.",
    "At least one filled-in worked-example skeleton (synthetic model, no real patient data) is included to guide contributors.",
    "pnpm build && pnpm test && pnpm lint pass for any committed tooling; commit is DCO signed-off."
  ],
  "resources": [
    "C:\\code\\hee-lee-oss\\planning\\projects\\model-cards-oncology\\PLAN.md",
    "C:\\code\\hee-lee-oss\\planning\\ROADMAP.md",
    "Mitchell et al., Model Cards for Model Reporting (2019)",
    "Rostamzadeh et al., Healthsheet; TRIPOD+AI (2024); CLAIM (2024)",
    "Hugging Face model-card spec; Google Model Card Toolkit; Croissant ML v1.0"
  ],
  "output": "A canonical model-card schema definition plus a Markdown+YAML template, committed to the project repo and ready for reuse by per-model card tasks.",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
