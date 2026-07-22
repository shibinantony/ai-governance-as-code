# Beyond Hallucinations: Policy-as-Code & Governance-as-Code as the Next Enterprise AI Offering for BFSI and Pharma

> **A concise IP note on deploying AI safely in the two most regulated industries on earth — and why the credible answer is not another guardrail SDK, but versioned, executable governance.**

**Author:** Shibin Antony Boban — Principal AI Architect, AI Governance & Responsible AI
**Version:** 0.1 — 21 July 2026
**Status:** Working paper · Open for peer review
**License:** CC BY 4.0 (text) · Apache-2.0 (reference code snippets)

---

## TL;DR

1. **Hallucination is a symptom.** The disease is *ungoverned non-determinism* reaching regulated decisions.
2. **Guardrails alone fail the audit.** They stop bad tokens; they do not produce the evidence an examiner needs.
3. **The next enterprise offering is Policy-as-Code (PaC) + Governance-as-Code (GaC)** — controls written as versioned, testable, machine-enforceable artifacts, sitting inside CI/CD and at inference time.
4. **The moat is not the engine (OPA, Rego, LangChain, NeMo, Bedrock Guardrails).** It is the **BFSI/Pharma control-mapping layer** that translates SR 11-7, RBI FREE-AI, EU AI Act, DORA, 21 CFR Part 11, GxP, GMLP and ISO/IEC 42001 into a policy pack a bank or a pharma company can `git clone` on Monday.
5. This paper defines the reference architecture, the two industry policy packs, the commercial packaging, and the honest limitations.

---

## 1. The Problem Framed Correctly

Enterprise AI conversations still open with *"how do we stop hallucinations?"* That framing is why programmes stall.

- Stanford's AI Index 2026 reports hallucination rates across 26 leading LLMs ranging from 22% to 94% depending on task and domain. Even the best models leak fabricated citations, drug interactions, and legal precedents into production.
- The Federal Reserve confirmed in 2023 that AI/ML systems used in bank decisioning fall inside SR 11-7's definition of a "model" — full stop. AI models now account for roughly half of the average large bank's model inventory, yet only **26.4%** of financial institutions express confidence in their AI compliance readiness.
- The EU AI Act's high-risk obligations for credit scoring, fraud, biometrics and consequential decisioning bite from **2 August 2026** (Annex III), with the Digital Omnibus proposal deferring some regulated-product AI to 2 December 2027 and 2 August 2028. Non-compliance carries fines up to **€35M or 7% of global turnover**.
- In pharma, EMA and FDA jointly issued *10 Guiding Principles of Good AI Practice in Drug Development* in **January 2026**, layering on top of GMLP, 21 CFR Part 11, EU Annex 11/22, and ISPE GAMP AI.
- India's RBI released the **FREE-AI Framework** (13 August 2025) with 7 Sutras and 26 recommendations for AI in the financial sector — advisory, but examiner-facing.

The compliance surface has already hardened. The industry is still shipping AI on notebooks. That gap is the enterprise offering.

---

## 2. Why Guardrails Alone Fail the Audit

Guardrail libraries — NeMo Guardrails, Guardrails AI, Bedrock Guardrails, Llama Guard, LangChain output parsers — do useful work: input validation, PII redaction, topic bans, output schema enforcement, prompt-injection blocking.

They fail the enterprise test on four counts:

| Failure mode | What auditors see |
|---|---|
| **Policy buried in code** | A Python `if` block in a repo the compliance team cannot read. No version, no owner, no test. |
| **No independent enforcement point** | Enforcement lives inside the app. Same team writes and evaluates. SR 11-7 explicitly requires independent challenge. |
| **No evidence trail** | Guardrail triggers are logged as app logs, not as immutable, replayable policy decisions. Fails 21 CFR Part 11 authenticity, integrity, audit-trail requirements. |
| **No mapping to regulation** | Guardrail configuration is not traceable to a named subcategory of NIST AI RMF, an ISO/IEC 42001 Annex A control, an SR 11-7 element, or an EU AI Act Article. |

Guardrails answer *"did we block the bad thing?"* Auditors ask *"who decided this was the bad thing, when, on which authority, and where is the evidence it stayed blocked?"* That is a governance question, not a filtering question.

---

## 3. The Thesis — Policy-as-Code + Governance-as-Code

Borrow directly from what already worked in cloud, Kubernetes, and DevSecOps.

- **Policy-as-Code (PaC):** every runtime rule that governs an AI interaction — allowed models per role, PII redaction, jailbreak blocks, tool-call scopes, output schemas, refusal templates, region routing — expressed as declarative code (Rego, YAML compiled to Rego, or an equivalent DSL) evaluated by an engine like Open Policy Agent at the AI gateway.
- **Governance-as-Code (GaC):** every *organisational* control — model registration, risk-tier assignment, validation sign-offs, change control, incident reporting, third-party assessments — expressed as version-controlled artifacts inside CI/CD, with automated compliance checks and audit-ready logging.

The distinction matters:

- **PaC is runtime** — stops the request.
- **GaC is lifecycle** — proves the model should have been in production at all.

An enterprise AI programme needs **both**. Together they close the *policy-to-practice gap* recent research keeps flagging.

---

## 4. Reference Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    GOVERNANCE-AS-CODE (Lifecycle)                    │
│  Model Registry · Risk Tiering · Validation Evidence · Change Ctrl   │
│  Impact Assessments · Third-Party Attestations · Incident Register   │
│  → All as YAML/JSON in Git, gated by CI checks, signed & timestamped │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       POLICY-AS-CODE (Runtime)                       │
│                                                                       │
│  ┌────────────┐   ┌──────────────┐   ┌─────────────┐   ┌──────────┐ │
│  │   User /   │──►│  AI Gateway  │──►│ Policy Dec. │──►│  Model / │ │
│  │   Agent    │   │ (Bifrost /   │   │ Point (OPA) │   │  Tools   │ │
│  │            │   │  LiteLLM /   │   │  Rego rules │   │  RAG     │ │
│  └────────────┘   │  Databricks  │   └─────────────┘   └──────────┘ │
│                   │  UC Service  │           │                       │
│                   │  Policies)   │           ▼                       │
│                   └──────────────┘   ┌─────────────┐                 │
│                          │           │ Immutable   │                 │
│                          └──────────►│ Decision    │                 │
│                                      │ Log (WORM)  │                 │
│                                      └─────────────┘                 │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    CONTROL-MAPPING LAYER  (THE IP)                   │
│  NIST AI RMF · ISO/IEC 42001 · EU AI Act · SR 11-7 · RBI FREE-AI ·   │
│  DORA · 21 CFR Part 11 · GxP · GMLP · EMA-FDA GAIP · MITRE ATLAS     │
└─────────────────────────────────────────────────────────────────────┘
```

Reference substrate — all commodity, all replaceable:

- **Policy engine:** Open Policy Agent (Rego) — CNCF Graduated, battle-tested across Kubernetes, API gateways, microservices.
- **AI gateway enforcement point:** Bifrost, LiteLLM, TrueFoundry, Databricks Unity Catalog Service Policies, or Azure API Management — anywhere every request must pass through.
- **Guardrail primitives:** NeMo Guardrails, Guardrails AI, Llama Guard 4, Bedrock Guardrails — invoked *by* policy, not instead of it.
- **Governance-as-Code substrate:** Git + signed commits + CI (GitHub Actions / Azure DevOps) + evidence store (Azure Purview, Databricks Unity Catalog, or S3 WORM).

**The IP is not any of the above. The IP is the control-mapping layer.**

---

## 5. The BFSI Policy Pack

The BFSI pack turns the following into executable policies and CI gates:

| Regulation / Framework | What the pack encodes |
|---|---|
| **Fed SR 11-7 / OCC 2011-12 / FDIC** | Model inventory schema, tier assignment logic, independent validation gate, backtesting & outcomes-analysis triggers, ongoing-monitoring thresholds. |
| **RBI FREE-AI (7 Sutras, 26 recommendations)** | Board-level AI policy template, incident-reporting form, model-risk register, fairness-monitoring cadence for credit/underwriting/collections. |
| **EU AI Act — Annex III high-risk (credit scoring, fraud, insurance pricing)** | Conformity-assessment checklist, transparency notices, human-oversight controls, log retention, GPAI provider vs. deployer obligations. |
| **DORA (in force 17 Jan 2025)** | ICT third-party register for AI vendors, incident classification & 4-hour reporting flow, resilience testing including AI-specific TLPT scenarios. |
| **MAS FEAT / HKMA / APRA CPS 230** | Regional overlays for APAC deployments — same policy engine, different Rego bundle. |
| **MITRE ATLAS + OWASP LLM Top 10 (2025) + OWASP Agentic ASI (Dec 2025)** | Red-team scenario library, detection rules, technique-to-mitigation mapping. |

**Runtime policies encoded (sample):**

```rego
package bfsi.credit.underwriting

# SR 11-7 §III: use only within approved scope
default allow := false

allow if {
    input.model.tier <= data.role[input.actor.role].max_tier
    input.model.status == "validated"
    input.model.last_validation_days_ago <= 365
    input.request.segment in input.model.approved_segments
    not contains_prohibited_features(input.request.features)
}

# EU AI Act Art. 14 — human oversight for adverse decisions
require_human_review if {
    input.response.decision == "decline"
    input.request.amount >= 25000
}

# RBI FREE-AI Sutra 5 (Fairness) — protected-attribute leakage check
deny["protected attribute in feature set"] if {
    some feat in input.request.features
    feat in data.protected_attributes.in
}
```

**Governance-as-Code artifacts (sample repo layout):**

```
/governance
  /models
    credit-scorecard-v3.4.yaml       # tier, owner, validation evidence, PCCP
  /policies
    bfsi-runtime.rego                # runtime rules
    bfsi-runtime_test.rego           # policy unit tests (CI-gated)
  /assessments
    aia-credit-scorecard-v3.4.md     # EU AI Act Impact Assessment
    fair-lending-test-2026Q2.ipynb   # signed notebook, output frozen
  /incidents
    2026-06-11-drift-alert.yaml      # RBI/DORA-compliant incident record
  /crosswalks
    sr11-7.yaml  eu-ai-act.yaml  rbi-free-ai.yaml  iso42001.yaml
```

Every policy commit is signed; every merge triggers policy tests, control-mapping regeneration, and evidence bundling. That is what "audit-ready" actually means.

---

## 6. The Pharma Policy Pack

Pharma is the harder problem: the same LLM, in a GxP system, must satisfy regulations written before transformers existed.

| Regulation / Framework | What the pack encodes |
|---|---|
| **EMA-FDA 10 Guiding Principles of Good AI Practice (Jan 2026)** | Risk-based validation depth, context-of-use registry, human-centric review gates. |
| **FDA GMLP (10 principles, IMDRF final Jan 2025) + PCCPs** | Data provenance, training/test independence, representative-population checks, Predetermined Change Control Plan template. |
| **21 CFR Part 11 + EU Annex 11 / draft Annex 22** | Authenticity, integrity, confidentiality — every prompt, output, human review and model-version pin logged as an ALCOA+ electronic record. |
| **GAMP 5 (Cat 4/5 for AI) + CSA** | Risk-based validation strategy, IQ/OQ/PQ replaced by intended-use + credibility evidence. |
| **EU AI Act — medical devices & clinical decision support** | Overlays on top of MDR/IVDR; high-risk conformity assessment where AI is safety-relevant. |
| **ICH E6(R3), E9 for AI-touched clinical evidence** | Data integrity chain from source system to submission dossier. |

**Runtime policies encoded (sample):**

```rego
package pharma.gxp.llm

# 21 CFR Part 11 §11.10(d) — attributability
default allow := false

allow if {
    input.actor.authenticated == true
    input.actor.mfa == true
    input.context.gxp_scope in {"GMP", "GLP", "GCP", "GPvP"}
    input.model.version_pinned == true
    input.session.audit_stream == "connected"
}

# GMLP Principle 9 — human-AI team performance for clinical decisions
require_human_in_loop if {
    input.context.decision_class == "clinical"
}

# Prompt-edit = privileged action (equivalent to prod code change)
deny["prompt edit requires change control"] if {
    input.action == "prompt.edit"
    not input.change_ticket.approved
}
```

**Key architectural choice:** treat *every* AI-touched record — prompt, retrieved context, output, reviewer signature, model version, guardrail decision — as an electronic record subject to Part 11. The policy engine writes them to a WORM store and stamps each with the applicable regulatory citation.

---

## 7. What Makes This IP (and Not Yet Another Blog Post)

Multiple vendors already sell "AI governance platforms." The differentiated wedge sits in three places:

1. **Regulation-native policy libraries.** The Rego bundles ship with citations to the exact subcategory / article / clause they enforce. When an examiner asks *"show me how you meet SR 11-7 Element 3, validation independence,"* the answer is a `git blame` on the policy file plus its CI test history.
2. **Bidirectional crosswalks generated from the policy, not the other way round.** Change a control → the ISO/IEC 42001, NIST AI RMF, EU AI Act, SR 11-7 and RBI FREE-AI mapping files regenerate. No spreadsheet-driven compliance.
3. **Reg-tech + Model-tech in one artifact.** Most vendors do one or the other. Coupling **Governance-as-Code (lifecycle)** with **Policy-as-Code (runtime)** is what actually gives a board a defensible answer.

---

## 8. Maturity Model (for buyer self-assessment)

| Level | Signal | Typical evidence |
|---|---|---|
| **L0 — Ungoverned** | Notebooks, shadow LLMs, no inventory | Nothing an examiner can inspect |
| **L1 — Documented** | Written AI policy, Excel model register | PDFs, static Confluence |
| **L2 — Guardrailed** | NeMo/Guardrails AI in prod apps | App logs, no independent trail |
| **L3 — Policy-as-Code** | OPA/Rego policies in Git, tested in CI, enforced at gateway | Signed policy bundles, decision logs |
| **L4 — Governance-as-Code** | Lifecycle artifacts (registration, validation, change, incidents) in Git; crosswalks auto-generated | Examiner-portal export in 48 hours |
| **L5 — Continuous Assurance** | Red-team CI, drift-triggered revalidation, cross-jurisdictional policy composition | Board-level dashboard tied to control effectiveness |

Anecdotal industry data suggests most BFSI institutions operate at **L1–L2** in 2026. Pharma sits at L1 for GxP AI.

---

## 9. 90-Day Implementation Roadmap (Repeatable Engagement)

**Days 1–30 — Diagnose & scaffold**

- Model & AI-service inventory (Unity Catalog / Purview / registry of record)
- Risk tier assignment per SR 11-7 / GAMP 5 Cat 4-5 logic
- Stand up OPA at the AI gateway; ship a "shadow-mode" policy that logs only

**Days 31–60 — Encode the top-10 controls**

- Bring the community Rego bundle in; overlay the BFSI or Pharma pack
- Wire CI: `opa test`, `conftest`, control-crosswalk regeneration, signed evidence
- Turn on enforcement for lowest-risk use cases first

**Days 61–90 — Prove it to a regulator (simulated)**

- Run an examiner walk-through: pick 3 subcategories (NIST AI RMF GOVERN-2.2, MEASURE-2.7; SR 11-7 validation independence; Part 11 §11.10(d))
- Produce evidence bundle in under 48 hours end-to-end
- Publish a lessons-learned note; feed back to policy library

Deliverable at day 90: a demonstrably auditable AI platform for the first 3–5 use cases, and a repeatable pattern for the next 50.

---

## 10. Honest Limitations

An IP note that hides its own weaknesses is not IP. It is marketing.

- **Rego has a learning curve.** YAML → Rego compilers help but do not eliminate the need for a small policy-engineering competence.
- **Semantic policies are not deterministic.** Blocking "PII" or "prompt injection" ultimately depends on a classifier or an LLM judge. Policy-as-Code makes the *decision* auditable; the *detection* is still probabilistic.
- **Policy engines are not a silver bullet.** A misconfigured OPA is a new single point of failure. Sidecar vs. centralised deployment, cache warmth, and data-mapping freshness matter.
- **Regulation is moving faster than any policy library.** The Digital Omnibus (Nov 2025), the AI Liability Directive withdrawal, the Critical-Infrastructure AI RMF Profile (Apr 2026) and the SP 800-53 AI overlays are all still landing. The library must be maintained, not written once.
- **Governance-as-Code fails without executive sponsorship.** Git-based controls are useless if the CRO / Head of Quality has not accepted them as the system of record.

---

## 11. Call to Action

If you own AI risk in a bank, an insurer, or a pharma company:

1. Stop asking *"which guardrail vendor should we buy?"* Start asking *"where does our policy live, who signs it, and how fast can we prove it to an examiner?"*
2. Adopt an open policy substrate (OPA is the safe default) before you standardise on any single AI gateway or model provider.
3. Publish your control library — internally at minimum, publicly if you can. The organisations that share crosswalks will shape the standards.

Contributions, corrections, and policy-pack extensions welcome via issues and PRs.

---

## References (working list, to be expanded)

1. NIST AI RMF 1.0 & GenAI Profile (NIST AI 600-1); Critical Infrastructure Profile concept note, Apr 2026.
2. ISO/IEC 42001:2023 — AI Management Systems.
3. Regulation (EU) 2024/1689 — EU AI Act; EBA AI Act mapping for banking/payments (Nov 2025); guidelines for providers/deployers of high-risk AI (2026).
4. Federal Reserve SR 11-7 / OCC 2011-12; revised MRM guidance April 2026.
5. RBI FREE-AI Committee Report, 13 Aug 2025 — 7 Sutras & 26 recommendations.
6. Regulation (EU) 2022/2554 — DORA; in force 17 Jan 2025.
7. FDA/IMDRF Good Machine Learning Practice — 10 Guiding Principles.
8. EMA-FDA Guiding Principles of Good AI Practice in Drug Development, Jan 2026.
9. 21 CFR Part 11 & EU Annex 11 — applied to generative AI in GxP.
10. Open Policy Agent — CNCF Graduated policy engine.
11. Databricks Unity Catalog Service Policies (Beta, 2026) — ABAC for AI services.
12. MITRE ATLAS; OWASP LLM Top 10 (2025); OWASP Agentic ASI (Dec 2025).
13. Stanford AI Index 2026 — hallucination rate distribution across leading LLMs.

---

*Prepared as a working IP note. Not legal advice. Regulations cited are as at 21 July 2026 and will move — the policy library must move with them.*
