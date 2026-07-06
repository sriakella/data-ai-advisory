# LinkedIn Post — 2026-06-18
## Amplifies: bfs/2026-06-18_bfs_decision-record_aml-tip-off-suppression.md

---

Your AML model is mis-specified before it is mis-deployed.

Most teams treat tip-off suppression as a compliance filter bolted onto the output of an AML scoring pipeline. The model scores P(suspicious), crosses a threshold, files a SAR — and a downstream rule blocks the customer notification.

That filter can fail silently. A misconfigured rule, a pipeline race condition, or a model update that shifts output distributions can let the notification through. And the audit trail records a suppressed action, not a structurally absent one — a material difference under FinCEN, FCA, or RBI examination.

The correct design: tip-off prohibition (31 U.S.C. § 5318(g)(2), POCA 2002 s.333A) does not say suppress the notification. It says customer notification is a structurally inadmissible action when a SAR obligation is triggered. Remove it from the model's valid output space at design time. The model never assigns probability mass to an inadmissible output. Compliance is structural, not operational.

This is a decision record, not a position piece. The architectural decision, the tradeoffs, and the regulatory grounding are published below.

[link]

#AML #BFSI #DecisionIntelligence #ModelDesign
