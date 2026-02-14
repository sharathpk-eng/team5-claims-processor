# Agent I/O Contract
Healthcare Claim Adjudication Agent

---

## Input (User or System → Agent)

Structured claim object or documents containing:

### Minimal Required Fields

- claim_id
- member_id
- policy_id
- billed_amount
- service_date
- diagnosis or procedure code
- provider

Optional:
- discharge summary
- invoices
- reports
- prescriptions

---

## Internal Case Object (Canonical)

The agent maintains a canonical `ClaimCase` object:

- claim_id
- member_id
- policy_id
- diagnosis_codes
- procedure_codes
- billed_amount
- coverage_result
- medical_necessity_result
- code_validation_result
- pricing_result
- fraud_score
- decision
- escalation_flag
- explanation
- audit_log
- tool_outputs
- timestamps

---

## Output (Agent → User/System)

All responses must include the structured block below.

---

### 1. Decision

One of:
- Approved
- Partially Approved
- Rejected
- Escalated for Review

---

### 2. Key Findings

- Coverage status
- Medical necessity result
- Code validation result
- Fraud risk summary

---

### 3. Financial Summary

- Billed amount
- Allowed amount
- Payable amount
- Member liability

---

### 4. Policy & Risk Flags

- coverage_valid: true/false
- medical_necessity_passed: true/false
- code_valid: true/false
- fraud_risk_high: true/false
- escalation_required: true/false

---

### 5. Evidence Used

- Policy rules referenced
- Guidelines referenced
- Pricing tables used
- Fraud indicators

---

### 6. Next Steps

Either:
- Payment processing handoff
OR
- Escalation instructions

---

## HITL Language Requirement

If escalation is required, the agent must ask:

"Escalation required. Approve forwarding this claim for human medical or fraud review? (yes/no)"

The agent must not proceed without explicit confirmation.
