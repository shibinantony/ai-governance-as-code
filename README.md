[README.md](https://github.com/user-attachments/files/30225494/README.md)
# ai-governance-as-code

**Policy-as-Code and Governance-as-Code for AI in regulated industries.**

A working library of executable, version-controlled governance controls for BFSI and Pharma AI deployments — mapped clause-by-clause to the regulations that actually get audited.

---

## Why this exists

Enterprise AI conversations still open with *"how do we stop hallucinations?"* That framing is why programmes stall.

The real problem is not hallucination. It is **ungoverned non-determinism reaching regulated decisions** — and the failure mode is not a wrong answer, it is the inability to prove to an examiner why the decision was allowed to happen at all.

Guardrail SDKs (NeMo, Guardrails AI, Bedrock Guardrails, Llama Guard) do useful work at the token layer. They fail the audit for four reasons:

- Policy buried in application code, not readable by compliance
- No independent enforcement point (SR 11-7 will not accept this)
- No immutable evidence trail (21 CFR Part 11 will not accept this either)
- No traceability to a named regulatory clause

This repository is the alternative: **governance controls as code, versioned in Git, enforced at the AI gateway, and mapped to the regulations that examiners actually cite.**

---

## What lives here (as it ships)

| Component | Status | Notes |
|---|---|---|
| Thesis paper | 🟢 v0.1 | ./docs/thesis-v0.1.md |
| BFSI Rego bundle | ⚪ Planned | SR 11-7 · RBI FREE-AI · EU AI Act Annex III · DORA |
| Pharma Rego bundle | ⚪ Planned | 21 CFR Part 11 · EMA-FDA GAIP · GMLP · GAMP 5 |
| Cross-framework crosswalks | ⚪ Planned | NIST AI RMF · ISO/IEC 42001 · EU AI Act · SR 11-7 · RBI FREE-AI · Part 11 |
| Governance-as-Code templates | ⚪ Planned | Model registration, PCCPs, incident records, validation evidence, all as YAML in Git |
| Policy test harness | ⚪ Planned | `opa test` unit tests + CI gates |
| Red-team scenario library | ⚪ Planned | MITRE ATLAS · OWASP LLM Top 10 · OWASP Agentic ASI |

Legend: 🟢 shipped · 🟡 in progress · ⚪ planned.

---

## The wedge

Horizontal AI governance platforms (Credo AI, Holistic AI, watsonx.governance, OneTrust, Fairly, ModelOp) already exist and are well-funded. This project does not compete with them.

The gap they leave — and what this repo fills — is the **regulated-industry policy library**. Deep, opinionated, clause-mapped Rego bundles for BFSI and Pharma that any of those platforms, or a home-grown control plane, can consume.

The value is not in the engine. The value is in the mapping.

---

## Who this is for

- **Heads of Model Risk / AI Governance** who need something an examiner can inspect on Monday.
- **AI platform engineers** wiring OPA, LiteLLM, Bifrost, Databricks Unity Catalog Service Policies, or a home-grown gateway.
- **GRC and Compliance teams** who own the crosswalk between AI systems and SR 11-7 / EU AI Act / 21 CFR Part 11 / ISO 42001.
- **Regulators, auditors, standards contributors** looking for a reference implementation to critique.

---

## Principles

1. **Every runtime policy carries the citation of the regulation it enforces.** No orphaned rules.
2. **Every lifecycle artifact — model registration, validation evidence, change control, incident record — is a signed file in Git.** No spreadsheets pretending to be systems of record.
3. **Crosswalks are generated from policy, not the reverse.** Change a control → mappings regenerate.
4. **Nothing here is legal advice.** These are engineering artifacts. Regulatory interpretation stays with your Legal, Compliance, and Model Risk functions.
5. **Honest about limits.** Semantic detection (PII, prompt injection, toxicity) remains probabilistic. Policy-as-Code makes the *decision* auditable; it does not make the *detection* deterministic.

---

## Roadmap

- **v0.1 — Thesis paper.** The reference architecture, the two policy packs described, honest limitations named.
- **v0.2 — First Rego bundle.** SR 11-7 + EU AI Act Annex III for credit-scoring use cases, with `opa test` coverage.
- **v0.3 — Pharma bundle.** 21 CFR Part 11 + EMA-FDA GAIP for LLM-in-GxP contexts.
- **v0.4 — Crosswalk generator.** YAML → mapping tables → auto-generated compliance evidence.
- **v0.5 — CI-native evidence bundling.** GitHub Actions template that produces an examiner-ready package on every merge.

Versioned publicly. No private roadmap.

---

## License

- **Text and documentation:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- **Code, Rego policies, templates:** [Apache License 2.0](./LICENSE)

Both licenses permit commercial use, modification, and redistribution — including inside enterprise engagements — with attribution.

---

## Contributing

Not accepting pull requests until v0.2. Issues and discussion are open — the hardest problem is not the code, it is the control mapping, and that improves fastest when practitioners disagree in public.

If you work in Model Risk, AI Governance, or Regulated AI Quality and want to critique a mapping, open an issue.

---

## About

Maintained by **Shibin Antony Boban** — Principal AI Architect specialising in AI Governance, Responsible AI, and Red Teaming.

Views and outputs are personal. This project is developed on personal time and infrastructure, published as personal intellectual property, and is not affiliated with any employer or client.

---

*"The next enterprise offering is not another guardrail SDK. It is versioned, executable governance — and the moat is not the engine, it is the control-mapping layer."*
