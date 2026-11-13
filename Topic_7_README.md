# Topic 7 - Advanced Planning, Reasoning, and Self-Correction

## Project
**Workflow:** `Topic_7_AI_Self_Correction_Agent`  
**Platform:** n8n  
**Model:** Google Gemini Chat Model  
**Outcome:** Demonstrated a failed JSON validation, AI correction, successful revalidation, and structured final output.

## Objective
Build an AI Agent that converts order details into a JSON string containing `order_id` and `price`, validates syntax and required keys with a JavaScript Code Tool, retries after errors, and returns a structured result.

## Architecture
`When chat message received` -> `AI Agent`  
AI Agent sub-nodes: Google Gemini Chat Model (LLM), `schema_validator` (Code Tool), Structured Output Parser (final response). The parser also had a Gemini model attached for its auto-fix configuration during testing.

## Configuration
- Chat input: `{{ $json.chatInput }}`
- AI Agent maximum iterations: **4**
- Code Tool name: `schema_validator`
- Code Tool validation: `JSON.parse(query)` followed by checks for `order_id` and `price`.
- Output fields: `json_data` (string), `reflection_loops` (number), `validation_status` (string).
- Gemini credentials are configured inside n8n; no keys are included in this submission.

## Test procedure
1. Send order details for order 102, product Phone, price 500.
2. For the controlled self-correction test, instruct the agent to submit invalid JSON with unquoted property names first.
3. Inspect `schema_validator` Run 1: invalid input `{order_id: 102, price: 500}` returns `Validation Failed` with a JSON syntax error.
4. Inspect `schema_validator` Run 2: corrected input `{"order_id":102,"price":500}` returns `Validation Passed: JSON is valid and complete.`
5. Inspect the AI Agent output: `json_data` contains order 102 and price 500; `reflection_loops` is 2; `validation_status` is `Passed`.

## Observed result
| Check | Result |
|---|---|
| Invalid draft detected | PASS |
| Error feedback returned | PASS |
| Corrected draft validated | PASS |
| Final structured output | PASS |
| Reflection count | 2 |
| Max iterations configured | 4 |

## Evidence
See `Topic_7_Evidence.docx` for screenshots of the workflow canvas, failed validator call, successful validator call, and final agent output/settings.

## Important note
The invalid first draft was deliberately requested to demonstrate recovery. Restore the normal system prompt after the assessment so the agent does not intentionally produce malformed JSON. The screenshots establish the observed run, not guaranteed behavior on every future execution.
