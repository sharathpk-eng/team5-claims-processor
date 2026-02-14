# Healthcare Insurance Claim Adjudication System
## Requirements Document

---

## 1. Executive Summary

This document outlines the requirements for an AI-powered Healthcare Insurance Claim Adjudication Agent. The system automates the evaluation of healthcare insurance claims by checking policy coverage, medical necessity, billing codes, pricing, and fraud risk. It produces explainable decisions and escalates complex cases to human reviewers when needed.

---

## 2. System Overview

### 2.1 Purpose

The Claim Adjudication Agent processes healthcare insurance claims to determine:
- Whether the claim is covered under the policy
- If the medical service was necessary
- If billing codes are valid
- What amount should be paid
- If there are fraud risk indicators

### 2.2 Key Principles

- **Deterministic**: Uses structured tools with predictable outputs
- **Explainable**: Every decision includes reasoning and evidence
- **Auditable**: Complete audit trail of all evaluations
- **Safe**: Escalates uncertain cases to human reviewers

---

## 3. System Architecture

### 3.1 Single Agent Design

The system uses a single AI agent that follows a state machine workflow to process claims from intake to final decision.

```
┌─────────────────────────────────────────────────────────────┐
│                   CLAIM ADJUDICATION AGENT                  │
│                                                             │
│  ┌─────────┐   ┌──────────┐   ┌─────────┐   ┌──────────┐ │
│  │ Intake  │──▶│ Validate │──▶│ Evaluate│──▶│ Decision │ │
│  └─────────┘   └──────────┘   └─────────┘   └──────────┘ │
│                                     │                       │
│                                     ▼                       │
│                              ┌─────────────┐               │
│                              │ Escalation  │               │
│                              │   (HITL)    │               │
│                              └─────────────┘               │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. Functional Requirements

### 4.1 Input Requirements

The system must accept claims with the following information:

#### Required Fields
- `claim_id` - Unique claim identifier
- `member_id` - Patient/member identifier
- `policy_id` - Insurance policy identifier
- `billed_amount` - Total amount billed
- `service_date` - Date of service
- `diagnosis_codes` - ICD diagnosis codes
- `procedure_codes` - CPT procedure codes
- `provider` - Healthcare provider information

#### Optional Fields
- Discharge summaries
- Medical invoices
- Lab reports
- Prescriptions
- Supporting documentation

### 4.2 Processing Workflow

The agent follows an 8-state workflow:

```
┌──────────────────────────────────────────────────────────────┐
│                    WORKFLOW STATE MACHINE                    │
└──────────────────────────────────────────────────────────────┘

    1. INTAKE
    ↓
    Parse claim data and extract key fields
    
    2. VALIDATION
    ↓
    Check for missing required information
    
    3. PLANNING
    ↓
    Determine which evaluations are needed
    
    4. EXECUTION
    ↓
    Run evaluation tools (coverage, necessity, pricing, fraud)
    
    5. AGGREGATION
    ↓
    Collect and normalize all tool results
    
    6. DECISION
    ↓
    Apply decision logic based on results
    
    7. EXPLANATION
    ↓
    Generate human-readable explanation
    
    8. ESCALATION CHECK
    ↓
    Determine if human review is needed
    
    FINALIZE
    ↓
    Output final decision and audit trail
```

#### State 1: Intake
- Parse incoming claim data
- Extract structured fields
- Create internal `ClaimCase` object
- Identify required evaluations

#### State 2: Missing Information Check
- Verify all required fields are present
- Request missing information if needed
- Stop workflow if critical data is unavailable

#### State 3: Evaluation Planning
- Determine which tools to invoke:
  - Coverage check (always required)
  - Medical necessity (for clinical procedures)
  - Code validation (always required)
  - Pricing calculation (always required)
  - Fraud scoring (for high-value or flagged claims)

#### State 4: Evidence & Tool Execution
- Invoke evaluation tools:
  - **Policy Rule Evaluator**: Check coverage and exclusions
  - **Medical Guideline Evaluator**: Assess medical necessity
  - **ICD/CPT Validator**: Verify diagnosis and procedure codes
  - **Pricing Engine**: Calculate allowed and payable amounts
  - **Fraud Risk Scorer**: Identify suspicious patterns
- Capture all tool outputs
- Log all actions in audit trail

#### State 5: Results Aggregation
- Normalize tool outputs
- Extract key findings:
  - Coverage eligibility status
  - Medical necessity determination
  - Code validation results
  - Calculated payable amount
  - Fraud risk score

#### State 6: Decision Logic
- Apply decision rules to determine outcome:
  - **Approved**: All checks passed, claim is payable
  - **Partially Approved**: Some services covered, others excluded
  - **Rejected**: Coverage denied or medical necessity failed
  - **Escalated**: Requires human review

#### State 7: Explanation Generation
- Create structured explanation including:
  - Decision summary
  - Key rule checks performed
  - Payable amount calculation breakdown
  - Evidence and tools referenced
  - Confidence indicators

#### State 8: Human Approval / Escalation (HITL)
- Check escalation triggers
- If escalation required:
  - Stop automated processing
  - Request human approval
  - Provide handoff summary

### 4.3 Evaluation Tools

The system uses the following evaluation tools:

```
┌─────────────────────────────────────────────────────────┐
│                   EVALUATION TOOLS                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────────────┐      ┌─────────────────────┐   │
│  │ Policy Rules     │      │ Medical Guidelines  │   │
│  │ Evaluator        │      │ Evaluator           │   │
│  │                  │      │                     │   │
│  │ • Coverage check │      │ • Necessity check   │   │
│  │ • Exclusions     │      │ • Clinical validity │   │
│  └──────────────────┘      └─────────────────────┘   │
│                                                         │
│  ┌──────────────────┐      ┌─────────────────────┐   │
│  │ ICD/CPT          │      │ Pricing Engine      │   │
│  │ Validator        │      │                     │   │
│  │                  │      │ • Allowed amount    │   │
│  │ • Code validity  │      │ • Member liability  │   │
│  │ • Code matching  │      │ • Payable amount    │   │
│  └──────────────────┘      └─────────────────────┘   │
│                                                         │
│  ┌──────────────────┐                                  │
│  │ Fraud Risk       │                                  │
│  │ Scorer           │                                  │
│  │                  │                                  │
│  │ • Pattern detect │                                  │
│  │ • Risk scoring   │                                  │
│  └──────────────────┘                                  │
└─────────────────────────────────────────────────────────┘
```

---

## 5. Output Requirements

### 5.1 Decision Types

The system must output one of four decision types:
- **Approved**: Claim fully approved for payment
- **Partially Approved**: Some services approved, others denied
- **Rejected**: Claim denied
- **Escalated for Review**: Requires human review

### 5.2 Output Structure

Every claim adjudication must include:

#### 1. Decision
- Clear statement of approval, partial approval, rejection, or escalation

#### 2. Key Findings
- Coverage status (covered/not covered)
- Medical necessity result (passed/failed/uncertain)
- Code validation result (valid/invalid)
- Fraud risk summary (low/medium/high)

#### 3. Financial Summary
- Billed amount
- Allowed amount (per policy)
- Payable amount (what insurance pays)
- Member liability (what patient owes)

#### 4. Policy & Risk Flags
```
coverage_valid: true/false
medical_necessity_passed: true/false
code_valid: true/false
fraud_risk_high: true/false
escalation_required: true/false
```

#### 5. Evidence Used
- Policy rules referenced
- Medical guidelines referenced
- Pricing tables used
- Fraud indicators detected

#### 6. Next Steps
- Payment processing instructions, OR
- Escalation instructions for human review

### 5.3 Output Example

```json
{
  "claim_id": "CLM-2024-12345",
  "decision": "Approved",
  "key_findings": {
    "coverage_status": "Covered",
    "medical_necessity": "Passed",
    "code_validation": "Valid",
    "fraud_risk": "Low"
  },
  "financial_summary": {
    "billed_amount": 5000.00,
    "allowed_amount": 4200.00,
    "payable_amount": 3360.00,
    "member_liability": 840.00
  },
  "flags": {
    "coverage_valid": true,
    "medical_necessity_passed": true,
    "code_valid": true,
    "fraud_risk_high": false,
    "escalation_required": false
  },
  "evidence": {
    "policy_rules": ["Rule 123: Outpatient surgery covered"],
    "guidelines": ["Guideline G45: Procedure medically necessary"],
    "pricing_tables": ["Table P12: Surgical procedures"],
    "fraud_indicators": []
  },
  "next_steps": "Proceed to payment processing"
}
```

---

## 6. Escalation Requirements

### 6.1 Escalation Triggers

The system MUST escalate to human review when:
- Fraud risk score exceeds threshold
- Medical necessity is indeterminate
- Conflicting policy rules detected
- Claim amount exceeds configured limit
- Tool results are inconsistent
- Missing critical data after retries

### 6.2 Human-in-the-Loop (HITL) Protocol

When escalation is required:

1. Agent must STOP automated processing
2. Agent must ask: "Escalation required. Approve forwarding this claim for human medical or fraud review? (yes/no)"
3. Agent must NOT proceed without explicit confirmation
4. Agent must provide structured handoff summary

### 6.3 Escalation Handoff

The escalation handoff must include:
- Reason for escalation
- All evaluation results
- Conflicting findings (if any)
- Recommended next steps
- Complete audit trail

```
┌────────────────────────────────────────────────────┐
│            ESCALATION DECISION FLOW                │
└────────────────────────────────────────────────────┘

    Evaluation Complete
           ↓
    Check Escalation Triggers
           ↓
    ┌──────────────┐
    │ Any trigger  │
    │   active?    │
    └──────────────┘
         ↓     ↓
       YES    NO
         ↓     ↓
    ┌─────┐  ┌──────────┐
    │HITL │  │ Finalize │
    │     │  │ Decision │
    └─────┘  └──────────┘
       ↓
    Request Human Approval
       ↓
    Provide Handoff Summary
       ↓
    Wait for Confirmation
```

---

## 7. Data Model

### 7.1 ClaimCase Object

The internal canonical data structure:

```
ClaimCase {
  // Identifiers
  claim_id: string
  member_id: string
  policy_id: string
  
  // Clinical Data
  diagnosis_codes: string[]
  procedure_codes: string[]
  
  // Financial
  billed_amount: number
  
  // Evaluation Results
  coverage_result: object
  medical_necessity_result: object
  code_validation_result: object
  pricing_result: object
  fraud_score: number
  
  // Decision
  decision: string
  escalation_flag: boolean
  explanation: string
  
  // Audit
  audit_log: array
  tool_outputs: array
  timestamps: object
}
```

---

## 8. Non-Functional Requirements

### 8.1 Auditability
- Every action must be logged
- All tool invocations must be recorded
- Complete audit trail must be maintained
- Timestamps for all state transitions

### 8.2 Explainability
- Every decision must include reasoning
- Evidence must be cited
- Rules applied must be documented
- Confidence levels must be indicated

### 8.3 Determinism
- Same input should produce same output
- Tool behavior must be predictable
- Decision logic must be rule-based

### 8.4 Safety
- Uncertain cases must escalate
- High-risk claims must be reviewed
- No automated decisions on edge cases

### 8.5 Performance
- Process standard claims within acceptable timeframe
- Handle concurrent claim processing
- Scale to handle claim volume

---

## 9. Decision Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│              CLAIM ADJUDICATION DECISION FLOW               │
└─────────────────────────────────────────────────────────────┘

                    Claim Received
                         ↓
                  ┌──────────────┐
                  │   Intake &   │
                  │  Validation  │
                  └──────────────┘
                         ↓
                  ┌──────────────┐
                  │   Coverage   │───→ Not Covered ───→ REJECT
                  │    Check     │
                  └──────────────┘
                         ↓
                      Covered
                         ↓
                  ┌──────────────┐
                  │   Medical    │───→ Failed ───→ REJECT
                  │  Necessity   │
                  └──────────────┘
                         ↓
                      Passed
                         ↓
                  ┌──────────────┐
                  │     Code     │───→ Invalid ───→ REJECT
                  │  Validation  │
                  └──────────────┘
                         ↓
                      Valid
                         ↓
                  ┌──────────────┐
                  │   Pricing    │
                  │ Calculation  │
                  └──────────────┘
                         ↓
                  ┌──────────────┐
                  │    Fraud     │───→ High Risk ───→ ESCALATE
                  │   Scoring    │
                  └──────────────┘
                         ↓
                      Low Risk
                         ↓
                  ┌──────────────┐
                  │  Escalation  │───→ Triggered ───→ ESCALATE
                  │    Check     │
                  └──────────────┘
                         ↓
                   No Escalation
                         ↓
                      APPROVE
                  (or Partial Approve)
```

---

## 10. Use Cases

### 10.1 Standard Claim Approval

**Scenario**: Routine outpatient procedure claim

**Flow**:
1. Claim received with all required fields
2. Coverage check: Procedure covered under policy
3. Medical necessity: Procedure medically necessary
4. Code validation: ICD and CPT codes valid
5. Pricing: Calculate allowed and payable amounts
6. Fraud check: No risk indicators
7. Decision: APPROVED
8. Output: Payment instructions with financial breakdown

### 10.2 Claim Rejection

**Scenario**: Service not covered under policy

**Flow**:
1. Claim received
2. Coverage check: Service excluded from policy
3. Decision: REJECTED
4. Output: Rejection notice with policy exclusion cited

### 10.3 Escalation for Medical Review

**Scenario**: Unclear medical necessity

**Flow**:
1. Claim received
2. Coverage check: Covered
3. Medical necessity: Indeterminate (conflicting guidelines)
4. Escalation triggered
5. Agent requests human approval
6. Handoff summary provided to medical reviewer
7. Await human decision

### 10.4 Fraud Detection Escalation

**Scenario**: High fraud risk indicators

**Flow**:
1. Claim received
2. All checks pass
3. Fraud scoring: High risk (unusual billing pattern)
4. Escalation triggered
5. Agent requests human approval
6. Handoff summary provided to fraud investigator
7. Await human decision

---

## 11. Success Criteria

The system is successful when it:

1. **Accurately processes claims**: Correct decisions based on policy rules
2. **Provides clear explanations**: Every decision is understandable
3. **Maintains audit trail**: Complete record of all evaluations
4. **Escalates appropriately**: Uncertain cases go to human review
5. **Operates safely**: No incorrect automated approvals
6. **Scales effectively**: Handles claim volume efficiently

---

## 12. Future Enhancements

Potential future improvements:
- Multi-agent architecture for specialized evaluations
- Machine learning for fraud pattern detection
- Integration with external medical databases
- Real-time policy updates
- Automated appeals processing
- Predictive analytics for claim trends

---

## 13. Glossary

- **ICD**: International Classification of Diseases (diagnosis codes)
- **CPT**: Current Procedural Terminology (procedure codes)
- **HITL**: Human-in-the-Loop (human review process)
- **Adjudication**: Process of evaluating and deciding on insurance claims
- **Medical Necessity**: Determination that a service is appropriate and required
- **Allowed Amount**: Maximum amount insurance will pay for a service
- **Member Liability**: Amount patient is responsible to pay

---

**Document Version**: 1.0  
**Last Updated**: February 11, 2026  
**Status**: Draft for Review

