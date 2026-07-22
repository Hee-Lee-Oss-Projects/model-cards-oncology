# Competitive & Improvement Analysis — `model-cards-oncology`

> Scope: standardized, source-verified model cards for **open** cancer ML models
> (intended use, data provenance, performance, subgroup/bias, limitations), research-use-only.
> Risk: medium (technical cards) / high (patient-facing summaries). Analysis date: 2026-06-29.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong: it leads with the cancer data/license gate, encodes a
`verified` enum to forbid fabricated metrics, mandates subgroup reporting-or-gap-statement,
separates a high-risk patient-facing tier, and defines "shipped" as host-accepted (not
produced). Appendix A's 25 applied improvements are concrete, not boilerplate. Below are the
substantive findings.

**Strengths (correct and well-aligned).**
- **Mitchell et al. (2019) coverage is complete.** The canonical schema covers all nine
  original sections — model details, intended use, factors, metrics, eval data, training
  data, quantitative analyses, ethical considerations, caveats — and adds the out-of-scope-use
  field as a distinct required field, which is exactly the lever that prevents clinical
  over-reach. (Mitchell's intended-use section explicitly pairs "primary intended uses/users"
  with "out-of-scope use cases.")
- **Healthcare frameworks are mostly present:** Healthsheet (dataset documentation), TRIPOD+AI
  (BMJ, Apr 2024), CLAIM (imaging checklist), and FUTURE-AI are all named and pinned. The
  research-use-only / not-medical-advice disclaimer is correctly elevated to a schema field, a
  pipeline step, a review gate, and a Definition-of-Shipped requirement — best-in-class.
- **Clinical-vs-research boundary** is handled rigorously: no clinical fitness claims, explicit
  regulatory-status field ("research use only; not FDA/CE cleared"), clinical-validation enum
  (none/retrospective-internal/retrospective-external/prospective). This mirrors the variables
  FDA's June 2024 transparency guidance and the npj Digital Medicine transparency audits look
  for.
- **Dataset provenance and per-source licensing** are concrete (TCGA/TCIA CC-BY vs.
  controlled dbGaP/EGA tiers; COSMIC/OncoKB non-commercial; cBioPortal share-alike), with
  SHA-256 + Wayback snapshots and a `citations[]` provenance index. This is genuinely ahead of
  every general model-card tool.

**Gaps / corrections needed.**

1. **CHAI is entirely absent — the single biggest omission.** The Coalition for Health AI
   Applied Model Card (open-sourced Oct 2024, public schema + JSON schema on GitHub, a Model
   Card Registry, and a "nutrition label" framing) is now the de-facto health-AI model-card
   standard in the US and is referenced in HTI-1 transparency discussions. The plan pins
   Mitchell, HF, Google MCT, Croissant, TRIPOD+AI, CLAIM, Healthsheet, FUTURE-AI, SPDX — but
   never mentions CHAI. At minimum CHAI should be a named, pinned spec and a candidate **output
   adapter** (see §7). Omitting it risks producing cards that procurement/clinical reviewers
   can't map to the standard they're adopting.

2. **MI-CLAIM and DECIDE-AI are missing.** MI-CLAIM (Minimum Information about Clinical AI
   Modeling, Norgeot et al. 2020) is the closest "minimum reporting" analogue to a model card
   for clinical AI and is the framework named in this task brief; DECIDE-AI covers early-stage
   clinical evaluation (17 AI-specific + 10 generic items). Neither appears. They should at
   least be cross-walked even if not adopted wholesale.

3. **The model-artifact license gate is over-conservative and conceptually conflated.** The
   plan's PASS rule requires `permitsDerivatives:true` on the *model artifact*. But writing a
   model card *about* a model is descriptive/nominative documentation — it does not redistribute
   weights or create a derivative of the weights, so it is generally permissible even for
   research-only or no-redistribution weight licenses. Flagship open oncology models illustrate
   the stakes: **UNI** (mahmoodlab) is gated and its terms forbid redistributing/reproducing the
   model; **CONCH** is "academic research only." Under the gate as written, both would be
   FLAG/EXCLUDE — yet these are exactly the high-impact, widely-reused models most in need of
   honest cards. Recommendation: split the gate into (a) *may we publish documentation about
   this model?* (almost always yes) vs. (b) *may we redistribute weights/data?* (we never do
   anyway). Keep `permitsDerivatives` as a *recorded fact about reuse*, not a documentation
   blocker. This materially widens the candidate pool without weakening any guardrail.

4. **Google Model Card Toolkit is a fragile pin.** MCT's `ModelCardGenerator` was moved out to
   TFX Addons and the toolkit has been effectively stale since ~2023. Pinning its JSON schema as
   a first-class output is reasonable for interop but should be marked "legacy/low-priority," with
   CHAI JSON and HF YAML as the primary live targets.

5. **"Keeping current" is under-specified for standards drift, not just model drift.** The plan
   tracks model-version drift well (M3 refresh task) but standards themselves move fast
   (CLAIM 2.0; TRIPOD+AI errata; CHAI versioning; FUTURE-AI best-practice updates). The
   `specVersions` block exists, but the refresh cadence/owner for *spec* drift should be as
   explicit as for model drift.

6. **Fairness/subgroup reporting needs a defined dimension taxonomy.** The plan lists
   site/scanner, sex, age band, ancestry "where available," but doesn't pin *which* taxonomy
   (FUTURE-AI fairness, TRIPOD+AI subgroup items, CHAI fairness metrics). Without a fixed
   schema, "addresses subgroups" is hard to score consistently. Adopt one taxonomy and map the
   others to it.

7. **Minor:** accuracy-of-claims process is solid (cite-or-fail), but there is no explicit rule
   for *conflicting* sources (paper vs. weights card vs. repo report different numbers). Add a
   "discrepancy" disposition to the `verified` enum or a conflict-resolution note field.

---

## 2. Competitive landscape

**A. Original Model Cards (Mitchell et al., 2019).** The foundational nine-section framework;
the conceptual parent of everything here.
- *Strengths:* universal, simple, widely cited; intended-use + out-of-scope is the durable core.
- *Weaknesses:* domain-agnostic — no clinical validation, regulatory status, de-identification,
  or oncology subgroup semantics; voluntary and inconsistently filled.
- https://arxiv.org/abs/1810.03993 · https://www.researchgate.net/publication/330268857

**B. Hugging Face model cards.** The dominant *distribution* surface: Markdown + YAML front
matter, eight-section template, structured `model-index` eval results parsed into Hub widgets.
- *Strengths:* enormous reach, free hosting, PR workflow (the plan's primary delivery channel),
  machine-readable metadata, datasets/metrics/license fields.
- *Weaknesses:* completion is voluntary and famously sparse for biomedical models; no
  health-specific fields (no clinical validation, de-identification, subgroup-by-ancestry, or
  not-a-medical-device disclaimer); no provenance/citation enforcement.
- https://huggingface.co/docs/hub/en/model-cards · https://huggingface.co/docs/hub/en/model-card-annotated

**C. Google Model Card Toolkit (TensorFlow Responsible AI).** Tooling that auto-generates cards
(JSON schema + HTML/Markdown).
- *Strengths:* structured JSON schema, programmatic generation, TFX integration.
- *Weaknesses:* effectively **semi-deprecated** (generator moved to TFX Addons; stale since
  ~2023); TF-centric; generic, not health-aware.
- https://github.com/tensorflow/model-card-toolkit · https://research.google/blog/introducing-the-model-card-toolkit-for-easier-model-transparency-reporting/

**D. CHAI — Coalition for Health AI, Applied Model Card + Registry (2024–25).** The most
directly competitive effort: an open-source, JSON-schema'd "nutrition label" for health AI,
plus a centralized Model Card Registry, aligned to CHAI's five principles (Transparency; Safety;
Security & Privacy; Fairness & Bias; Usefulness) and to ONC HTI-1 transparency expectations.
- *Strengths:* clinical procurement traction, registry/distribution, fairness + intended-use +
  limitations first-class, permissive license, momentum with health systems.
- *Weaknesses:* **aimed at deployed/commercial predictive DSIs**, not open research models;
  developer-self-attested (no independent source-verification or citation index); not
  oncology-specific; assumes a vendor with a deployment context, ground-truth monitoring, and
  local validation that open research models simply don't have.
- https://github.com/coalition-for-health-ai/mc-schema · https://www.chai.org/ ·
  https://www.healthcareitnews.com/news/chai-launches-open-source-healthcare-ai-nutrition-label-model-card ·
  https://www.digitalhealthnews.com/coalition-for-health-ai-unveils-model-card-registry-to-standardize-ai-transparency-in-healthcare

**E. FDA AI/ML transparency guidance (June 2024) + PCCP guidances (Aug 2024–Jan 2025).**
Establishes transparency guiding principles and demographic/performance reporting expectations
for ML-enabled medical devices.
- *Strengths:* authoritative; defines the regulatory-status vocabulary cards should reference.
- *Weaknesses:* applies only to cleared devices; transparency/demographic reporting uptake
  remains low in practice (audits show gaps); not a fill-in artifact for open research models.
- https://www.fda.gov/medical-devices/software-medical-device-samd/artificial-intelligence-software-medical-device ·
  https://www.nature.com/articles/s41746-025-02052-9

**F. Reporting checklists: TRIPOD+AI (2024), CLAIM (2.0), DECIDE-AI, MI-CLAIM, FUTURE-AI.**
Peer-review-oriented reporting standards.
- *Strengths:* rigorous, consensus-backed, subgroup/fairness and validation items; FUTURE-AI's
  six principles (Fairness, Universality, Traceability, Usability, Robustness, Explainability)
  span the lifecycle.
- *Weaknesses:* designed for *manuscripts*, not machine-readable model-host metadata; no tooling,
  registry, or PR workflow; adoption is partial.
- https://www.bmj.com/content/385/bmj-2023-078378 (TRIPOD+AI) ·
  https://arxiv.org/abs/2309.12325 (FUTURE-AI) · https://pmc.ncbi.nlm.nih.gov/articles/PMC11306008/

---

## 3. Gaps we can fill

1. **Open research oncology models are nobody's job.** CHAI/FDA cover *deployed/commercial*
   devices; HF/Google provide *empty* generic templates; checklists target *papers*. The
   specific intersection — **standardized, source-verified cards for open cancer ML models on
   HF/Zenodo/BioImage/MONAI zoos** — is unowned. This is the wedge.
2. **Independent source-verification.** Every competitor is self-attested. A cited, citation-
   indexed, "cite-or-fail" card with a `verified` enum (reported/reproduced/reported-unverified/
   not-reported) is a categorically higher trust artifact.
3. **Subgroup/bias forced-disclosure.** No mainstream template forces an explicit, sourced
   "subgroup performance not reported" gap statement — silence is the norm. Making absence
   *visible* is a real contribution.
4. **Reproduced (not copied) metrics on open eval data.** The subgroup-metric harness verifying
   numbers on CC-BY TCIA/CPTAC eval sets is something no model-card tool does.
5. **Oncology-aware data/license intelligence.** Encoding TCGA/TARGET tier boundaries, TCIA
   per-collection CC-BY, COSMIC/OncoKB NC, dbGaP/EGA exclusion as a reusable gate is unique.
6. **A standards crosswalk.** A single canonical object that projects to Mitchell/HF/CHAI/
   Croissant and maps onto TRIPOD+AI/CLAIM/FUTURE-AI items is a genuinely useful interoperability
   layer no one ships today.

---

## 4. Differentiators to win

1. **Source-verified, citation-indexed cards** (cite-or-fail) vs. everyone else's self-attested
   templates — the defensible quality moat.
2. **Open-model focus** where the regulatory/commercial efforts (CHAI/FDA) structurally don't go.
3. **Oncology data/license gate as a reusable asset** (TCGA/TCIA/COSMIC/dbGaP semantics).
4. **Verified, not copied, subgroup metrics** via the reproducibility harness.
5. **Forced bias-gap disclosure** as a schema-enforced norm.
6. **Canonical-schema-first multi-format projection** (HF + CHAI + MCT + Croissant from one
   object) — interop without parallel hand-maintenance.
7. **"Delivered, not merged" rigor** — host-accepted with an evidence artifact, not vanity counts.

---

## 5. Claude API leverage (and the hard limits)

**Where Claude adds leverage (draft-then-verify):**
1. **First-draft card extraction from papers + repos + weights cards** into the canonical schema,
   with a *required source pointer per field* (paper section / repo file / eval log). Claude is
   strong at locating and structuring scattered claims; emit `citations[]` with every assertion
   so humans verify against source, never against the model's memory.
2. **Limitation & failure-mode enumeration** — prompt Claude to surface distribution-shift risks
   (scanner/stain/site variation, ancestry skew, cohort selection, label noise) from the methods
   text, framed as *candidate* limitations for expert confirmation.
3. **Fairness/subgroup-reporting prompts** — Claude scans the paper for which subgroups *were*
   and *were not* reported and auto-drafts the mandatory "subgroup performance not reported" gap
   statement against a fixed dimension taxonomy (FUTURE-AI fairness + TRIPOD+AI items).
4. **Standards crosswalk + completeness scoring** — map a draft onto Mitchell/CHAI/TRIPOD+AI/
   CLAIM items and compute the before/after 0–100 completeness score.
5. **Plain-language summary drafting** (high-risk tier only) — Claude drafts the "what this model
   is / is not" text, but it ships *only* after oncologist + advocate sign-off.

**Where Claude must NOT decide (hard gates, human/expert only):**
- **No fabricated or estimated numbers** — if a metric isn't in a cited source or reproduced on
  open eval data, the card says "not reported." Claude must never interpolate.
- **License & de-identification determinations are human-verified.** Claude may *surface* a
  candidate SPDX id and clause, but `permitsUse`/`permitsDerivatives`/accessTier are set by the
  Data-License+Privacy reviewer against the cited clause — never auto-accepted.
- **Clinical-grade / fitness-for-use determinations and bias *assessments*** (vs. bias
  *reporting*) require the ML and oncology reviewers; Claude drafts, experts adjudicate.
- **Controlled-access / PII boundary** — Claude must halt and route to FLAG/EXCLUDE on any
  signal of controlled data; it must never attempt de-/re-identification.
- **Acceptance/"delivered"** is a human steward judgement, not model output.

---

## 6. Ten concrete optimizations

1. **Add a CHAI Applied Model Card adapter and pin the CHAI schema** as a first-class output
   target (publish to the CHAI Model Card Registry where eligible). Highest-leverage single change.
2. **Refactor the model-license gate** to separate "may we document this?" (almost always yes)
   from "may we redistribute weights/data?" (we never do), so UNI/CONCH-class research-only models
   become documentable. Keep `permitsDerivatives` as recorded metadata, not a blocker.
3. **Pin a single fairness/subgroup dimension taxonomy** (FUTURE-AI fairness mapped to TRIPOD+AI
   items) so "addresses subgroups" is scored consistently.
4. **Add MI-CLAIM and DECIDE-AI crosswalks** to the canonical schema's `specVersions` and item map.
5. **Demote Google MCT to "legacy interop"** and treat HF YAML + CHAI JSON as primary live targets.
6. **Add a conflict/discrepancy disposition** to the `verified` enum for sources that disagree.
7. **Add a standards-drift refresh task** (owner + cadence) parallel to the model-drift task, so
   CLAIM 2.0 / TRIPOD+AI errata / CHAI versions don't silently stale the cards.
8. **Ship an oncology data/license lookup table** (TCGA tiers, TCIA per-collection CC-BY,
   COSMIC/OncoKB NC, dbGaP/EGA exclusion) as a reusable, versioned reference the gate consumes.
9. **Add a public "card-completeness leaderboard"** (before/after scores across documented models)
   as the verifiable evidence of "improves documentation" and a reuse-attraction surface.
10. **Build an MCP server** exposing the schema, validator, and crosswalk so any agent (Claude or
    other) can draft a compliant, cited card in place (see §7).

---

## 7. Parallel & perpendicular spin-offs

- **`cancer-dataset-datasheets` (parallel, shared spine).** Healthsheet-aligned datasheets for the
  open datasets that feed these models (TCGA open tiers, TCIA collections, CPTAC). The model card's
  `trainingData[]`/`evalData[]` provenance objects are literally pointers into these datasheets —
  build them as one linked graph, not two silos.
- **`ml-oncology-benchmarks` (perpendicular).** Where the card says "reported, unverified," a
  benchmark project can *reproduce* the headline metric; the subgroup-metric harness here is the
  seed. Cards consume benchmark results; benchmarks cite cards for intended-use/limitations.
- **`pathology-image-benchmarks` (perpendicular).** Scanner/stain/site subgroup evaluation for
  digital-pathology FMs (UNI/CONCH-class) — directly feeds the subgroup/bias section that is
  currently the weakest-reported part of every card.
- **CHAI-aligned `health-model-card-generator` (product spin-off).** Generalize the canonical
  schema + Claude draft-then-verify pipeline beyond oncology into a CHAI-conformant generator for
  open health-AI models broadly — a clean wedge into the standard CHAI itself only applies to
  deployed devices.
- **MCP server (`model-card-mcp`).** Expose canonical schema, validator, license/data gate lookup,
  standards crosswalk, and completeness scorer as MCP tools so the human's own agent drafts cited
  cards inline — fits Hee-Lee Oss's donated-lane "human runs their agent" model exactly, with no headless
  invocation.

---

## 8. Open questions

1. **CHAI registry eligibility:** will CHAI accept cards for *open research* models, or is the
   registry deployment-only? If the latter, is a parallel public registry the right home?
2. **The documentation-vs-derivative legal line:** can counsel confirm that publishing a cited
   model card about a research-only-licensed model (e.g. UNI/CONCH) is permissible nominative
   documentation, unblocking the candidate pool (§1.3)?
3. **NC datasets (COSMIC/OncoKB):** does a CC-BY card that merely *reports facts about* an
   NC-trained model respect the NC terms, or must such models be excluded? (`ncpolicy-004`.)
4. **Reproduction scope:** how far does the subgroup harness go before "reproduce" becomes
   "re-benchmark" (which is `ml-oncology-benchmarks`' job)?
5. **Which standard is authoritative on conflict** when Mitchell, CHAI, and TRIPOD+AI disagree on
   a field's required content?
6. **Securing reviewers:** which credentialed oncologist + patient-advocate reviewers, at what
   cadence, gate the high-risk layer — and who is the named Data-License+Privacy reviewer (the
   blocking M0 prerequisite)?

---

### Sources
- Mitchell et al., Model Cards — https://arxiv.org/abs/1810.03993
- Hugging Face model cards — https://huggingface.co/docs/hub/en/model-cards , https://huggingface.co/docs/hub/en/model-card-annotated
- Google Model Card Toolkit — https://github.com/tensorflow/model-card-toolkit
- CHAI Applied Model Card schema — https://github.com/coalition-for-health-ai/mc-schema ; CHAI — https://www.chai.org/ ; registry — https://www.digitalhealthnews.com/coalition-for-health-ai-unveils-model-card-registry-to-standardize-ai-transparency-in-healthcare ; launch — https://www.healthcareitnews.com/news/chai-launches-open-source-healthcare-ai-nutrition-label-model-card
- FDA AI/ML transparency — https://www.fda.gov/medical-devices/software-medical-device-samd/artificial-intelligence-software-medical-device ; transparency audit — https://www.nature.com/articles/s41746-025-02052-9
- TRIPOD+AI — https://www.bmj.com/content/385/bmj-2023-078378 ; 2024 guidelines overview — https://pmc.ncbi.nlm.nih.gov/articles/PMC11306008/
- FUTURE-AI — https://arxiv.org/abs/2309.12325
- UNI — https://github.com/mahmoodlab/UNI ; CONCH — https://github.com/mahmoodlab/CONCH , https://www.nature.com/articles/s41591-024-02856-4
