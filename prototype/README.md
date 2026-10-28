# Prototype: Deterministic Scoring & Triage Engine (Extra Credit)

This directory contains a standalone Python script demonstrating how the **AI Proposal Triage Tool** evaluates extracted proposal data, applies mandatory pass/fail rules, calculates weighted scores, handles edge cases, and generates the final triage JSON.

## Features Demonstrated
1. **Configurable Criteria:** Loads admin-configured weights and threshold rules (not hard-coded into logic).
2. **Mandatory Compliance Gate:** Verifies electrical licence holder status, WSIB, insurance, and budget.
3. **Edge Case Triage:**
   - **Proposal B:** Identifies missing bidder licence (subcontractor deferred post-award) -> flags without guessing.
   - **Proposal D:** Identifies missing completion timeline (TBD after site visit) -> flags without guessing.
   - **Proposal C:** Logs prompt injection detection while evaluating real merits impartially.
4. **Deterministic Math:** Computes repeatable, auditable scores for eligible proposals (A: 97, E: 90, C: 81).
5. **JSON Output:** Emits the exact structure required by `ranking.json`.

## How to Run

No external dependencies or API keys are required to run this prototype.

From the repository root or the prototype directory:
```bash
python prototype/triage.py
```
*(On Windows, you can also run `py prototype/triage.py`)*

Output will print directly to the terminal as a validated JSON array matching `ranking.json`.
