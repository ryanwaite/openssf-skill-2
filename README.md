# OpenSSF Ensemble — Security Evaluation Skill Set

Use the OpenSSF security tools as an **ensemble**, not a menu. Point it at an
open source project and get back **one decision-ready verdict** — not a pile of
disconnected scores.

> **Two questions, one engine:**
> - **Consume:** *Is this dependency safe to adopt?* → **Adopt / Adopt-with-mitigations / Avoid**
> - **Produce:** *Is the project we build trustworthy and hardened?* → **A–F posture grade + roadmap**

Works with **GitHub Copilot** and **Claude Code**.

This is a re-architecture of [`openssf-skill`](https://github.com/ryanwaite/openssf-skill):
same deep OpenSSF content, restructured so agents run the tools *together* and
fuse the signals into a single verdict.

---

## Why an ensemble?

Most security tooling makes you run Scorecard, then OSV-Scanner, then a secrets
scan, then check signing… and then *you* have to decide what it all means. This
skill set does the fusing for you:

```
        Scorecard ─┐
   OSV / audits ───┤
         SBOM ─────┤      normalize → weight by mode → escalate      ┌─ Adopt / Avoid   (consume)
   signing/SLSA ───┼──►   (live secrets & critical CVEs cap it)  ──► │
       secrets ────┤            ensemble/risk-model.md                └─ A–F grade       (produce)
   maintenance ────┤
  OSPS Baseline ───┘
```

The same tools run in both modes — **the mode only changes the weights**.

---

## Quick start

1. Install (one command — see below).
2. In your project, ask the agent:

   ```
   /openssf evaluate this repo
   /openssf is express@4.18.2 safe for us to adopt?
   /openssf grade our security posture before release
   ```

The agent establishes the mode, runs the ensemble, and returns a verdict with
evidence, ranked risks, and concrete next actions.

---

## Installation

### GitHub Copilot

```bash
mkdir -p .github
curl -o .github/copilot-instructions.md \
  https://raw.githubusercontent.com/ryanwaite/openssf-skill-2/main/.github/copilot-instructions.md
```

Then enable `github.copilot.chat.codeGeneration.useInstructionFiles` in VS Code.

### Claude Code

```bash
git clone https://github.com/ryanwaite/openssf-skill-2.git ~/.claude/skills/openssf
```

Invoke with `/openssf` in any project.

---

## How it works

| Layer | File(s) | Role |
|-------|---------|------|
| **Router** | `SKILL.md`, `.github/copilot-instructions.md` | Sends the request to the right skill |
| **Orchestrator** | `skills/evaluate/` | Establishes mode, runs the battery, produces the report |
| **Modes** | `skills/consume/`, `skills/produce/` | Mode-specific batteries and outputs |
| **Fusion engine** | `ensemble/risk-model.md` | Normalization, mode weights, escalation rules, outcome mapping |
| **Output spec** | `ensemble/report-template.md` | The decision-ready report format |
| **Runner** | `scripts/ensemble-eval.py` | Executes the ensemble, emits fused JSON/Markdown (degrades gracefully) |
| **Detector** | `scripts/assess-project.py` | Local language/artifact detection |
| **Grounding** | `references/` | Deep per-tool guidance the agent draws on |

### Run the ensemble directly

```bash
# Evaluate a dependency you might adopt
python3 scripts/ensemble-eval.py --target requests@2.31.0 --mode consume

# Grade a repo you own
python3 scripts/ensemble-eval.py --target . --mode produce --format md
```

Any tool that isn't installed is reported as `unknown` (and lowers confidence) —
never silently skipped.

---

## The verdict, in short

- **Mode sets the weights.** Consume leans on known CVEs + maintenance; produce
  leans on secrets + provenance + code review.
- **Escalations override the average.** A live secret or an unpatched CRITICAL
  with a fix available caps the verdict no matter how good everything else looks.
- **Unknown ≠ fine.** Missing data lowers confidence; it never inflates the score.
- **Every claim cites a tool. Every verdict shows its math.** Two evaluators
  running the same tools on the same target reach the same verdict.

---

## What's covered

Scorecard · OSV / dependency scanning · SBOM (SPDX/CycloneDX) · SLSA provenance ·
Sigstore/Cosign signing · secrets scanning (Gitleaks/TruffleHog/detect-secrets) ·
OSPS Baseline · STRIDE threat modeling · security code review · container
hardening · OpenSSF Best Practices Badge. See `references/` for depth.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Run the tests with:

```bash
python3 -m unittest discover tests/ -v
```

## License

MIT — see [LICENSE](LICENSE).

## References

- [OpenSSF Best Practices](https://best.openssf.org/)
- [OpenSSF Scorecard](https://scorecard.dev/)
- [SLSA](https://slsa.dev/) · [OSPS Baseline](https://baseline.openssf.org/) · [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- Original project: [`openssf-skill`](https://github.com/ryanwaite/openssf-skill)
