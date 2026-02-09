# Agent States: Healthcare Claim Adjudication Agent
**Phase 1 – Single Agent Architecture**

---

## Objective

Design a single-agent workflow that adjudicates healthcare insurance claims by:

1. Understanding claim data and context  
2. Evaluating policy coverage and exclusions  
3. Assessing medical necessity  
4. Validating diagnosis and procedure codes  
5. Calculating payable amounts and pricing limits  
6. Assessing fraud risk indicators  
7. Producing an explainable decision  
8. Escalating to Human-in-the-Loop (HITL) when required  

The system must ensure:
- Deterministic tool usage  
- Explainability  
- Auditability  
- Safe escalation  

---

## State Machine (High Level)

### 1. Intake (Perceive – Understand Claim)

**Purpose:** Parse and structure the claim.

Actions:
- Parse claim input
- Extract:
  - claim_id
  - member_id
  - policy_id
  - diagnosis codes (ICD)
  - procedure codes (CPT)
  - billed amount
  - provider details
  - documents

- Determine required evaluations:
  - policy coverage
  - medical necessity
  - billing validation
  - pricing
  - fraud scoring

Output:
- ClaimCase skeleton created

---

### 2. Missing Information Check

**Purpose:** Ensure required claim elements exist.

Checks:
- Required identifiers present
- At least one diagnosis or procedure code
- Amount and service date available

If missing:
- Ask targeted questions
- Stop workflow

---

### 3. Evaluation Planning (Decide Phase)

**Purpose:** Decide which tools must be invoked.

Planning logic:
- Coverage check → always required
- Medical necessity → required for clinical procedures
- Pricing → required for payable calculation
- Fraud scoring → required for high-value or flagged claims

Output:
- Execution plan stored in case object

---

### 4. Evidence & Tool Execution (Act Phase)

Agent calls deterministic tools:

Typical tools:
- Policy rule evaluator
- Medical guideline evaluator
- ICD/CPT validator
- Pricing engine
- Fraud risk scorer

Actions:
- Call tools sequentially or in parallel
- Capture structured outputs
- Append results to audit log

---

### 5. Results Aggregation (Perceive Again)

**Purpose:** Interpret tool outputs.

Actions:
- Normalize results
- Identify:
  - Coverage eligibility
  - Medical necessity status
  - Code validity
  - Payable amount
  - Fraud score

Store structured observations.

---

### 6. Decision Logic (Decide Phase)

Possible decisions:
- Approved
- Partially Approved
- Rejected
- Escalate for Review

Decision rules consider:
- Coverage status
- Pricing limits
- Fraud threshold
- Medical necessity confidence
- Escalation rules

---

### 7. Explanation Generation (Act Phase)

Agent produces:
- Decision summary
- Key rule checks
- Payable calculation explanation
- Tool outputs referenced
- Confidence indicators

---

### 8. Human Approval / Escalation (HITL)

Escalation required if:
- Fraud risk above threshold
- Medical necessity unclear
- Conflicting policy rules
- Claim value above escalation limit

Agent must:
- Stop automation
- Produce structured handoff summary

---

### 9. Finalize

Outputs generated:
- Final adjudication report
- Explanation
- Audit log
- Escalation summary (if applicable)

---

## Stop Conditions (Must Escalate)

The agent must stop and escalate if:

- Medical necessity indeterminate
- Fraud score exceeds threshold
- Conflicting policy rules
- Claim amount exceeds configured limit
- Tool results inconsistent
- Missing critical data after retries

---

## Output Guarantee

Final output must include:

- Decision
- Payable amount
- Coverage result
- Fraud score summary
- Rules applied
- Evidence used
- Next steps or escalation note
