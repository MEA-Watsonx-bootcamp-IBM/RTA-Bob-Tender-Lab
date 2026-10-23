---
name: tender-report
description: Generates the tender validation report as an HTML file from the fixed template. Use whenever the user asks for a validation report, an HTML report, or to save or export tender validation findings.
---

# Tender validation report

Produce the report by filling the fixed template `report_template.html` in this skill's folder. Never design your own layout.

## Steps

1. Read `report_template.html` from this skill's folder.
2. Make sure you have all findings for the tender(s) the user named. If a check has not been run yet in this conversation, run it first:
   - key information extraction
   - completeness, against the rules in `.bob/rules-tender-validator/`
   - regulatory compliance, against the regulation in `reference/`
   - Arabic-English consistency, only if both language versions are being validated
3. Fill the template:
   - Replace every `{{PLACEHOLDER}}`. Use the tender's own reference, title and entity.
   - For each `<!-- REPEAT:name -->` block, write one copy per row, then remove the example row. Keep the REPEAT/END comments.
   - Key information rows: estimated value, advance payment, bid validity, bid security, performance guarantee, delay penalty, delivery period, publication date, closing date, clarification deadline.
   - Completeness: all 16 required sections, in order.
   - Compliance: one row per applicable regulation article (at least Art. 6, 7, 8, 10, 11, 12, 13, 14, 15, 18). If the tender has no clause for an article, set Tender says to "Not found in the document" and the status to Missing.
   - Use only the badge classes `present`, `missing`, `compliant`, `noncompliant`, `mismatch`. In the bilingual table, label matching rows "Match" with the `present` class.
   - Bilingual rows: always compare estimated value, advance payment, bid validity, closing date, delivery period, delay penalty, and every clause that exists in only one version.
   - Wrap every piece of Arabic text in `<span class="ar" dir="rtl" lang="ar">...</span>`.
   - If only one language version was validated, delete the `bilingual` section and set `{{MISMATCH_COUNT}}` to "n/a".
4. Verdict: `ready` / "READY FOR RELEASE" only if there are zero Missing, Non-compliant and Mismatch findings. Otherwise `notready` / "NOT READY FOR RELEASE", and give the counts in the summary.
5. Save the file as `output/validation_report_<variant>.html`, where `<variant>` is the tender's input folder name (for example `defective` or `clean`).
6. Keep the file self-contained: no external CSS, scripts, fonts or images.
7. Tell the user the file path and the verdict in one or two sentences.
