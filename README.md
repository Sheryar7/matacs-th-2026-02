# AI Proposal Triage Design

**Assessment Reference:** MATACS-TH-2026-02  
**Candidate:** Sheryar Khan  
**Position:** Associate Software Developer  
**Company:** Matacs Technologies Inc.

---

## 1. Project Overview

The AI Proposal Triage Tool is a proposed system designed to help facilities management teams review contractor proposals consistently and efficiently.

This assessment focuses on a **150 kW commercial rooftop solar installation in Windsor, Ontario**, with a maximum budget of **CAD $260,000** and a required completion time of **16 weeks**.

Contractor proposals can contain lengthy documents, technical details, commercial terms, and mandatory compliance information. Reviewing these documents manually can be time-consuming and may lead to missing information or inconsistent evaluations.

The proposed system separates AI-assisted document understanding from deterministic evaluation rules and human decision-making.

**Core principle: AI assists with understanding documents; application code applies evaluation rules; authorized humans make the final procurement decision.**

## 2. Current Implementation

The current Python prototype demonstrates rule-based eligibility checks, weighted scoring, ranking, and flagging using structured sample data.

It does not currently read contractor PDFs directly, call a live AI model, or implement a complete database-backed audit system. The AI prompts and architecture diagram describe proposed capabilities for a broader production system.

### Implemented prototype capabilities

- Applies mandatory compliance checks to structured proposal data.
- Evaluates proposal information against defined rules.
- Calculates scores and ranks proposals that pass the implemented checks.
- Flags proposals with unresolved or unconfirmed information.
- Produces structured JSON results for review.

### Proposed production capabilities

- Extracting proposal facts from uploaded PDF documents using an AI model.
- Associating extracted facts with supporting evidence quotations.
- Validating extracted data and evidence against source documents.
- Providing AI-assisted reviewer explanations.
- Storing criteria versions, evaluation history, and reviewer actions in a database.
- Maintaining persistent audit records and access controls.

These proposed features should not be interpreted as functionality already implemented in the current prototype.

## 3. System Design

The proposed design separates responsibilities to improve consistency, transparency, and human oversight.

| Responsibility | Proposed approach |
|---|---|
| Proposal document understanding | AI-assisted extraction into structured data |
| Evidence handling | Extract quotations and verify them against source documents |
| Mandatory compliance checks | Deterministic application code |
| Weighted scoring | Deterministic application code |
| Ranking and flagging | Application code using defined rules |
| Reviewer explanations | Templates or AI-assisted summaries based on verified results |
| Unclear or missing information | Flag for human review |
| Final procurement decision | Authorized human decision-maker |
| Persistent audit history | Database-backed production capability |

Deterministic code makes calculations repeatable for the same inputs and rules, but it does not guarantee that the inputs or rules are correct. Evidence validation and human review remain necessary.

## 4. Assessment Deliverables

The repository contains the following assessment materials. Confirm that each file is present and up to date before submission.

| File | Purpose |
|---|---|
| [`WRITEUP.md`](WRITEUP.md) | Technical write-up answering the assessment questions |
| [`diagram/workflow.png`](diagram/workflow.png) | Visual representation of the proposed workflow |
| [`diagram/workflow.mmd`](diagram/workflow.mmd) | Editable Mermaid source for the workflow diagram |
| [`PROMPTS.md`](PROMPTS.md) | Proposed AI extraction and reviewer explanation prompts |
| [`ranking.json`](ranking.json) | Structured sample evaluation results for proposals A–E |
| [`prototype/triage.py`](prototype/triage.py) | Runnable Python prototype for rule-based evaluation |


## 5. Sample Evaluation Results

The following results are reported by the current prototype. They should be checked against the latest execution of the script and the contents of `ranking.json`.

| Proposal | Contractor | Status | Rank | Score |
|---|---|---|---:|---:|
| A | Northshore Solar Ltd. | `ranked` | 1 | 97 |
| E | SunGrid Contractors Inc. | `ranked` | 2 | 90 |
| B | BrightPath Energy Inc. | `flagged` | `null` | `null` |
| C | Lakeview Renewables Corp. | `flagged` | `null` | `null` |
| D | Essex Green Power Inc. | `flagged` | `null` | `null` |

### Explanation of results

- **Proposal A:** Reports 14 qualifying installations, a price of $238,000, a 12-week timeline, a named lead, a six-person crew, a safety plan, and 12 months of monitoring.
- **Proposal E:** Offers a 12-year workmanship warranty, 24 months of monitoring, a price of $242,500, a 13-week timeline, and eight qualifying installations.
- **Proposal B:** The proposal does not confirm that the bidding company itself holds the required Ontario electrical contractor licence. A subcontractor's licence does not establish the bidder's own compliance.
- **Proposal C:** The proposal reports nine installations since 2020, but the available information does not establish how many qualify within the required five-year evaluation period. Its appendix also contains instructions attempting to influence an automated review.
- **Proposal D:** The installation schedule is deferred until a detailed site assessment, so compliance with the 16-week completion requirement cannot be verified.

A flagged proposal is not automatically rejected. It requires clarification or human review before its eligibility and score can be determined.

The reported scores of 97 and 90 are prototype outputs, not proof that the scoring model has been independently validated for a real procurement decision.

## 6. Evaluation Criteria

The assessment uses the following weighted criteria:

| Criterion | Weight |
|---|---:|
| Commercial installation experience | 30% |
| Price | 25% |
| Completion timeline | 15% |
| Warranties | 15% |
| Team and approach | 15% |
| **Total** | **100%** |

The mandatory requirements include the bidding company's Ontario electrical contractor licence, WSIB clearance, and $5 million in commercial general liability insurance. The project also has a CAD $260,000 budget cap and a 16-week completion requirement.

The prototype applies its implemented rules to structured sample data. Refer to [`prototype/triage.py`](prototype/triage.py) for the actual implemented behavior.

## 7. How to Run the Prototype

### Requirements

- Python 3 installed.
- A terminal opened in the repository's root directory.

The prototype uses structured sample data and does not require live AI model access to run.

### Windows PowerShell

```powershell
py prototype/triage.py
```

Alternatively:

```powershell
python prototype/triage.py
```

The script prints the proposal evaluation results as JSON.

After running it, compare the output with [`ranking.json`](ranking.json) to confirm that the saved results match the current implementation.

## 8. Candidate Information and AI-Use Disclosure

### A. Time Spent

**Total time:** Approximately 3 hours, including AI-assisted development, implementation review, testing, documentation, and final validation.



### B. AI Tools Disclosure

AI tools were used to assist with reviewing the design, identifying inconsistencies between the prototype and its documentation, explaining technical concepts, and improving written explanations.

The suggested changes were reviewed, and the Python prototype was tested locally. The current prototype uses structured sample data and rule-based evaluation; it does not perform live AI extraction of contractor PDFs.



### C. Example of Reviewing and Correcting AI Output

One important review issue concerned Proposal C. The proposal reported nine installations since 2020, but this information alone did not establish how many installations qualified within the required five-year evaluation period.

The evaluation was therefore updated to flag Proposal C for clarification rather than assign an experience score based on an unsupported assumption.

This illustrates why missing or ambiguous evidence should be identified for human review instead of being treated as a confirmed fact.



## 9. Limitations and Future Improvements

The current prototype is a demonstration of rule-based triage using structured sample data. It is not a complete production procurement application.

Potential future improvements include:

1. PDF text extraction and AI-assisted structured data extraction.
2. Evidence quotation verification against original proposal documents.
3. Automated validation of AI output against a strict schema.
4. More comprehensive automated tests for mandatory requirements, scoring boundaries, and missing data.
5. Persistent storage for criteria versions, evaluation results, and reviewer actions.
6. An interface that lets reviewers inspect evidence and resolve flagged items.
7. Audit records for changes, overrides, and final decisions.

Any production implementation should preserve human authority over procurement decisions and provide a clear way to investigate flagged results.

## 10. Final Note

This project demonstrates a practical approach to proposal triage by separating document understanding, rule-based evaluation, and human decision-making.

Its most important design principle is **to make uncertainty visible rather than invent missing facts**. AI can assist with extracting and explaining information, while deterministic code and human oversight help keep the evaluation process consistent, transparent, and accountable.
