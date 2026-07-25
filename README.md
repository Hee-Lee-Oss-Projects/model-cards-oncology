# model-cards-oncology

> Open machine-learning models for cancer — digital-pathology classifiers, mutation/biomarker predictors from H&E images, radiogenomics models, transcriptomic subtype classifiers, survival models — are   ·  **Risk tier:** med  ·  **Status:** planning

Open machine-learning models for cancer — digital-pathology classifiers, mutation/biomarker predictors from H&E images, radiogenomics models, transcriptomic subtype classifiers, survival models — are increasingly published with downloadable weights on Hugging Face, Zenodo, GitHub, the BioImage Model Zoo, and MONAI Model Zoo. Most ship with **little or no structured reporting** of what the model is *for*, what data it was trained and evaluated on, how it performs across patient subgroups, where it fails, and — critically — that it is **research software, not a medical device**. A clinician, researcher, or patient advocate who finds such a model usually cannot tell whether the training data was openly licensed and de-identified, whether the reported accuracy holds across scanners/sites/ancestry groups, or whether the model has any clinical validation at all. That documentation gap is a real safety and reproducibility problem.

**Definition of shipped:** / merged repo PR / published Zenodo record / written maintainer confirmation) with: every claim cited; model + all datasets gate-passed (open/de-identified, license permits use) with the committed gate artifact; subgroups reported or the gap explicitly flagged; the research-use-o

This is a **Hee-Lee Oss** good-deed project. Contributors pull a task, do it with their own coding agent, and open a PR. Get started: https://github.com/HeeLeeOss/hee-lee-oss-downloads

## Plan
- [PLAN.md](./PLAN.md) — robust enterprise plan (vision, architecture, roadmap, risks; includes an applied-improvements appendix + review sign-off)
- [TASKS.md](./TASKS.md) — schema-mapped task backlog
- [tasks/](./tasks/) — ready-to-pull task JSON(s)

## Contribute
```bash
hee-lee-oss browse
hee-lee-oss next --repo HeeLeeOss/model-cards-oncology --no-fork
```

## Licensing & review
- Open license (see PLAN.md).
- Risk tier **med** — deeds are *delivered, not merged*; a domain reviewer (and expert sign-off for any high-stakes content) must approve before merge.

> Planning stage; no adopting partner secured yet (`verifiedNeed: false` on delivery-dependent tasks).
