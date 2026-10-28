# AI Proposal Triage Design (`MATACS-TH-2026-02`)

**Candidate:** Sheryar Khan  
**Position:** Associate Software Developer  
**Company:** Matacs Technologies Inc.  
**Reference:** MATACS-TH-2026-02

---

## 1. Problem Overview

A facilities management team receives lengthy contractor proposals for commercial projects. This assessment focuses on a 150 kW commercial rooftop solar installation in Windsor, Ontario, with a maximum budget of CAD $260,000 and a required completion time of 16 weeks.

Reviewing multiple proposals manually can be slow and inconsistent. Important details, such as electrical contractor licensing, insurance, project experience, and completion schedules, may be missed.

The goal is to design an AI-assisted proposal triage system that helps reviewers identify missing information, check mandatory requirements, calculate weighted scores, and compare eligible proposals. The system supports human decision-making; it does not make the final contract award.

## 2. Proposed Solution Overview

The proposed solution separates AI-assisted document understanding from rule-based evaluation.

1. **AI for language, code for scoring:** AI can extract facts from unstructured proposal documents into structured data. Application code applies mandatory checks, calculates scores, and ranks eligible proposals.
2. **Defensive design:** Proposal content is treated as untrusted data. Instructions embedded inside a proposal, such as the manipulation attempt in Proposal C's appendix, must not control the evaluation.
3. **Transparent decisions:** Missing or ambiguous information should not be guessed. Proposals with unresolved eligibility or evidence issues are flagged for human review.

**Current implementation:** The Python prototype demonstrates rule-based checks, scoring, ranking, and proposal flags using sample data. It does not perform live AI extraction, read uploaded PDFs, or implement a complete database-backed audit system. The prompts and write-up describe parts of the proposed broader system.

## 3. Division of Responsibilities: AI vs. Deterministic Code

| Function | Proposed responsibility | Rationale |
|---|---|---|
| Criteria and weights | Application code and database | Criteria should be structured, validated, and versioned. |
| Proposal fact extraction | AI (large language model, or LLM) | AI can help interpret varied wording and document formats. |
| Evidence quotes | AI proposes quotes; application and human checks verify them | Evidence helps reviewers check extracted facts against the original proposal. |
| Prompt-injection safeguards | Prompt design and application validation | Proposal text must be treated as untrusted data, not as instructions. |
| Mandatory checks | Application code using verified facts | The same eligibility rules should be applied consistently. |
| Weighted scoring | Application code | Fixed formulas make calculations repeatable and easier to audit. |
| Ranking and sorting | Application code | Eligible proposals can be ordered using their calculated scores. |
| Reviewer explanations | Templates or AI-assisted summaries | Explanations should reflect verified facts, scores, and flags. |
| Verification and final award | Human reviewer and authorized procurement officer | Humans resolve uncertainty and make the final award decision. |
| Audit logging | Database and application code in a production system | A production implementation should preserve evaluation history and reviewer actions. |

Deterministic code improves consistency, but it cannot guarantee correct results if the input data, scoring rules, or implementation contain errors. Human verification remains important.

## 4. Deliverables and File Guide

The following files are intended to be included in this repository. Confirm that each file exists at the listed path before submission.

| Deliverable | File | Description |
|---|---|---|
| Technical write-up | [WRITEUP.md](WRITEUP.md) | Answers the five assessment questions within the required word limit. |
| Workflow diagram | [diagram/workflow.png](diagram/workflow.png) | Visual overview of the proposed workflow. |
| Diagram source | [diagram/workflow.mmd](diagram/workflow.mmd) | Editable Mermaid diagram source. |
| AI prompt specification | [PROMPTS.md](PROMPTS.md) | Prompts for structured extraction and reviewer explanations, including safeguards against prompt injection. |
| Ranking results | [ranking.json](ranking.json) | Sample results for proposals A-E, including ranks, scores, flags, and reasons. |
| Python prototype | [prototype/triage.py](prototype/triage.py) | Runnable demonstration of rule-based checks, scoring, ranking, and flags. |

## 5. Sample Proposal Evaluation Results

The current prototype produces the following results:

| Proposal | Bidder | Status | Rank | Score | Reason |
|---|---|---|---:|---:|---|
| A | Northshore Solar Ltd. | `ranked` | 1 | 97 | Reports 14 qualifying installations, a $238,000 price, a 12-week timeline, a named lead, a six-person crew, a safety plan, and 12-month monitoring. |
| E | SunGrid Contractors Inc. | `ranked` | 2 | 90 | Offers a 12-year workmanship warranty, 24-month monitoring, a $242,500 price, a 13-week timeline, and eight qualifying installations. |
| B | BrightPath Energy Inc. | `flagged` | `null` | `null` | The proposal does not confirm that the bidder itself holds the required Ontario electrical contractor licence. |
| C | Lakeview Renewables Corp. | `flagged` | `null` | `null` | The proposal reports nine installations since 2020, but does not establish how many meet the required five-year experience period. |
| D | Essex Green Power Inc. | `flagged` | `null` | `null` | The completion timeline is not committed, so compliance with the 16-week requirement cannot be confirmed. |

A flagged proposal is not automatically rejected. It requires clarification or human review before its eligibility and score can be determined.

The scores shown here are the current prototype's outputs. They should not be treated as proof of a fully validated procurement scoring model.

## 6. Candidate Information and AI-Use Disclosure

**Complete this section honestly before submission. Replace the examples below where needed.**

### A. Time Actually Spent

**Total time:** About 75 minutes

Include only work you actually completed, such as reviewing the requirements, designing the workflow, testing the Python prototype, and reviewing the documentation.

### B. AI Tools Disclosure

Describe which AI tools you used and what you used them for. For example, if accurate:

> I used ChatGPT to help review the proposed design, identify inconsistencies between the Python prototype and its documentation, explain technical concepts, and refine written explanations. I reviewed the suggested changes and tested the Python prototype locally. The current prototype uses sample data and rule-based evaluation; it does not perform live AI extraction of contractor PDFs.

Edit this disclosure so that it accurately describes your own work and tool usage.

### C. Example of Reviewing and Correcting AI Output

Use a real example from your work. One possible example, if it accurately reflects what happened, is:

> During review, I identified that Proposal C's nine installations since 2020 did not establish how many installations fell within the required five-year period. I updated the result to flag Proposal C rather than assign an experience score based on an unsupported assumption. This makes the decision more transparent and gives a human reviewer a clear question to resolve.

Do not claim that you independently identified or corrected something unless that accurately describes your contribution. You should be able to explain your reasoning and the changes you made.

## 7. How to Run the Prototype

Run the script from the repository's root directory using Python 3.

**Windows PowerShell:**

```powershell
py prototype/triage.py
```

Alternatively:

```powershell
python prototype/triage.py
```

The script prints the sample proposal results as JSON. The current expected outcome is Proposal A ranked first with a score of 97, Proposal E ranked second with a score of 90, and Proposals B, C, and D flagged with null scores and ranks.

This is a demonstration using sample proposal data, not a complete production application.
