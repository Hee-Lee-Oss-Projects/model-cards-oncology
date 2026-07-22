# PLAN — model-cards-oncology

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## Executive summary

Open machine-learning models for cancer — digital-pathology classifiers, mutation/biomarker
predictors from H&E images, radiogenomics models, transcriptomic subtype classifiers, survival
models — are increasingly published with downloadable weights on Hugging Face, Zenodo, GitHub,
the BioImage Model Zoo, and MONAI Model Zoo. Most ship with **little or no structured reporting**
of what the model is *for*, what data it was trained and evaluated on, how it performs across
patient subgroups, where it fails, and — critically — that it is **research software, not a
medical device**. A clinician, researcher, or patient advocate who finds such a model usually
cannot tell whether the training data was openly licensed and de-identified, whether the reported
accuracy holds across scanners/sites/ancestry groups, or whether the model has any clinical
validation at all. That documentation gap is a real safety and reproducibility problem.

This project produces standardized, **source-verified model cards** for *open* cancer ML models —
following the Model Cards framework (Mitchell et al., 2019) extended with healthcare-specific
reporting (Healthsheet, TRIPOD+AI, CLAIM, FUTURE-AI) — and contributes them back to the model's
host (Hugging Face model-card PR, Zenodo metadata, GitHub PR to the model repo). Each card
documents: intended use and explicit out-of-scope use, training/evaluation **data provenance with
license and de-identification verification**, performance metrics **with subgroup/bias
breakdowns**, known limitations and failure modes, clinical-validation and regulatory status, and a
prominent **research-use-only / not-medical-advice** disclaimer. **Every assertion in a card is
cited** to the model's paper, weights, eval logs, or repository — no uncited claims.

The **deliverable is documentation about a model, not the model, the data, or any clinical claim.**
We do not retrain, redistribute weights, or access patient data. Risk tier is **medium** for the
technical model cards (factual/licensing/bias judgement) and **high** for any patient-facing
plain-language summary, which is education-only and gated behind **oncologist + patient-advocate
sign-off**. The central, non-negotiable design principle is the **cancer data & license gate**:
a model is only documented if both the model artifact *and* its training/eval data are
openly/appropriately licensed and the data is **open-access, aggregate, or de-identified** —
controlled-access (dbGaP, EGA, individual-level biobanks) and identifiable patient data are
**out of scope** and we never access them.

## Problem & beneficiaries

**Who is helped.**
- **Cancer ML researchers and reusers** who want to build on, benchmark against, or replicate an
  open model and need to know its real intended use, training data, and limitations before trusting
  it. Undocumented models waste research effort and propagate hidden bias.
- **Clinical-AI safety/translational reviewers and journal/reproducibility editors** who need
  structured, checklist-aligned reporting (TRIPOD+AI / CLAIM) to assess a model.
- **Patient advocates and patient-facing educators** who need a trustworthy, plain-language,
  "what this model is and is *not*" explanation (the high-risk layer) — explicitly *not* medical
  advice — to understand AI tools they encounter in the cancer space.
- **The original model authors**, whose work becomes more reusable, more citable, and more
  responsibly framed when it carries a proper card.

**The verified need.** The *general* need is well established: poor and absent model documentation
is widely cited as a barrier to safe reuse of clinical/biomedical ML, and is the motivation behind
the Model Cards, Healthsheet, TRIPOD+AI, and CLAIM efforts. However, the **per-model, per-partner
need is TO BE SECURED**: we have **not** yet confirmed a named model maintainer, consortium, or
hub that has agreed to accept contributed model cards. Until a specific maintainer/hub confirms
acceptance, individual tasks carry `verifiedNeed: false`. This honesty matters because Hee-Lee Oss's bar
is "delivered, not merged" — a card that no one accepts is not a delivered outcome.

**Partner org.** TO BE SECURED. Candidate channels: Hugging Face model authors (model-card PRs),
the BioImage Model Zoo and MONAI Model Zoo maintainers, Grand Challenge organizers, Zenodo record
owners, and cancer-AI consortia / reproducibility initiatives. M0 includes explicit
maintainer-outreach work; no partner is assumed.

## Goals and non-goals

**Goals**
- Produce a reusable, standards-aligned **canonical model-card schema + template** for cancer ML
  models, unifying Model Cards, Healthsheet (dataset documentation), TRIPOD+AI/CLAIM reporting
  items, and FUTURE-AI principles into one source-of-truth object.
- For each in-scope open model, deliver a complete, **source-verified** model card with cited
  intended-use, data provenance, subgroup/bias metrics, limitations, validation/regulatory status,
  and a research-use-only disclaimer.
- Make the **model + cancer-data license/de-identification gate** a non-skippable, auditable step.
- Provide light tooling: a schema validator, a Hugging Face model-card metadata emitter, a Model
  Card Toolkit JSON / Croissant adapter, and (where eval data is open) a reproducible subgroup-metric
  harness so bias breakdowns are *verified*, not copied.
- Produce, only behind oncologist + advocate review, plain-language "what this model is / is not"
  summaries for patient advocates — education-only, marked **not medical advice**.

**Non-goals**
- We do **not** train, fine-tune, retrain, or redistribute model weights, nor host/mirror data.
- We do **not** access controlled-access (dbGaP, EGA) or identifiable patient data — models that
  cannot be documented without such access are flagged and excluded.
- We do **not** make or imply any clinical, diagnostic, prognostic, or treatment recommendation, and
  we do **not** evaluate models for clinical deployment or imply fitness for clinical use.
- We do **not** invent or estimate metrics: if a subgroup metric is not reported and cannot be
  reproduced on open eval data, the card states "not reported" — we never fabricate numbers.
- We do **not** document models whose license forbids the documentation/derivative work, nor
  "launder" a closed/non-commercial model or dataset as open.
- We do **not** auto-publish; a human submits the card after review.

## Success metrics (outcomes)

Outcome-based and beneficiary-centric. Vanity metrics ("cards written") are excluded — only
**accepted** cards and **verified reuse/safety** outcomes count.

| Metric | Baseline | Target (first 6 months) |
| --- | --- | --- |
| Model cards **accepted onto the model's host** (HF PR merged / Zenodo record / repo PR merged / maintainer confirmation) — last-mile delivered | 0 | 6 accepted |
| Models passing the model+data license/de-identification gate / total triaged | n/a | 100% of *documented* models have a recorded, cited license + verified open/de-identified data provenance |
| Cards whose reported metrics include **subgroup/bias breakdowns** (site/scanner, sex, age band, ancestry where available) or an explicit, sourced "subgroup performance not reported" gap statement | n/a | 100% of delivered cards address subgroups (report or flag the gap) |
| Verified **reuse / improvement** signal: card cited, forked, or adopted upstream; or a documented limitation/bias gap acknowledged by the model author | 0 | ≥ 3 with externally verifiable evidence |
| Confirmed model/consortium/hub partners accepting contributions | 0 | ≥ 2 secured |
| License / de-identification / fabricated-metric errors found in review | n/a | **0** license-or-deid errors and **0** fabricated metrics in delivered cards |
| Patient-facing summaries shipped **with** oncologist + advocate sign-off and "not medical advice" framing | 0 | 100% of any patient-facing output (zero ungated) |

Notes on attribution: a "reuse event" must be externally verifiable (a citation, a merged
model-card PR, an upstream issue referencing the card's findings). Self-reported reuse does not
count. The Steward records a canonical **acceptance evidence artifact** per model
(`outcomes/<model-id>.json`: channel, permalink/DOI/merge-commit, timestamp, completeness
before/after, reviewer sign-offs).

**Quantifying "improves documentation" (so DoDs are verifiable).** Each card gets a
**card-completeness score (0–100)**: fraction of canonical-schema fields populated *and*
source-cited (identity, license, training-data provenance + de-id, eval-data provenance,
intended/out-of-scope use, headline + subgroup metrics, limitations/failure modes, clinical-validation
status, regulatory status, disclaimer). Target: every delivered card ≥ **90/100**, versus a
recorded **before-score** captured at triage on the model as-published. Before/after pairs and the
per-card AI-session minutes + human-review cycles are logged by the Steward in the outcome ledger;
M2 compares median effort against the recorded M0/M1 baseline to evidence "effort reduced."

## Scope

**In scope**
- Model-card documentation for **open** cancer ML models: model identity & versioning, provenance
  (paper, weights, code, license), intended use + out-of-scope use, training-data provenance (with
  license + de-identification verification), evaluation-data provenance, headline + **subgroup/bias**
  metrics, calibration/uncertainty where reported, known limitations & failure modes,
  clinical-validation status, regulatory status, ethical considerations, and the research-use-only /
  not-medical-advice disclaimer.
- A canonical model-card schema, a Markdown + YAML model-card template, a schema validator, and
  output adapters (Hugging Face model-card metadata, Google Model Card Toolkit JSON, Croissant for
  eval datasets, optional CITATION.cff).
- A **reproducible subgroup-metric harness** that recomputes reported metrics *only* on
  open-access/de-identified eval data, to verify (not invent) bias breakdowns.
- Model + data **license and de-identification triage** for each candidate model.
- **Patient-facing plain-language summaries** (high-risk, gated) derived from accepted technical
  cards — education-only, oncologist + patient-advocate reviewed, "not medical advice."

**Candidate model pool.** Maintained in a `MODEL-CATALOG.md` (created in M1): a license- and
data-classified catalog of real open cancer ML models across modalities (digital pathology,
radiology/radiomics, genomics/transcriptomics, multi-omics, NLP over open clinical text). Each
entry's license / weights-availability / training-data-openness / PII-risk columns are first-pass
triage signals only; no entry becomes a task until it passes the per-model gate.

**Out of scope**
- The model itself (no training/fine-tuning/redistribution of weights) and the data (no
  hosting/mirroring/processing of patient data).
- **Controlled-access data** (dbGaP, EGA, individual-level biobanks) and any **identifiable patient
  data** — models requiring such access to document or reproduce are flagged/excluded.
- Any **medical advice, diagnosis, prognosis, or treatment guidance**, and any claim or implication
  that a model is fit for clinical use.
- Clinical validation, benchmarking-for-deployment, or head-to-head "which model is best for
  patients" ranking.
- Models whose license forbids documentation/derivatives, and any task primarily serving a
  for-profit entity's proprietary model.
- Automated, unattended publishing to any hub.

## Solution approach & architecture

This is a **content/documentation project with light software** (a schema + template + validator +
adapters + an optional metric-verification harness). It does not move data or run models in
production.

**Pipeline (per model)**
1. **Triage & gate.** Identify the model, its authors, weights/code/license, and its training and
   evaluation datasets. PASS only if (a) the model artifact is openly licensed in a way that permits
   documentation/derivative metadata, **and** (b) every training/eval dataset is open-access,
   aggregate, or de-identified and its license permits the use we make of it. Record the decision as
   a committed gate artifact.
2. **Provenance & claim capture.** For every field, capture the source citation (paper section,
   repo file, weights card, eval log) — provenance on every assertion. License text + data-provenance
   snapshot captured (committed copy + SHA-256 + Wayback URL).
3. **Intended-use authoring.** Intended use, intended users, and **explicit out-of-scope use**
   (e.g. "not validated for clinical decision-making; not for populations/scanners outside the
   training distribution").
4. **Data documentation (Healthsheet-aligned).** Training and eval data: source, license,
   de-identification status & method (as documented by the data provider — we never de-identify),
   cohort composition, known representational gaps.
5. **Metrics & bias.** Headline metrics + **subgroup breakdowns** (site/scanner, sex, age band,
   ancestry/race where ethically reported and available), calibration/uncertainty, and operating
   point. Where a metric is reported but unverifiable, mark "reported, unverified"; where reproducible
   on open eval data, the subgroup-metric harness recomputes it; where absent, state the gap.
6. **Limitations, validation & regulatory status.** Failure modes, distribution-shift risks,
   clinical-validation status (none / retrospective / prospective / external), and regulatory status
   (e.g. "research use only; not FDA/CE cleared").
7. **Disclaimer.** Mandatory research-use-only / not-medical-advice statement on every card.
8. **Review & contribute.** Data-License+Privacy reviewer (hard gate) + ML/technical reviewer; plus
   oncology expert + patient advocate for any patient-facing summary. A human submits the card.

**Canonical model-card schema.** A single internal object per model is the source of truth; all
outputs (HF model-card YAML+Markdown, Model Card Toolkit JSON, Croissant for eval data, plain-language
summary) are *projections* of it. Fields (superset of Model Cards + Healthsheet + TRIPOD+AI/CLAIM):
`id`, `name`, `version`, `authors`, `modality` (pathology|radiology|genomics|transcriptomics|multi-omics|nlp),
`task`, `provenance {paperDoi, weightsUrl, codeUrl, retrievedAt}`,
`modelLicense {id, url, permitsDerivatives:boolean, snapshotRef}`,
`intendedUse`, `outOfScopeUse[]`, `intendedUsers[]`,
`trainingData[] {name, source, license {id,url,permitsUse,snapshotRef}, accessTier (open|aggregate|deidentified|controlled), deidentification {method, source}, composition, gaps[]}`,
`evalData[] {…same shape…}`,
`metrics {headline[], subgroups[] {dimension, group, metric, value, verified (reported|reproduced|reported-unverified|not-reported), source}}`,
`calibration`, `limitations[]`, `failureModes[]`,
`clinicalValidation (none|retrospective-internal|retrospective-external|prospective)`,
`regulatoryStatus`, `ethicalConsiderations`, `disclaimer`,
`citations[] {claim, source}` (provenance index), `specVersions {modelCard, healthsheet, croissant, mctoolkit}`,
`completenessScore {before, after}`.

**Model & eval-data access protocol (makes "describe, never store patient data" enforceable).**
- We read **papers, repos, weights cards, and eval logs** — text artifacts — as the primary source.
- We **never download controlled-access or identifiable patient data.** Where a subgroup metric must
  be *reproduced*, the harness runs **only against the model's already-open, de-identified eval set**
  (e.g. a CC-BY TCIA collection), reads the minimum needed, keeps it local/ephemeral, and commits
  **no** patient images/rows — only the computed aggregate metric and the provenance pointer.
- **Stop on access barrier or PII signal.** If documenting/reproducing requires controlled data, or
  any identifiable data surfaces, work halts and the model routes to EXCLUDE/FLAG.

**Tech stack.** TypeScript, ESM, pnpm workspaces (Hee-Lee Oss convention). Validator and adapters are
small Node packages with minimal deps; the subgroup-metric harness may shell out to a pinned,
containerized Python eval environment (recorded in `specVersions`) since most cancer-ML evals are
Python — kept isolated and reproducible, never embedded in the core. Cards authored in Markdown +
YAML front-matter + JSON/JSON-LD. No runtime service.

**Pinned spec versions** (recorded in `specVersions`, bumped only by a deliberate task):
Model Cards (Mitchell et al., 2019 framework) · Hugging Face model-card spec (current `modelcard`
metadata schema) · Google Model Card Toolkit JSON schema (pinned release) · Croissant ML v1.0 (for
eval-dataset metadata) · TRIPOD+AI (2024) and CLAIM (2024 update) checklists as the reporting-item
source · Healthsheet questionnaire · SPDX license identifiers.

**Key decisions.**
- Canonical-schema-first so we never hand-maintain parallel formats.
- The model+data gate is a **blocking** committed artifact, not an informal judgement.
- Bias breakdowns are **verified where possible** (reproduced on open eval data) and otherwise
  honestly flagged "reported-unverified" or "not-reported" — never fabricated.
- Patient-facing output is a separate, **high-risk**, expert-gated deliverable, kept out of M0/M1.

## Data, licensing & compliance

**This is the critical section, and it leads with the binding cancer guardrails.**

**Cancer data guardrails (binding, non-negotiable).**
1. **Open-access / aggregate / de-identified data only.** We document a model only if its training
   and evaluation data are open-access, aggregate, or de-identified. **Controlled-access data
   (dbGaP, EGA, individual-level biobanks) and any identifiable patient data are out of scope** and
   are never accessed by this project. A model that cannot be meaningfully documented without such
   access is **flagged and excluded** (we may still record a stub noting "trained on controlled-access
   data — not documentable under our guardrails").
2. **Per-source license verification.** Every dataset a model touches is license-checked
   individually. Examples of how sources are treated:
   - **Open TCGA/TARGET genomic *summary* data and de-identified imaging on TCIA, and CPTAC imaging**
     — generally usable; many TCIA collections are **CC-BY 3.0/4.0** (verify per collection;
     attribution required). The **controlled-access** TCGA/TARGET tiers (raw germline, aligned BAMs
     via dbGaP) are **out of scope**.
   - **COSMIC** and **OncoKB** — **non-commercial** licenses requiring registration/agreement →
     governed by the NC/share-alike policy (task `ncpolicy-004`); a model relying on them can be
     documented only if our derivative documentation is compatible with the NC terms, with rationale
     recorded; otherwise flagged.
   - **cBioPortal / open processed cohorts** — verify per dataset (some ODbL/share-alike); apply the
     policy.
   - NCI/TCGA/CPTAC **publication and data-use guidelines** are honored (attribution, no
     re-identification, no implying endorsement).
3. **We never de-identify or re-identify.** De-identification status is *reported from the data
   provider's documentation*; if a provider has not documented adequate de-identification, the model
   is flagged/excluded. Any re-identification attempt is a hard refusal.
4. **Provenance on every assertion.** Every claim in a card carries a citation to its source; the
   `citations[]` index is part of the committed deliverable. Uncited claims fail review.

**Model-artifact licenses we accept** (must permit documentation/derivative metadata): permissive
code/weight licenses (MIT, Apache-2.0, BSD), CC0/CC-BY for weights/cards, and OpenRAIL-style
licenses **provided** their use restrictions do not conflict with producing open documentation.
"All rights reserved," no-derivatives, or evaluation-only weight licenses → **flagged/excluded**.
An **objective acceptance criterion** governs both model and data: PASS only if
`permitsDerivatives:true` (model) / `permitsUse:true` (each dataset) is recorded **with a cited
clause/URL**; missing/unparseable evidence = FLAG/EXCLUDE, never default-allow.

**Privacy/PII stance.** The dominant privacy concern is *upstream* patient data inside training/eval
sets. Handled by the gate: open/de-identified only; controlled-access/identifiable excluded; we do
not download, store, or process patient-level data; the subgroup harness touches only already-open,
de-identified eval data and commits only aggregate numbers. No secrets/tokens/data in logs,
receipts, or commits (Hee-Lee Oss rule).

**Attribution & output license.** Every card attributes the model authors and each dataset per its
license, links to originals, and states that **the card — not the model or data — is our
contribution.** Documentation output is licensed **CC-BY-4.0**; validator/adapter/harness **code is
MIT**. Where a documented dataset is NC, the card's reuse of that dataset's *facts* is framed to
respect the NC terms (decided in `ncpolicy-004`).

## Quality, review & risk gates

**Risk tier: medium** for technical model cards (factual accuracy, license/de-identification
judgement, bias reporting); **high** for any patient-facing plain-language summary, which is
education-only and requires **credentialed oncologist + patient-advocate sign-off before
publication** (per the good-deed definition's high tier). No patient-facing output ships ungated.

**Required review before a deed is "done":**
- **Data-License + Privacy reviewer** (mandatory, every model; hard gate): confirms the model and
  *every* dataset are appropriately licensed and that all data is open/aggregate/de-identified with
  no controlled-access/identifiable data touched. Non-skippable.
- **ML / technical reviewer** (mandatory, every model): confirms metrics, subgroup breakdowns,
  calibration, intended/out-of-scope use, and limitations are accurate and cited; verifies the
  subgroup harness output (CI green) where used; confirms no fabricated numbers.
- **Oncology expert reviewer + patient advocate** (mandatory for patient-facing summaries; advisory
  for clinically-oriented technical cards): confirms clinical framing is correct and that the
  "not medical advice" + research-use-only framing is prominent and adequate.

**Test fixtures & golden files (so "CI green" means something).** Synthetic/public fixtures only —
never real patient data:
- **Schema validator** — golden valid cards that must pass + malformed cards that must fail.
- **Adapters (HF YAML / Model Card Toolkit JSON / Croissant)** — golden canonical→output pairs
  diffed in CI; output validated against the pinned spec.
- **Subgroup-metric harness** — a tiny synthetic eval fixture with known ground-truth metrics that
  the harness must reproduce exactly; pinned containerized environment hash recorded.

**Definition of Shipped.** A model card **accepted onto the model's host** (merged HF model-card PR
/ merged repo PR / published Zenodo record / written maintainer confirmation) with: every claim
cited; model + all datasets gate-passed (open/de-identified, license permits use) with the committed
gate artifact; subgroups reported or the gap explicitly flagged; the research-use-only disclaimer
present; completeness ≥ 90/100; and the Steward's `outcomes/<model-id>.json` acceptance artifact
recorded. For patient-facing summaries, Shipped additionally requires recorded oncologist +
advocate sign-off. Producing the card is *not* shipped; recorded acceptance is.

## Roadmap & milestones

**M0 — Foundation & cold-start (thin)**
- Goal: build the canonical schema, template, gate, and validator; prove the end-to-end flow on one
  pilot model; secure the blocking reviewer role and begin maintainer outreach.
- **Cold-start de-risking.** To avoid producing a card nothing accepts, the pilot is gated on a
  realistic acceptance path *before* authoring, in priority order: (a) an **informal channel** — a
  model author who has agreed to review a model-card PR; failing that, (b) a **self-serve fallback**
  where acceptance does not depend on a third party — a **Zenodo metadata record/DOI we publish**
  for an openly-licensed model, or a card we host that is referenceable. The pilot must use one of
  these so M0 yields a real *accepted* outcome.
- Exit criteria: (1) canonical model-card schema + template published; (2) schema validator + HF
  model-card emitter green in CI with golden fixtures; (3) the **model+data license/de-identification
  gate** (incl. the NC/share-alike policy) exists and is applied to one model; (4) the bias/subgroup
  reporting standard published; (5) one model fully documented end-to-end and **accepted** via an
  informal channel or self-serve fallback (acceptance artifact recorded) — or **submitted** with the
  blocker surfaced; (6) Data-License+Privacy reviewer **named and filled**; oncology + advocate
  reviewers identified (needed before any high-risk task); (7) ≥ 1 maintainer-outreach thread opened.

**M1 — Gate hardened + first acceptances + candidate catalog**
- Goal: make the gate rigorous, build the candidate catalog, and get real cards accepted.
- Exit criteria: (1) gate codified as a reviewable artifact and applied to ≥ 5 models; (2)
  `MODEL-CATALOG.md` created (≥ 15 license/data-classified candidate models); (3) ≥ 2 model cards
  **accepted** onto their host; (4) ≥ 1 confirmed model/consortium/hub partner; (5) license + data
  provenance snapshot capture working (committed copy + SHA-256 + Wayback).

**M2 — Tooling & scale**
- Goal: reduce per-model effort and *verify* bias breakdowns.
- Exit criteria: (1) Model Card Toolkit JSON + Croissant eval-dataset adapter shipped, validated
  against pinned specs; (2) reproducible subgroup-metric harness shipped, reproducing a synthetic
  fixture and verifying ≥ 1 real card's subgroup metric on open eval data; (3) ≥ 5 cards accepted
  cumulatively; (4) median per-model effort measurably reduced vs. the recorded M0/M1 baseline.

**M3 — Patient-facing layer (high-risk) + reuse outcomes + sustainability**
- Goal: ship gated plain-language summaries, demonstrate reuse, and establish maintenance.
- Exit criteria: (1) ≥ 1 patient-facing plain-language summary shipped **with** oncologist +
  advocate sign-off and "not medical advice" framing (zero ungated patient-facing output); (2) ≥ 3
  verifiable external reuse/improvement events; (3) ≥ 6 cards accepted cumulatively; (4) documented
  staleness/refresh + model-version drift process and a named steward for ongoing host liaison.

Dependencies: M1 depends on the M0 toolkit + gate; M2 tooling depends on M1's canonical schema and a
body of cards; M3's high-risk layer depends on accepted technical cards and the secured expert +
advocate reviewers.

## Work breakdown

The itemized, schema-mapped backlog lives in `TASKS.md`, organized by the milestones above. Each
milestone has a task table (`ID | Title | Type | Size | Risk | Deliverable | Depends on |
Reviewer`), acceptance criteria for the most important tasks, and a Definition of Done. A backlog of
sized-but-unscheduled tasks and one complete, schema-valid example Task JSON are included there. The
candidate model pool that feeds per-model card tasks is the `MODEL-CATALOG.md` created in M1; each
catalog entry becomes a per-model card task only after passing the per-model gate.

## Governance, roles & stakeholders

- **Maintainer (Owner):** TBD — owns the schema, gate, tooling, triage, and backlog.
- **Data-License + Privacy reviewer:** TBD (name TO BE SECURED) — mandatory, **non-skippable**
  gatekeeper for license + open/de-identified-data verification; no card ships without this sign-off.
  Must be **filled before the M0 pilot is reviewed** (blocking prerequisite). May rotate among ≥ 2
  qualified reviewers, but ≥ 1 named qualified reviewer must always exist or triage halts. Until
  named, all tasks remain `verifiedNeed:false` and no model passes the gate.
- **ML / technical reviewer(s):** rotation verifying metrics, subgroup harness output, and tooling
  (CI green).
- **Oncology expert reviewer + patient advocate:** credentialed oncologist + a patient advocate —
  **mandatory sign-off** for every patient-facing (high-risk) summary; advisory for clinically
  oriented technical cards. Must be secured before any high-risk task starts (M0 identifies them).
- **Steward (last-mile owner):** TBD — owns host/maintainer relationships, confirms acceptance, and
  records the outcome ledger (the "delivered" signal).
- **Partner / requestor:** TO BE SECURED — named model maintainer(s), hub, or consortium.

## Dependencies & integrations

- **Standards/specs (pinned — see Tech stack):** Model Cards (Mitchell et al., 2019), Hugging Face
  model-card spec, Google Model Card Toolkit, Croissant ML v1.0, TRIPOD+AI (2024), CLAIM (2024),
  Healthsheet, FUTURE-AI, SPDX. Versions recorded in `specVersions`, bumped only by a deliberate task.
- **External hosts/registries (output-only):** Hugging Face Hub, Zenodo, GitHub, BioImage Model
  Zoo, MONAI Model Zoo, Grand Challenge. No automated upload.
- **Data sources (verified per-source, read-only, open/de-identified only):** GDC/TCGA-TARGET open
  tiers, TCIA (per-collection license), CPTAC imaging, cBioPortal, COSMIC/OncoKB (NC — policy-gated).
  Controlled-access sources (dbGaP, EGA) are **excluded dependencies** — explicitly out of scope.
- **Models:** specific open cancer ML models — TO BE SELECTED via triage; none assumed in scope yet.
- **Hee-Lee Oss pieces:** Task JSON schema (`packages/schema`), donated-lane CLI workspace/PR flow
  (`packages/cli`), good-deed definition + refusal guardrails. No funded-lane/runner dependency.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Documenting a model trained on controlled-access/identifiable data (guardrail breach) | Medium | High | Cancer data gate as blocking pre-step; exclude unless every dataset is open/aggregate/de-identified; never access controlled data | Data-License+Privacy reviewer |
| Misclassifying a model or dataset license (e.g. treating NC/no-derivatives as open) | Medium | High | Mandatory license reviewer; cited `permitsDerivatives/permitsUse` + snapshot; NC/share-alike policy; exclude on doubt | Data-License+Privacy reviewer |
| Fabricating or mis-copying performance/subgroup metrics | Medium | High | Provenance on every assertion; reproduce on open eval data where possible; mark "reported-unverified"/"not-reported"; technical review | ML reviewer |
| Card implies clinical fitness or is read as medical advice | Medium | High | Mandatory research-use-only/not-medical-advice disclaimer; out-of-scope-use section; oncology+advocate review for patient-facing | Oncology expert |
| Underreporting bias because subgroup data isn't available | High | Medium | Require explicit, sourced "subgroup performance not reported" gap statements; never silent omission | ML reviewer |
| No host/maintainer partner → cards produced but never accepted (fails "delivered") | Medium | High | M0 outreach + self-serve fallback (Zenodo); steward role; `verifiedNeed:false` until secured | Steward |
| Model/data versions change, making cards stale | Medium | Medium | Record model version + retrieval date; drift/refresh process (M3); validator flags spec drift | Maintainer |
| Subgroup harness mishandles eval data / leaks patient data | Low | High | Access protocol: open/de-identified eval only, local/ephemeral, commit aggregates only; synthetic CI fixtures | Maintainer |
| Spec drift (HF/Model Card Toolkit/Croissant changes) | Medium | Low | Canonical-schema-first; pinned `specVersions`; isolated version-bump task | Maintainer |
| Re-identification or de-anonymization steering | Low | High | Hard refusal per Hee-Lee Oss guardrails; flag and stop; we never de-/re-identify | Data-License+Privacy reviewer |

## Security & privacy

- **Threat surface is small** (no runtime service, no model hosting, no patient-data storage). Main
  surfaces: CI, the published card files, and the optional metric harness.
- **Secrets handling:** validator/adapters need no credentials. Any host API token for a contribution
  is supplied by the submitting human and never written to logs, receipts, or commits.
- **PII / patient data:** dominant concern is *upstream* data in models we document. Handled by the
  cancer data gate (open/de-identified only; controlled-access/identifiable excluded). The subgroup
  harness reads only already-open, de-identified eval data, locally and ephemerally, and commits only
  aggregate metrics — never patient images/rows.
- **Abuse/misuse prevention:** refuse and flag any task that would re-identify patients, launder a
  closed/NC model or dataset as open, fabricate metrics, produce medical advice, or imply clinical
  fitness. Cards remain descriptive, cited, and research-framed.

## Sustainability & maintenance

- **Post-delivery ownership:** the steward maintains host/maintainer relationships; the maintainer
  keeps the schema, validator, adapters, and harness current with spec/host changes.
- **Refresh:** model-version drift is tracked (recorded version + retrieval date); when a model is
  updated, the card becomes a `maintenance` task. The validator flags spec drift.
- **Outcome tracking:** the steward records acceptances, sign-offs, and verified reuse/improvement
  signals against the success metrics, reviewed each milestone.

## Open questions

- Which model maintainer / hub / consortium will be the first confirmed partner (Hugging Face
  authors vs. BioImage/MONAI Model Zoo vs. a reproducibility initiative)?
- For NC datasets (COSMIC/OncoKB) underlying a model, does our CC-BY card adequately respect the NC
  terms, or must such models be excluded? (Default: policy-gated, exclude on doubt — `ncpolicy-004`.)
- How much metric *reproduction* (subgroup harness on open eval data) is in scope vs. reporting the
  authors' numbers with provenance? (Default: reproduce where the eval set is open; otherwise cite +
  mark "reported-unverified.")
- What is the threshold for "documentable" when a model's training data is partly controlled-access
  (some open, some dbGaP)? (Default: exclude if core claims depend on the controlled portion.)
- Which credentialed oncologist + patient-advocate reviewers can be secured for the high-risk layer,
  and at what cadence?
- What counts as a sufficiently "verifiable external reuse/improvement event"?

## References

- Hee-Lee Oss work rules — `C:\code\hee-lee-oss\CLAUDE.md`
- Good Deed Definition + risk tiers — `C:\code\hee-lee-oss\docs\good-deed-definition.md`
- Task JSON schema — `C:\code\hee-lee-oss\packages\schema\src\schemas.ts`
- Portfolio roadmap (Track 8 cancer guardrails) — `C:\code\hee-lee-oss\planning\ROADMAP.md`
- Mitchell et al., "Model Cards for Model Reporting" (2019)
- Gebru et al., "Datasheets for Datasets"; Rostamzadeh et al., "Healthsheet"
- TRIPOD+AI (2024); CLAIM — Checklist for AI in Medical Imaging (2024 update); FUTURE-AI; MI-CLAIM
- Hugging Face model-card spec; Google Model Card Toolkit; Croissant ML metadata spec; CITATION.cff
- GDC/TCGA-TARGET data-access tiers; TCIA collection licenses; CPTAC; cBioPortal; COSMIC; OncoKB
- SPDX license list; Creative Commons CC0 / CC-BY 4.0; Apache-2.0 / MIT; OpenRAIL

---

## Appendix A — Improvements applied

The following 25 specific improvements were applied to this plan during drafting and the review pass
(not generic advice — each is a concrete change present in the document above):

1. **Cancer data gate made the lead of Data/licensing & compliance**, stated as four binding
   numbered guardrails (open/de-identified only; per-source license; never de-/re-identify;
   provenance on every assertion) rather than buried prose.
2. **Controlled-access exclusion made explicit and named** (dbGaP, EGA, individual-level biobanks)
   in scope, dependencies, and risks — with the "stub-only for controlled-data models" disposition.
3. **Two-tier risk model encoded**: medium for technical cards, high for patient-facing summaries,
   with the high tier gated behind oncologist + patient-advocate sign-off and kept out of M0/M1.
4. **"No fabricated metrics" elevated to a first-class rule** with a `verified` enum
   (reported|reproduced|reported-unverified|not-reported) on every subgroup metric and a zero-target
   success metric.
5. **Subgroup/bias reporting made mandatory** — every card must report subgroups *or* carry an
   explicit, sourced "subgroup performance not reported" gap statement (no silent omission).
6. **Reproducible subgroup-metric harness added** so bias breakdowns are *verified* on open eval
   data rather than copied — with a synthetic CI fixture proving correctness.
7. **Objective license acceptance criterion** added for both model (`permitsDerivatives:true`) and
   each dataset (`permitsUse:true`), each requiring a cited clause/URL; missing evidence = FLAG, no
   default-allow.
8. **Per-source license treatment spelled out concretely** (open TCGA/TARGET + TCIA CC-BY vs.
   controlled tiers; COSMIC/OncoKB non-commercial; cBioPortal share-alike) instead of generic
   "verify licenses."
9. **NC/share-alike policy promoted to a blocking M0 task (`ncpolicy-004`)** that must be decided
   before triage, so COSMIC/OncoKB-dependent models get a fixed rule, not ad-hoc judgement.
10. **Model & eval-data access protocol added** (text artifacts primary; reproduce only on open
    de-identified eval data; local/ephemeral; commit aggregates only; stop on access barrier/PII).
11. **Provenance-on-every-assertion operationalized** via a `citations[]` index field in the schema
    and an acceptance criterion that uncited claims fail review.
12. **License + data-provenance snapshot format pinned** (committed copy + SHA-256 + Wayback URL) and
    given its own M1 task, mirroring the proven open-data-datasheets pattern.
13. **Cold-start de-risking with a self-serve fallback (Zenodo DOI)** so M0 can yield a genuinely
    *accepted* outcome rather than "submitted, pending."
14. **Definition of Shipped = host-accepted, not produced**, with a per-channel acceptance evidence
    artifact (`outcomes/<model-id>.json`) the Steward must record.
15. **Card-completeness score (0–100) with before/after** added to make "improves documentation"
    measurable, target ≥ 90, baseline captured at triage.
16. **Effort-reduction metric defined** (AI-session minutes + human-review cycles, median vs. M0/M1
    baseline) so the M2 "effort reduced" DoD is verifiable.
17. **Healthcare reporting standards integrated** (Healthsheet, TRIPOD+AI, CLAIM, FUTURE-AI) into the
    canonical schema, not just the generic Model Cards template.
18. **Mandatory research-use-only / not-medical-advice disclaimer** made a schema field, a pipeline
    step, a review item, and a Definition-of-Shipped requirement.
19. **Out-of-scope-use as a required, distinct field** (not folded into intended use) to prevent
    clinical over-reach.
20. **Blocking-role sequencing made explicit**: the Data-License+Privacy reviewer must be filled
    before the M0 pilot review; oncology+advocate reviewers secured before any high-risk task.
21. **Re-identification refusal added** as a hard guardrail in both Risks and Security & privacy.
22. **Pinned `specVersions` block + isolated version-bump task** so HF/Model Card Toolkit/Croissant
    drift never silently changes output.
23. **Containerized, isolated Python eval environment** for the harness (most cancer-ML evals are
    Python) kept out of the agent-neutral TS core, honoring the architecture rule.
24. **`MODEL-CATALOG.md` funnel + gate filter** model adopted (created in M1) with first-pass triage
    columns that never pre-approve a model — every entry still requires its own gate artifact.
25. **Outcome attribution tightened**: reuse events must be externally verifiable (citation / merged
    PR / upstream acknowledgement); self-reported reuse explicitly excluded.

## Review sign-off

A completeness + correctness review pass was performed against the spec, the schema, and the cancer
guardrails. Findings and resolutions:

- **All 17 required H2 sections present and in order** — verified.
- **Schema conformance** — every task in `TASKS.md` uses only enum-valid values for `type`, `lane`,
  `priority`, `riskTier`, `deliverable`, `tokenEstimate`, `status`; the example Task JSON includes
  all 16 required fields and validates against `packages/schema/src/schemas.ts`. Fixed during review:
  ensured `deliverable` is always one of `pr|dataset|document|translation` (cards = `document`, tools
  = `pr`; we never emit `dataset`).
- **Cancer guardrails leading Data/licensing & compliance** — confirmed; controlled-access exclusion
  and per-source license treatment are concrete, not generic.
- **Risk tiering correct** — patient-facing summary is `high` with oncologist + advocate sign-off;
  technical cards `medium`; foundational template/validator `low`. Verified no high-risk task is
  scheduled before its reviewers are secured.
- **"Delivered, not merged" honored** — `verifiedNeed:false` throughout (no partner secured),
  Definition of Shipped = host acceptance with recorded evidence artifact.
- **No fabricated data/metrics path** — the `verified` enum + "not-reported" gap statements close the
  underreporting and fabrication risks.
- **Correction applied during review** — added the explicit "stub-only" disposition for models
  trained on controlled-access data (previously only "excluded"), so the catalog can record *why* a
  model is undocumentable rather than silently dropping it.
- **Residual items requiring a human decision** are surfaced in Open questions (first partner; NC
  acceptance for COSMIC/OncoKB-backed models; reproduction scope; partial-controlled-data threshold;
  securing the credentialed reviewers).

Sign-off: plan is internally consistent, schema-aligned, and guardrail-compliant for a Draft v0.1.0.
Blocking before execution: name the Data-License+Privacy reviewer and secure the oncology + advocate
reviewers; secure ≥ 1 host/maintainer partner (or commit to the Zenodo self-serve fallback for M0).
