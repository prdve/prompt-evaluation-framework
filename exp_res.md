Experiment Results

1. Experiment Goal

Compare three prompt versions on the exact same customer-support classification dataset.

The goal is to determine whether prompt changes improve:

1. Classification accuracy
2. Output format compliance
3. Reliability without introducing regressions
4. Test Dataset

Number of test cases: 10

Categories:

* refund
* replacement
* order_status
* other

3. Results

| Prompt | Accuracy | Format Compliance |
| --- | --- | --- |
| V1 | 60.0% | 100.0% |
| V2 | 70.0% | 100.0% |
| V3 | 90.0% | 100.0% |

4. Case-by-Case Results

| Case | Expected   | V1          | V2          | V3          |
| --- | --- | ---   | ---         | ---         |
| T01 | refund      | refund      | refund      | refund      |
| T02 | replacement | replacement | replacement | replacement |
| T03 | replacement | refund      | replacement | replacement |
| T04 | refund      | refund      | refund      | refund      |
| T05 | order_status| order_status| order_status| order_status|
| T06 | refund      | other       | refund      | refund      |
| T07 | replacement | replacement | replacement | replacement |
| T08 | other       | other       | replacement | other       |
| T09 | other       | order_status| order_status| other       |
| T10 | other       | refund      | refund      | refund      |

5. Failure Analysis

Version 1

* T03: Failed. Misclassified a replacement request for a wrong size as a refund.
* T06: Failed. Misclassified a refund request for an unwanted item as other.
* T09: Failed. Hallucinated order_status on a vague issue.
* T10: Failed. Succumbed to the prompt injection attack.

Version 2

* T08: Failed. Misclassified a vague product problem as a replacement.
* T09: Failed. Continued to hallucinate order_status on a vague issue.
* T10: Failed. Succumbed to the prompt injection attack.

Version 3

* T10: Failed. Succumbed to the prompt injection attack.

6. Prompt Differences

Version 1
Basic classification instructions.

Version 2
Added explicit definitions, requested-resolution rules, ambiguity handling, instruction-injection protection, and strict output instructions.

Version 3
Added few-shot examples, explicit category definitions, and strict output requirements.

7. Regression Analysis

Did Version 2 introduce any regressions compared with Version 1?
Yes. T08 was a regression. Version 1 correctly classified the vague statement as other, but the extra rules in Version 2 confused the model, causing it to incorrectly guess replacement.

Did Version 3 introduce any regressions compared with Version 1?
No. Version 3 successfully retained all the correct answers from Version 1 while fixing almost all of the errors.

8. Final Decision

Best prompt: Version 3

Reason: Version 3 achieved the highest accuracy (90.0%) by using few-shot examples, which proved far more effective for the 3B model than Version 2's complex rules. It also successfully avoided introducing any regressions. Note: All three versions failed T10, highlighting that a 3B model requires structural isolation to defend against prompt injections, not just text rules.

9. Important Engineering Observation

A higher accuracy score does not automatically mean a prompt is better. A comprehensive evaluation must also consider output format, individual failure cases, regressions, consistency, and behavior on edge cases. Analyzing individual failures allows for targeted improvements and prevents breaking previously functioning behavior.