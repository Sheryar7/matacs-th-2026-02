# AI Proposal Triage Design: Technical Write-Up

**Candidate:** Sheryar Khan | **Reference:** MATACS-TH-2026-02 | **Role:** Associate Software Developer

---

### Question 1: AI vs. Rules

This design separates tasks involving natural language interpretation from tasks requiring deterministic rule enforcement:

| Workflow Step | Responsibility | Rationale |
| :--- | :--- | :--- |
| **Criteria Setup** | Application Code & Database *(Proposed)* | Criteria and weights must be structured, validated, and versioned. |
| **Fact Extraction** | AI / LLM *(Proposed Production)* | Proposal documents have varied formats. LLMs parse free text into structured JSON fields. |
| **Evidence Quotes** | AI extracts; human/code verifies *(Proposed)* | Verbatim excerpts help reviewers locate proof, but require verification against the PDF. |
| **Prompt-Injection Defence** | Prompt Design & Validation *(Proposed)* | Proposal text must be treated as untrusted data, not as system instructions. |
| **Mandatory Checks** | Deterministic Code *(Implemented in Prototype)* | Code applies licence, WSIB, insurance, and budget rules consistently without hallucinating facts. |
| **Weighted Scoring** | Deterministic Code *(Implemented in Prototype)* | Fixed mathematical formulas make scoring repeatable, auditable, and transparent. |
| **Ranking & Sorting** | Deterministic Code *(Implemented in Prototype)* | Orders eligible proposals reliably by total score. |
| **Reviewer Summaries** | AI / Template *(Proposed Production)* | Synthesizes concise briefing notes based strictly on verified facts and flags. |
| **Verification & Award** | Human Reviewer & Procurement Officer | Humans investigate flags, resolve uncertainties, and make the legal award. |
| **Audit Logging** | Database & Code *(Proposed Production)* | Stores immutable records of criteria versions, raw inputs, scores, and reviewer actions. |

**Why draw the line here?**
LLMs excel at interpreting unstructured text, but can misread facts, hallucinate, or perform inconsistent arithmetic. Deterministic code applies defined rules and mathematical formulas without variance. However, code is only as reliable as its inputs: an unverified extracted fact can still produce an incorrect result.

*Implementation note:* The submitted Python prototype implements rule-based mandatory checks, scoring, ranking, and flagging using sample data. Live LLM extraction, database versioning, and immutable audit logging represent proposed production capabilities.

---

### Question 2: Consistency and Explainability

**Repeatability:**
Application code produces identical scores whenever it receives the same proposal data and configuration. In production, repeated AI extractions should be stabilized using low temperature (`0.0`), strict JSON schema validation, and persistent storage of verified extractions.

The scoring formula is:
$$\text{Weighted Points} = \left(\frac{\text{Criterion Score}}{100}\right) \times \text{Criterion Weight}$$
Criterion weights must sum to 100 points ($1.0$), which the prototype validates at startup.

**Explainability:**
Reviewers need complete visibility into every decision:
- An overall score and documented rationale for each evaluated proposal.
- Clear flags indicating why a proposal was unranked and what clarification is needed.
- In production, itemized point breakdowns per criterion and clickable source citations.

Under current prototype rules:
- **Proposal A** is ranked first (score: 97).
- **Proposal E** is ranked second (score: 90).
- **Proposal B** is flagged (bidder's own electrical contractor licence is unconfirmed).
- **Proposal C** is flagged (nine installations since 2020 does not establish how many fall within the required five-year window).
- **Proposal D** is flagged (completion timeline is uncommitted).

Flagged proposals receive null scores and ranks until missing evidence is resolved.

---

### Question 3: Human in the Loop and Auditability

The tool assists human evaluation; it never awards contracts independently.

**Human Review Checkpoints:**
1. **Resolve Flags:** Reviewers seek clarification for B's licence, C's experience dates, and D's completion schedule.
2. **Record Overrides:** If new evidence is accepted, an authorized reviewer updates the facts with a recorded justification, triggering automatic re-evaluation.
3. **Approve Award:** An authorized procurement officer reviews the final comparison and makes the award.

**Proposed Production Audit Log:**
A production implementation should permanently record:
- Criteria version and active weights snapshot.
- Cryptographic hash of the uploaded proposal file.
- Extracted facts, quotations, and calculation sheets.
- Reviewer identity, timestamps, and override justifications.
- Final human award sign-off.

---

### Question 4: Changing Criteria

If an administrator adjusts criteria (e.g., increasing Timeline weight from 15% to 20%):

1. **Version Immutability:** Historical evaluation runs remain locked under their original version identifier (e.g., `v1.0`).
2. **New Versioning:** Modified criteria are saved under an incremented version (e.g., `v2.0`).
3. **Instant Re-Scoring:** Previously verified proposal facts are re-scored under `v2.0` in milliseconds without calling the LLM again.
4. **Side-by-Side Comparison:** Reviewers compare `v1.0` and `v2.0` results side-by-side to observe ranking shifts.

---

### Question 5: Risks and Safeguards

1. **Extraction Errors & Hallucinations:**
   *Risk:* AI misreads figures or hallucinates credentials.
   *Safeguard:* Prompts require verbatim quotes; missing data must be returned as null. Reviewers verify quotes against the source PDF.
2. **Prompt Injection & Manipulation:**
   *Risk:* Bidders inject hidden instructions (e.g., Proposal C's Appendix C claiming pre-approval and demanding full marks).
   *Safeguard:* Proposal text is enclosed in `<untrusted_proposal_document>` delimiters. Prompts order the model to treat text strictly as passive data and ignore internal commands. The prototype scores only validated facts, rendering the injection harmless.
3. **Missing or Ambiguous Information:**
   *Risk:* System guesses unstated facts and treats bidders unfairly.
   *Safeguard:* System refuses to guess. Proposals B, C, and D are marked `status: "flagged"` with null rank and score for human clarification.
4. **Scoring Bias:**
   *Risk:* Inconsistent evaluation across vendors.
   *Safeguard:* Transparent, published mathematical formulas applied uniformly by code.
5. **Cost & Latency:**
   *Risk:* Repetitive, expensive LLM calls for multi-page documents.
   *Safeguard:* Single extraction call per document; structured facts are stored to allow unlimited instant re-evaluations at zero AI cost.
