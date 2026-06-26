---
name: openssf
description: |
  OpenSSF security skill set used as an ENSEMBLE. Run multiple OpenSSF tools
  together (Scorecard, OSV/dependency scanning, SBOM, Sigstore/SLSA signing,
  secrets scanning, OSPS Baseline) and fuse their signals into one decision-ready
  verdict for open source projects. Invoke whenever someone wants to know whether
  an open source project is safe to ADOPT/DEPEND ON, or whether a project they
  BUILD is trustworthy and supply-chain-hardened. Routes to the right focused
  skill: evaluate (orchestrator), consume (third-party OSS), produce (your own
  projects), and per-capability references.
---

# OpenSSF Ensemble — Router

This skill set evaluates open source security as an **ensemble**: instead of a
menu of disconnected tools, it runs the relevant battery together and fuses the
results into one verdict. Route the request to the right skill.

## Routing

| If the user wants to… | Go to |
|-----------------------|-------|
| Evaluate any project and get one verdict (don't know mode yet) | **`skills/evaluate`** (orchestrator) |
| Vet third-party OSS they adopt / depend on | **`skills/consume`** → Adopt / Mitigate / Avoid |
| Assess / harden a project they build & ship | **`skills/produce`** → A–F grade + roadmap |
| Deep-dive one capability (Scorecard, SBOM, signing, secrets, SLSA, threat model, …) | the matching folder in **`references/`** |

**Default:** when in doubt, start at `skills/evaluate`. It establishes the mode,
runs the ensemble, and produces the report.

## The model in one breath
1. **Mode** — Consume (don't own it) vs Produce (own it). Mode sets the weights.
2. **Run the battery** — never skip a probe; an un-run probe is `unknown`, not "fine."
3. **Fuse** — normalize → weight → escalate (live secrets / critical CVEs cap the
   verdict) per `ensemble/risk-model.md`.
4. **Report** — one verdict + evidence + ranked risks + actions, per
   `ensemble/report-template.md`.

## Tooling
- `scripts/ensemble-eval.py` — runs the ensemble and emits a fused JSON/Markdown
  verdict (degrades gracefully when a tool isn't installed).
- `scripts/assess-project.py` — local language/artifact detection.

Every conclusion cites the tool behind it. Every verdict shows its math.
