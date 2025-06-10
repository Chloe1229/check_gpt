📌\[Declaration]
This specification defines the instruction set for Codex to generate a fully hardcoded implementation of step7 and step8. These steps must be executed directly after step6 without modifying any existing data structures, variables, or session keys used in step1\_to\_6.py.

The objective is to convert structured data, predefined conditional logic, and template field mappings into deterministic, fully hardcoded code blocks that require no user inference or external dependency.

This replaces and extends the original instruction, which was limited to implementing only step7. Codex is now required to fully implement both:

* Step 7: Evaluation logic and visual output generation grouped by title\_key
* Step 8: Final document rendering logic in PDF format based on official templates

The following reference files are mandatory and authoritative:

* step7\_data\_refac.xlsx:
  Contains finalized logic expressions and output text blocks for each condition. These expressions are ready-to-evaluate and must be inserted directly.

* step1\_to\_6.py:
  Defines all inputs and stored session values. Keys from step6 must be reused exactly.

* step6\_used\_key\_info.csv:
  Provides the list of valid keys used in step6. This is for verification only and must not be altered.

* Step8 filled template reference (Excel/Word):
  Contains the field-by-field layout and expected values for each section of the generated application. Codex must map outputs to this layout exactly as shown.

Key instructions for Codex:

* Step7 logic must evaluate each row in the dataset and apply exact if-condition matching using output\_condition\_all\_met.
* Matching rows should generate structured output (output\_1\_text, output\_2\_text) grouped by title\_key.
* Step8 must paginate one page per title\_key with results, populating each output field in strict alignment with the filled template.
* All formatting, spacing, and expressions must be preserved 1:1. No summaries, abstractions, or derivations allowed.

This declaration overrides all prior instructions. Codex must treat this document as the binding task definition for full implementation of both step7 and step8.

────────────────────────────────────────────────────────────────────────────
📌\[Purpose of This Specification]
The purpose of this document is to instruct Codex to generate a fully hardcoded implementation of both step7 and step8 logic, fully aligned with the following constraints:

1. User selection state stored in st.session\_state.step6\_selections (as implemented in step1\_to\_6.py)
2. Condition expressions and outputs explicitly defined in step7\_data\_refac.xlsx
3. Key definitions validated exclusively through step6\_used\_key\_info.csv
4. Output templates and form mappings based on the filled example provided for step8 layout

In step7, Codex must evaluate prior step6 inputs and match them against prewritten conditional rules to determine whether a result should be displayed. This logic is deterministic and must be implemented through explicit condition evaluation from output\_condition\_all\_met. Grouping and pagination are based on title\_key, with results shown in full if any condition matches.

In step8, Codex must use the results of step7 to populate a formal application document using an official government layout. One page per title\_key must be generated. The form must follow exact formatting, layout, field structure, and vocabulary as defined in the official template. All outputs must match the example values and formatting in the filled reference.

This specification provides the full authority for Codex to implement both step7 and step8 as an uninterrupted, fully hardcoded continuation of step6 logic without deviation or inference.

──────────────────────────────────────── Step 7 Specification (Codex-ready) ────────────────────────────────────────

Step 7 must be implemented using a fully deterministic, hardcoded logic path. Codex is required to process all 75 individual evaluation cases from step7\_data\_refac.xlsx using explicit if-conditions derived from the column output\_condition\_all\_met. Each row represents one atomic decision rule. When the condition is satisfied, its associated outputs (output\_1\_text, output\_2\_text, output\_1\_tag) must be displayed.

Grouping:

* Each row is assigned to a group identified by title\_key.
* Conditions must be evaluated independently but grouped together under their common title\_key.
* If any condition within the group evaluates to True, its result must be shown alongside others on the same page.
* If no results are triggered for that title\_key, a fallback message must be shown exactly once.

Page logic:

* One page is rendered per title\_key value in step6\_targets.
* Each page must display the subtitle (step6\_items\[title\_key]\["title"]) and all matched results.
* Each output must be shown using full markdown rendering including line breaks, numbering, and formatting preserved from Excel.

Evaluation:

* The expression in output\_condition\_all\_met must be evaluated exactly using Python’s eval():
  eval(expression, {}, {"step6\_selections": step6\_selections})
* No transformation or parsing of the logic string is allowed.

Examples:

* If a condition states that step6\_selections.get("s2\_2\_req\_3") == "충족", and that condition is met, the corresponding output\_1\_text and output\_2\_text must be rendered in full.
* Results are collected into step7\_results\[title\_key] = \[(tag, output1, output2), ...] for use in step8.

The fallback message shown when no conditions match:
해당 변경사항에 대한 충족조건을 고려하였을 때,
「의약품 허가 후 제조방법 변경관리 가이드라인」에서 제시하고 있는
범위에 해당하지 않는 것으로 확인됩니다

⚠ All formatting and structure for matched outputs must follow exactly what is defined in the Excel rows. Do not paraphrase or simplify any string.

Hardcoding must be preserved at all times.

In STEP7, the 75 independently evaluated cases must be regrouped by title\_key and the results generated per title\_key grouping using fully hardcoded logic. Focus only on this task. Any additional enhancements will be considered later.

Even if one or more of the grouped cases under a title\_key produces results, when none match, the fallback guidance message must be shown. Steps 1 to 6 are functioning correctly and must not be modified.

Each individual case sharing the same title\_key must be evaluated independently, and their respective outputs collected under a common title\_key grouping.

Rendering logic for each of the 75 condition cases according to paging rules:
Each of the 75 condition cases must store their evaluation results independently.
However, based on paging rules, conditions with the same title\_key are grouped into a single screen. If multiple conditions produce results, all outputs are displayed in parallel on the same page.
If no condition under a given title\_key produces a result, then a fallback message must be shown.

IMPORTANT: A detailed example is included at the end of this specification.

This logic must be applied to all 24 defined title\_key groupings.

──────────────────────────────────────── Step 8 Specification (Codex-ready) ────────────────────────────────────────

Step 8 uses the results from step7 to auto-populate an official PDF application form following the exact template defined in step8\_양식.docx and step8프롬프트\_출력서식.docx.

Page generation:

* Each title\_key with any valid result from step7 must generate one full page.
* Pages must repeat based on the number of title\_keys in step7\_results.

Form population rules:

* Section 1 (신청인): Leave blank.
* Section 2 (변경유형): Use title\_text from the matched title\_key.
* Section 3 (신청유형): Use the first line of output\_1\_text (e.g., “IR”, “Cmaj”, etc.).
* Section 4 (충족조건): For each requirement from step6\_selections linked to that title\_key:

  * List all keys used (e.g., s2\_2\_req\_1 \~ req\_10) with their literal prompt text.
  * Next to each, show “○” if value is “충족”, “×” if “미충족”.
* Section 5 (필요서류):

  * From output\_2\_text, split by line breaks.
  * For each line, enter in order and leave 구비여부 and page fields blank for manual input.

Output format:

* PDF must be generated with fixed layout matching the original Word template.
* Fonts, spacing, cell widths, numbering, and line breaks must be identical.
* File name: 신청양식\_{title\_key}\_{YYYYMMDD}.pdf
* PDF must be read-only, non-editable.

UI integration:

* On Step 8 page, show preview of filled forms with two buttons: \[파일 다운로드하기], \[인쇄하기].
* If no title\_keys yield a result, show only:
  신청양식 자동생성 불가: 도출 결과 없음

⚠ Codex must strictly follow the filled example layout and cannot infer field usage. Every label, cell value, and layout structure must match what is defined in the reference Excel and Word forms.

Step 8 implements the final output document: a fully formatted, printable application form based on the official 의약품 허가 후 제조방법 변경관리 가이드라인 template.

Purpose:

* Convert all final outputs from step6/step7 into the fixed layout of the government application form.
* Generate a read-only PDF using the official template, with data auto-filled from earlier steps.

Key Features:

* Each title\_key with valid results generates one full page of output (step8 pagination must follow title\_key).

* Form is populated with:

  * Section 2: Upper/mid/lower item from title\_text.
  * Section 3: 보고유형 from output\_1\_text.
  * Section 4: All condition keys from step6 shown with ○ (충족) or × (미충족).
  * Section 5: Required documents (output\_2\_text), listed in full by line with their checkmark status left blank.

* Section 1 (신청인), and any blank placeholders in the template, must remain empty for manual user input.

UI Rules:

* Dedicated page for form preview.
* Two fixed buttons: \[파일 다운로드하기] and \[인쇄하기].
* PDF must exactly match the font, spacing, layout, and phrasing of the attached Word/Template image.

Repeat Generation:

* Multiple title\_key results → repeat template on new pages.
* No valid results → print default page with message: 신청양식 자동생성 불가: 도출 결과 없음.

PDF Output:

* File name format: 신청양식\_{title\_key}\_{YYYYMMDD}.pdf.
* Must be read-only and uneditable.

⚠ All formatting, order, spacing, and phrasing must match the official template precisely.

Codex must render step8 only after all step7 results are finalized, using step7\_results as the sole data source.

Output fields must follow the structure defined in the filled version of the template Excel provided. Codex must extract the field mappings directly from the filled template structure and not infer or omit any logic.
