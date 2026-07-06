# LinkedIn Post — 2026-06-16
## Amplifies: bfs/2026-06-16_bfs_reference-architecture_di-activation-substrata.md

---

BFSI firms build fraud platforms, marketing platforms, and compliance platforms separately — then wonder why the same customer gets blocked for fraud and targeted for a credit card upgrade in the same session.

The substrate is the same for all three. What changes is the verb.

In the architecture I published today, every BFSI decision function maps to one of three verbs operating on a shared five-primitive substrate: Activation (drive growth), Intervention (protect the firm), Automation (improve efficiency). The entity model, the state store, the policy engine, and the feedback loop do not change between them. Only the model vocabulary and the optimisation target shift.

The sharpest constraint in the framework is one most architecture diagrams omit entirely: AML tip-off suppression. When a SAR is triggered, customer notification is legally prohibited — 31 U.S.C. § 5318(g)(2) in the US, POCA 2002 in the UK. That suppression must fire in Layer 3 of the decision function, before any action is dispatched. Not downstream. Not as an alert filter. Architecturally upstream.

The full reference architecture — DI substrata table, four-layer decision stack, use case patterns across all three verbs, and the Salesforce FSC expression — is published in the link below.

https://github.com/sriakella/data-ai-advisory/blob/main/bfs/2026-06-16_bfs_reference-architecture_di-activation-substrata.md

#DecisionIntelligence #BFSI #AgenticAI #DataArchitecture
