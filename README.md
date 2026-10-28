# AI Proposal Triage Design (`MATACS-TH-2026-02`)

**Candidate:** Sheryar Khan  
**Position:** Associate Software Developer  
**Company:** Matacs Technologies Inc.  
**Reference:** MATACS-TH-2026-02  

---

## 1. Problem Overview

A facilities management team receives lengthy, unstructured contractor proposals for commercial facilities projects. For this assessment, the scenario is a **150 kW commercial rooftop solar installation in Windsor, Ontario** with a budget ceiling of **$260,000 CAD** and a mandatory completion window of **16 weeks**.

Reviewing these multi-page PDF proposals manually is slow, error-prone, and inconsistent. Reviewers can easily miss unstated electrical safety licences or subtle legal omissions. The team requires an **AI-assisted triage system** that automates initial compliance screening, calculates objective scores based on configurable criteria, flags missing or suspicious information, and produces a transparent, evidence-backed ranked shortlist for human decision-makers.

---

## 2. Proposed Solution Overview

Our solution is a lightweight, human-centric triage pipeline designed around three core principles:
1. **AI for Language, Code for Math:** We use AI exclusively to extract unstructured proposal text into validated JSON facts with verbatim evidence quotes. All mathematical scoring, pass/fail gates, and sorting are executed by deterministic code.
2. **Defensive by Design:** Untrusted document text is isolated inside `<untrusted_proposal_document>` delimiters, neutralizing prompt injection attempts (such as the malicious instruction found in Proposal C's Appendix C).
3. **Transparent & Auditable:** Missing or ambiguous facts are never assumed or invented. Proposals with unconfirmed requirements (such as Proposal B's unverified subcontractor licence and Proposal D's uncommitted schedule) are categorized as `flagged` with null scores, ensuring full visibility for human reviewers.

---

## 3. Division of Responsibilities: AI vs. Deterministic Code

| Function | Handled By | Rationale |
| :--- | :--- | :--- |
| **Criteria & Weights Setup** | Plain Code & DB | Admin criteria must be strictly structured, versioned, and stored in a database. |
| **Proposal Fact Extraction** | **AI (LLM)** | Unstructured PDFs have varied layouts. LLMs excel at parsing free-text into JSON. |
| **Evidence Quote Linking** | **AI (LLM)** | AI pinpoints verbatim quotes from the proposal text to support each extracted fact. |
| **Prompt Injection Defense** | Plain Code & AI Guard | System isolates document text inside untrusted tags and screens for unauthorized instructions. |
| **Mandatory Pass/Fail Gates** | Plain Code | Licensing and insurance rules require strict boolean logic with zero hallucinations. |
| **Weighted Scoring Calculations** | Plain Code | Mathematical formulas must be 100% exact, auditable, and repeatable. |
| **Ranking & Sorting** | Plain Code | Deterministic sorting algorithms order eligible bids by score. |
| **Executive Explanations** | **AI (LLM)** | Generates plain-language briefing notes synthesized strictly from verified facts. |
| **Verification & Final Award** | **Human Reviewer** | Facilities managers investigate flags, approve overrides, and make the legal award. |
| **Immutable Audit Logging** | Plain Code & DB | Stores criteria versions, raw inputs, scores, and reviewer notes in an unalterable log. |

---

## 4. Deliverables & File Guide

All assessment deliverables are organized within this repository:

| Deliverable | File Link | Description |
| :--- | :--- | :--- |
| **Design Write-Up** | [WRITEUP.md](WRITEUP.md) | Answers to the 5 core questions from the brief (strictly under 1,000 words). |
| **Workflow Diagram** | [diagram/workflow.png](diagram/workflow.png) | High-resolution visual process flow from upload to human decision. |
| **Mermaid Diagram Source** | [diagram/workflow.mmd](diagram/workflow.mmd) | Editable Mermaid diagram source code. |
| **AI Prompts Specification** | [PROMPTS.md](PROMPTS.md) | Production extraction and explanation prompts with anti-injection defenses. |
| **Expected Ranking JSON** | [ranking.json](ranking.json) | Validated JSON output ranking Proposals A–E with statuses, scores, and reasons. |
| **Deterministic Prototype** | [prototype/triage.py](prototype/triage.py) | Runnable Python script validating math, mandatory rules, and edge cases. |
| **Learning & Interview Guide** | [LEARNING_GUIDE.md](LEARNING_GUIDE.md) | Beginner-friendly study guide explaining all concepts and 10 interview Q&As. |

---

## 5. Summary of Sample Proposal Evaluation Results

| Proposal | Bidder Name | Status | Rank | Score | Summary Rationale |
| :---: | :--- | :---: | :---: | :---: | :--- |
| **A** | Northshore Solar Ltd. | `ranked` | **1** | **97** | Top commercial experience (14 installs), compliant $238k price, 12-wk timeline, complete safety and 12-mo monitoring plan. |
| **E** | SunGrid Contractors Inc. | `ranked` | **2** | **90** | Outstanding 12-yr workmanship warranty, comprehensive safety and 24-mo monitoring plan, competitive $242.5k price. |
| **C** | Lakeview Renewables Corp. | `ranked` | **3** | **81** | Compliant with solid track record and 10-yr warranty, but higher price ($244k), longer schedule (14 wks), and no monitoring plan. Prompt injection safely ignored. |
| **B** | BrightPath Energy Inc. | `flagged` | `null` | `null` | Bidder does not hold the mandatory Ontario electrical contractor licence; proposes an unconfirmed subcontractor post-award. |
| **D** | Essex Green Power Inc. | `flagged` | `null` | `null` | Completion timeline is uncommitted (deferred to site assessment); cannot verify compliance with 16-week requirement. |

---

## 6. Candidate to Complete Before Submission

> **Notice:** The sections below reflect the candidate's actual review, personal verification, and AI-use disclosure.

### A. Time Actually Spent
- **Total Time:** ~75 minutes (including brief analysis, workflow design, scoring policy formulation, prompt engineering, diagram generation, prototype script validation, and interview preparation review).

### B. AI Tools Disclosure
> *"I used AI as an architectural sounding board to draft initial prompt structures and brainstorm edge-case risk scenarios. All architectural decisions, scoring boundaries, prompt injection safeguards, and written explanations were personally reviewed, validated, and refined by me to ensure full compliance with the assessment brief."*

### C. Example of Reviewing and Correcting AI Output
> *"During the design process, the AI initially attempted to award Proposal B a high score due to its low price ($189,500), while treating the subcontractor licence as a minor detail that could be confirmed later. I caught this and corrected it: the assessment brief explicitly states that the licence must be 'held by the bidder and stated in the proposal'. Allowing an unconfirmed subcontractor to bypass mandatory compliance would violate legal and procurement safety standards. I updated the policy and code so that Proposal B is strictly marked as `flagged` with a null rank and score until a human reviewer clarifies the subcontractor's credentials."*

---

## 7. How to Run the Prototype (Extra Credit)

To run the deterministic triage script and reproduce `ranking.json`:

```bash
# Using standard Python 3
python prototype/triage.py

# On Windows
py prototype/triage.py
```
