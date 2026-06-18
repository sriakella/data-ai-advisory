---
title: "AML Tip-off Suppression Is Not a Filter. It Is a Model Design Constraint."
vertical: BFS
pattern_type: decision-record
maturity: published
tags:
  - decision-intelligence
  - aml
  - model-design
  - policy-constraints
  - bfsi
  - compliance
  - intervention
published: 2026-06-18
---

# AML Tip-off Suppression Is Not a Filter. It Is a Model Design Constraint.

## Context — the situation this addresses

Most BFSI teams building AML detection frame it as a scoring problem: train a model to maximise P(suspicious activity), threshold the score, file a SAR when the threshold is crossed, and suppress the customer notification as a post-processing step downstream.

The suppression is treated as an output filter — a compliance team requirement bolted onto the model pipeline after the inference is complete.

This framing is architecturally wrong. And it creates regulatory exposure that the filter itself cannot detect.

---

## Decision — the architectural position

**Tip-off suppression must be encoded in the model's output schema and the decisioning layer's action space at design time, not applied as a filter after inference.**

The tip-off prohibition under 31 U.S.C. § 5318(g)(2) (US), POCA 2002 s.333A (UK), and equivalent EU AMLD provisions does not say "suppress the notification." It says customer notification is a structurally inadmissible action when a SAR obligation is triggered. The distinction matters architecturally.

When suppression is a downstream filter, the model's internal output space still contains customer-notification as a valid action with an assigned probability. The filter intercepts it before dispatch. But:

- The audit trail records a suppressed action, not a structurally absent one — a material difference under regulatory examination
- The filter can fail silently: a misconfigured rule, a pipeline race condition, or a model update that shifts output distributions can cause the notification to pass through undetected
- The model cannot be evaluated for regulatory compliance independently of the filter — they are coupled, not separated

When suppression is a design constraint, customer-notification is removed from the model's valid action space before training. The model never assigns probability mass to an inadmissible output. The SAR filing and the downstream orchestration (case creation, escalation routing, compliance documentation) are the only valid output paths. Compliance is structural, not operational.

---

## Tradeoffs — what this gains, what this gives up

**Gains:** Auditable output space. The model's compliance can be verified without reference to any downstream filter. Regulatory examination of the decision record shows structural absence of inadmissible outputs, not evidence of suppression. This is a materially stronger position under FinCEN, FCA, or RBI review.

**Gives up:** Model flexibility. Encoding the policy constraint at design time means the output schema must be re-specified — and the model retrained — when regulatory scope changes. Teams accustomed to treating compliance as a configuration layer will find this discipline harder to operationalise. The tradeoff is correct. Regulatory constraints are not configuration.

---

## Application notes — where this fits, where it does not

**Fits:** Any BFSI team building or re-platforming an AML decisioning layer. The constraint applies regardless of whether the model is a classical ML classifier, a neural network, or an LLM-based reasoning agent. The output schema discipline is the same.

**Does not fit:** Pre-transaction real-time fraud scoring, where the action space legitimately includes customer-facing alerts (step-up authentication, card freeze notification). Fraud and AML share feature engineering patterns but have structurally different output constraints. Do not conflate them in the model design phase.

**On specialist platforms:** For firms using specialist AML platforms (vendor class: Actimize, SAS, Featurespace), the output schema constraint should be verified in the platform's decision record specification — not assumed. Post-decision orchestration layers (case management, SAR drafting, escalation routing) built on top of these platforms must not re-introduce customer-notification as an action path.

---

## References — public sources only

- FinCEN tip-off prohibition: [31 U.S.C. § 5318(g)(2)](https://www.law.cornell.edu/uscode/text/31/5318)
- UK tipping-off offence: [POCA 2002 s.333A](https://www.legislation.gov.uk/ukpga/2002/29/section/333A)
- EU Anti-Money Laundering Directive (AMLD6), Article 39 — tipping-off prohibition: [eur-lex.europa.eu](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018L1673)
- FinCEN SAR filing requirements: [fincen.gov/resources/statutes-regulations/guidance](https://www.fincen.gov/resources/statutes-regulations/guidance)

---

*Cross-reference: [Three-Verb Architecture for BFSI →](./2026-06-16_bfs_reference-architecture_di-activation-substrata.md) — this decision record addresses Layer 3 (Decisioning) and the Policy primitive of that framework.*
