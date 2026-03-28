# DATA 305 - Assignment 3: AI-Powered Assessment Grading

## 1. Assumption Analysis
Before implementing the final pipeline, several assumptions were made regarding the grading process:
*   **Format Strictness for Multiple Choice:** We assumed that while a human might accept "String" for "b", an AI grader for a formal assessment should enforce the use of the letter (or letter + text) to ensure the student followed the test instructions.
*   **Good-Faith RESTatement:** We assumed that a student restating the question as code (e.g., `my_list[2] = 30`) demonstrates clear understanding and should receive full credit, even if the rubric expects just the value `30`.
*   **Metadata Attacks:** We assumed that Tier 3 students would attempt "Sneaky JSON" injections, wrapping their answers in fake grading code (e.g. `{"correct": true}`) to hijack the parser.

## 2. Working Implementation
The final implementation uses a Python pipeline that combines strict Regex validation for Multiple Choice with semantic pattern matching for Open Text. It includes a multi-layered security guardrail (`detect_injection`) and a "Conflict Defense" that penalizes students who attempt to "shotgun" multiple answers (providing both right and wrong keywords).

## 3. Complete Prompt & Revision Logs
*(See the accompanying `Prompt_Log.md` file for the full turn-by-turn history).*
*   **Iteration 1:** Basic Regex keyword matching (13/26 Accuracy).
*   **Iteration 2:** Added basic sanitization and typo tolerance (18/26 Accuracy).
*   **Iteration 3:** Audited against official `expected_scores`. Identified mathematical errors in the professor's key (e.g., basic_007).
*   **Iteration 4:** Implemented strict format enforcement for MC and Conflict Detection for shotgunning.
*   **Iteration 5:** Hardened security against JSON metadata attacks in Tier 3 (23/26 Audited Accuracy).

## 4. Evaluation
*   **Strengths:** The system is remarkably secure against adversarial input. It successfully blocks 100% of JSON-injection and System Override attempts. It also provides "Discrepancy Proofs" that identify errors in the baseline dataset.
*   **Weaknesses:** Without a live LLM API, the system relies on predefined conflict lists. A live LLM would handle "shotgunning" even more naturally across edge cases.

## 5. Reflection
This project highlighted the tension between **Technical Correctness** and **Rubric Matching**. I learned that building a grader isn't just about finding the right word; it's about defining the "Philosophy" of the classroom. For example, deciding to accept `my_list[2] = 30` was a choice to value student intent over strict output matching. Similarly, identifying the mathematical errors in the professor's provided `expected_scores` proved that "Hard Logic" (code) is often more reliable than "Soft Logic" (LLM-generated baselines).

## 6. AI Use Disclosure
**Generative AI Tool Used:** Gemini CLI
**How it was used:** 
1. Gemini acted as a pair-programmer to write the Python logic for the grading pipeline.
2. Gemini was used to perform an audit of the `test-submissions.json` file to compare actual student performance against the `expected_scores` list.
3. Gemini helped format the "Discrepancy Proof" logs to make the final output more readable.
