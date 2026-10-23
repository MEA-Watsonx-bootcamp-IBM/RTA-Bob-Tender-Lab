# Smart Tender Validation with IBM Bob

**Hands-on lab guide.** Build a Tender Validator that checks a government tender for completeness, regulatory compliance and Arabic–English consistency.

> Prefer a printable copy? The same guide is available as a PDF: [Lab-Guide.pdf](Lab-Guide.pdf).

Before a government tender is published, someone has to confirm three things:

- it contains every mandatory section;
- it complies with the procurement regulation;
- its Arabic and English versions say exactly the same thing.

Done by hand, this means hours of side-by-side reading, and small errors can slip through.

In this lab you teach **IBM Bob** to do this check for you. You build a **Tender Validator** that applies your organization's checklist and cites the regulation for every finding. It compares both language versions, then produces a standard validation report. It is also prevented from changing the documents it reviews.

| | |
|---|---|
| **Duration** | About 80 minutes |
| **Level** | Beginner. No coding required |
| **You will build** | A custom Bob mode, a rule file, a skill and a validation report |
| **Languages** | English and Arabic |

## Contents

- [What you will learn](#what-you-will-learn)
- [Before you start](#before-you-start)
- [How to use this guide](#how-to-use-this-guide)
- [The scenario](#the-scenario)
- [Lab at a glance](#lab-at-a-glance)
- [Exercise 0: Open the lab repository](#exercise-0-open-the-lab-repository)
- [Exercise 1: Create the Tender Validator mode](#exercise-1-create-the-tender-validator-mode)
- [Exercise 2: Read the tender PDF](#exercise-2-read-the-tender-pdf)
- [Exercise 3: Extract and summarize a section](#exercise-3-extract-and-summarize-a-section)
- [Exercise 4: Check completeness with a rule file](#exercise-4-check-completeness-with-a-rule-file)
- [Exercise 5: Check regulatory compliance](#exercise-5-check-regulatory-compliance)
- [Exercise 6: Work bilingually](#exercise-6-work-bilingually)
- [Exercise 7: Create the report skill and generate the report](#exercise-7-create-the-report-skill-and-generate-the-report)
- [Exercise 8: Test the guardrail](#exercise-8-test-the-guardrail)
- [Exercise 9: Validate the clean tender](#exercise-9-validate-the-clean-tender)
- [Wrap-up](#wrap-up)
- [Troubleshooting](#troubleshooting)
- [Appendix: example validation report](#appendix-example-validation-report)

## What you will learn

By the end of this lab you will be able to:

- **Create a custom mode** that gives Bob a specialist role, working instructions and a limited set of tools.
- **Add a guardrail** so that Bob can write reports but cannot alter the source documents.
- **Encode an organizational checklist** as a rule file that Bob applies the same way for every user.
- **Ground every finding** in a quoted clause and a cited article of the regulation.
- **Work bilingually**: apply English rules to an Arabic document, ask questions in Arabic and find EN–AR discrepancies.
- **Create a skill** that produces a consistent, branded HTML report from a fixed template.

## Before you start

You need:

- **IBM Bob** installed and signed in.
- **This repository**, cloned or downloaded (**Code > Download ZIP**) and unzipped on your machine.
- A **web browser**, to view the generated reports.

## How to use this guide

- Each exercise lists its steps and gives the prompt to send to Bob in a code box. Copy the prompt exactly.
- Then compare Bob's answer with **What you should see**.
- **Why it matters** explains how the step applies to real procurement work.

> [!TIP]
> **About AI results.** Bob writes its own wording each time, so your answers will not match this guide word for word. What matters is that the **facts** match: the numbers, sections, articles and verdicts listed under *What you should see*. Bob may sometimes add extra relevant observations. That is expected.

## The scenario

You are a **procurement officer** at a UAE government transport authority. The *City Transport Authority (CTA)* is about to publish tender **CTA/2026/T-0147, Supply, Installation and Maintenance of Smart Bus Shelters**. Your job is to confirm that the tender is ready for release.

You first validate a **defective draft**. It contains several problems for you and Bob to find. Then you confirm that the corrected, **clean** version passes.

| Document | Location in the repository |
|---|---|
| Defective tender, English and Arabic | `documents/defective-tender/tender_EN.pdf`, `documents/defective-tender/tender_AR.pdf` |
| Clean tender, English and Arabic | `documents/clean-tender/tender_EN.pdf`, `documents/clean-tender/tender_AR.pdf` |
| Government Procurement and Contracts Regulation (21 articles) | `documents/procurement_regulation.pdf` |
| Report template | `templates/report_template.html` |

Bob creates an `output/` folder for your reports the first time it saves one.

## Lab at a glance

| # | Exercise | Documents | Time |
|---|---|---|---|
| 0 | Open the lab repository | - | 3 min |
| 1 | Create the Tender Validator mode | - | 12 min |
| 2 | Read the tender PDF | Defective EN | 4 min |
| 3 | Extract and summarize a section | Defective EN | 6 min |
| 4 | Check completeness with a rule file | Defective EN | 10 min |
| 5 | Check regulatory compliance | Defective EN + regulation | 8 min |
| 6 | Work bilingually | Defective EN + AR | 12 min |
| 7 | Create the report skill and generate the report | Defective EN + AR | 12 min |
| 8 | Test the guardrail | Defective EN | 5 min |
| 9 | Validate the clean tender | Clean EN + AR | 7 min |

---

## Exercise 0: Open the lab repository

⏱ 3 minutes

**Goal:** open the lab repository in Bob.

1. Clone or download the lab repository to your machine.
2. In Bob, choose **File > Open Folder**, select the repository folder, then click **Open**.

   > [!IMPORTANT]
   > Open the repository folder itself, the one that contains `documents` and `templates`, not a folder above it. Otherwise your mode, rules and skill are saved in the wrong place.

3. In the Explorer, check that you see:

   ```
   RTA-Bob-Tender-Lab/
   ├── documents/
   │   ├── clean-tender/
   │   │   ├── tender_AR.pdf
   │   │   └── tender_EN.pdf
   │   ├── defective-tender/
   │   │   ├── tender_AR.pdf
   │   │   └── tender_EN.pdf
   │   └── procurement_regulation.pdf
   ├── guide-images/
   ├── templates/
   │   └── report_template.html
   ├── LAB-GUIDE.md
   ├── Lab-Guide.pdf
   └── README.md
   ```

---

## Exercise 1: Create the Tender Validator mode

⏱ 12 minutes

**Goal:** create a specialist mode that sets out:

- who Bob is (its role);
- how Bob must work (its instructions);
- what Bob is allowed to do (its tools).

1. In Bob, open **Settings > Modes** and click **Create new mode**.
2. Fill in the basic fields:

   | Field | Value |
   |---|---|
   | Name | `📋 Tender Validator` |
   | Slug | `tender-validator` |
   | Description | `Validates government tenders for completeness, compliance and EN/AR consistency.` |
   | Save location | **Project** |

3. In **Role definition**, paste:

   ```
   You are a senior procurement compliance officer at a UAE government transport authority. You review tender documents before they are published and check that they are complete, compliant with government procurement regulations, and consistent between their Arabic and English versions. You are fluent in Arabic and English and precise about numbers, dates and obligations.
   ```

4. In **When to use**, paste:

   ```
   Use this mode to extract information from tender documents, check them for completeness or regulatory compliance, compare Arabic and English versions, or produce a tender validation report.
   ```

5. In **Custom instructions**, paste:

   ```
   - The tenders and the regulation are in documents/. Treat them as read-only: never modify, rename or delete them.
   - Base every finding on the document text. Quote the relevant clause and give its section number or heading.
   - For compliance findings, cite the regulation article (for example "Art. 11") from documents/procurement_regulation.pdf.
   - Never guess. If information is not in the document, say "Not found in the document".
   - Use these statuses only: Present, Missing, Compliant, Non-compliant, Mismatch.
   - Always respond in the language the user writes in. When quoting tender clauses, quote them in their original language.
   - When asked for a report, use the tender-report skill and save the file in output/.
   ```

6. Under **Tools**, select **Read**, **Edit** and **Skill**. Do not select any other tool.
7. Restrict **Edit** to report files only. Set its file pattern to:

   | File regex | Description |
   |---|---|
   | `(^\|/)output/.*\.html$` | `HTML reports in output/ only` |

8. Click **Save**.
9. If the settings screen has no file pattern field, set the pattern in the mode's file instead:
   1. Open `.bob/custom_modes.yaml` in the repository.
   2. Make its `groups` section look exactly like this:

   ```yaml
       groups:
         - read
         - - edit
           - fileRegex: (^|/)output/.*\.html$
             description: HTML reports in output/ only
         - skill
   ```

10. Select **📋 Tender Validator** in the mode picker. Use this mode for every exercise that follows.

### What you should see

- **📋 Tender Validator** appears in the mode picker.
- A new file, `.bob/custom_modes.yaml`, appears in the repository.

**Why it matters:** this mode is your organization's reviewer, defined once and shared by every user. The instructions are in English, but they tell Bob to answer in the user's language, so a question asked in Arabic gets an answer in Arabic.

---

## Exercise 2: Read the tender PDF

⏱ 4 minutes

**Goal:** check that Bob can read a PDF tender directly from the repository.

Start a **new task** in 📋 Tender Validator mode and send:

```
Read @documents/defective-tender/tender_EN.pdf. List its section headings with their numbers, and confirm the tender reference, title and issuing entity.
```

### What you should see

- **13 numbered sections**:
  1. Tender Information
  2. Introduction and Background
  3. Scope of Work
  4. Technical Specifications
  5. Contract Duration and Delivery Schedule
  6. Eligibility and Qualification Requirements
  7. Submission Instructions
  8. Bid Validity
  9. Commercial Terms
  10. Performance Guarantee
  11. Delay Penalties
  12. Governing Law and Dispute Resolution
  13. Language
- Reference **CTA/2026/T-0147**.
- Title **Supply, Installation and Maintenance of Smart Bus Shelters**.
- Issued by the **City Transport Authority**.

You can open the PDF to compare:

<p>
  <img src="guide-images/tender-cover.png" alt="Tender cover page" height="280">
  <img src="guide-images/tender-section-1.png" alt="Section 1, Tender Information" height="280">
</p>

*The tender's cover page and Section 1.*

---

## Exercise 3: Extract and summarize a section

⏱ 6 minutes

**Goal:** pull structured data out of a section.

In the same task, send:

```
From the Commercial Terms section of @documents/defective-tender/tender_EN.pdf, extract the estimated value, currency, advance payment, payment milestones and payment period as a table. Then summarize the section in three sentences.
```

### What you should see

| Item | Value |
|---|---|
| Estimated value | 12,500,000, excluding VAT |
| Currency | AED |
| Advance payment | 40% of the contract value, payable on contract signature |
| Payment milestones | 30% on installation (pro rata) · 20% on final acceptance · 10% quarterly over the maintenance period |
| Payment period | Within 30 days of an approved invoice |

These are followed by a three-sentence summary.

![Section 9, Commercial Terms, English version](guide-images/commercial-terms-en.png)

*Section 9, Commercial Terms, as it appears in the tender. Keep the 40% advance payment in mind.*

---

## Exercise 4: Check completeness with a rule file

⏱ 10 minutes

**Goal:** give Bob your organization's checklist of mandatory sections, so it checks every tender the same way.

1. In the Explorer, inside the `.bob` folder, create a new folder named `rules-tender-validator`.

   > [!NOTE]
   > Bob loads rules for a mode from the folder `.bob/rules-<mode slug>/`. The name must match exactly.

2. In that folder, create a file named `01-completeness.md`.
3. Paste the following into the file and save it. The checklist lists:
   - the **16 required sections**;
   - what makes each section count as present;
   - how Bob must report the result.

   ```markdown
   # Tender completeness rules

   When asked to check a tender for completeness, check it against every required section below.

   ## Required sections

   | # | Required section | Counts as present when the tender states |
   |---|---|---|
   | 1 | Tender reference and title | The tender reference number and the tender title |
   | 2 | Issuing entity and contact | The issuing government entity and a contact point |
   | 3 | Scope of work | What the contractor must deliver |
   | 4 | Technical specifications | Measurable technical requirements |
   | 5 | Eligibility and qualification | Who may bid: licences, experience, certifications |
   | 6 | Submission instructions | How to submit, the closing date and the clarification deadline |
   | 7 | Bid validity period | How long bids stay valid, in days |
   | 8 | Bid security (bid bond) | The bid bond amount or percentage, form and validity |
   | 9 | Evaluation criteria | The criteria and their weightings |
   | 10 | Commercial and payment terms | Estimated value or pricing basis, advance payment and payment terms |
   | 11 | Performance guarantee | The guarantee percentage and validity |
   | 12 | Delay penalties | The penalty rate and maximum cap |
   | 13 | Contract duration and delivery schedule | The duration and key milestones |
   | 14 | Governing law and dispute resolution | The applicable law and how disputes are settled |
   | 15 | Language clause | The issued languages and which version prevails |
   | 16 | Approval | The approving official, approval date and approval reference |

   ## How to check

   - Match sections by **meaning, not heading text**. The tender may use different headings or be written in Arabic.
   - Mark each section **Present**, or **Missing** if it does not appear at all.
   - If a section exists but lacks what the third column requires, mark it **Missing** and say what is absent.
   - For each Present section, give the tender's section number and heading.
   - End with a count, for example "13 of 16 required sections present", and list the missing ones.
   ```

4. **Start a new task** in 📋 Tender Validator mode.

   > [!IMPORTANT]
   > Rules load when a task starts. If you continue in the old task, Bob does not see the new checklist.

5. Send:

   ```
   Check @documents/defective-tender/tender_EN.pdf for completeness.
   ```

### What you should see

- **13 of 16** required sections present.
- Three sections **Missing**:
  - **Bid security (bid bond)**
  - **Evaluation criteria**
  - **Approval**
- For each Present section, the tender section where Bob found it.

> [!NOTE]
> The 13 numbered sections in Exercise 2 and the 13 sections present here count different things. They match only by coincidence:
> - Section 2, *Introduction and Background*, is not on the checklist.
> - The first two checklist items are both covered by Section 1.

**Why it matters:** the rule file is your organization's checklist written down once. Every officer gets the same check every time, and you can update the checklist without retraining anyone.

---

## Exercise 5: Check regulatory compliance

⏱ 8 minutes

**Goal:** check the tender against the regulation, with every finding backed by a quote and an article number.

In the same task, send:

```
Check @documents/defective-tender/tender_EN.pdf against the regulation in @documents/procurement_regulation.pdf. For each applicable article, quote what the tender says and mark it Compliant, Non-compliant or Missing.
```

### What you should see

| Status | Article | Finding |
|---|---|---|
| Non-compliant | **Art. 8** | Published 1 November, closes 11 November 2026: 10 days, minimum 21 |
| Non-compliant | **Art. 11** | Bid validity 60 days, minimum 90 |
| Non-compliant | **Art. 14** | Advance payment 40% with no guarantee. The maximum is 20%, and a guarantee is required |
| Non-compliant | **Art. 18** | Delay penalty 0.5% per day, capped at 20%. The maximum is 0.1% per day and 10% in total |
| Missing | **Art. 6** | No approval, approval date or approval reference |
| Missing | **Art. 12** | No bid security |
| Missing | **Art. 15** | No evaluation criteria |
| Compliant | **Art. 10** | Clarification deadline 4 November, exactly 7 days before closing |
| Compliant | **Art. 13** | Performance guarantee 10%, valid until 90 days after final acceptance |
| Compliant | **Art. 17** | Payment within 30 days of an approved invoice |

Bob may also comment on a few related articles, such as Art. 5 (mandatory contents) or Art. 16 (two-stage evaluation). That is acceptable.

<img src="guide-images/regulation-articles-11-14.png" alt="Articles 11 to 14 of the regulation" width="600">

*Articles 11–14 of the regulation. Compare them with the tender's 60-day bid validity and 40% advance payment.*

> [!TIP]
> **Look closer:** a 4 November clarification deadline with an 11 November closing date can look too short. Art. 10 requires **at least 7 days**, and this is exactly 7, so it is compliant. A good validator should not flag it.

**Why it matters:** every finding comes with a quote from the tender and the article number. A reviewer can check any finding in seconds, and the result can be audited.

---

## Exercise 6: Work bilingually

⏱ 12 minutes

**Goal:**

- apply the same English checklist to the Arabic tender;
- ask a question in Arabic;
- find every difference between the two language versions.

### 6a. English rules on an Arabic tender

Start a **new task** in 📋 Tender Validator mode and send:

```
Check @documents/defective-tender/tender_AR.pdf for completeness.
```

**What you should see**

- **12 of 16** required sections present.
- The same three sections are missing as in English: bid security, evaluation criteria and approval.
- **The performance guarantee is also missing.** This is the first sign that the two versions differ.

### 6b. Ask in Arabic

In the same task, send this question:

```
ما هي نسبة الدفعة المقدمة في @documents/defective-tender/tender_AR.pdf، وهل تتوافق مع اللائحة في @documents/procurement_regulation.pdf ؟
```

*In English: "What is the advance payment percentage in the tender, and does it comply with the regulation?"*

> [!TIP]
> If the file references do not link, delete each `@...` path and type `@` to pick the file from the list.

**What you should see:** an answer **in Arabic** that says:

- the advance payment is **40%**;
- this exceeds the **20% maximum in Art. 14**;
- the tender asks for no advance payment guarantee, although Art. 14 requires one.

### 6c. Compare the two versions

In the same task, send:

```
Compare @documents/defective-tender/tender_EN.pdf with @documents/defective-tender/tender_AR.pdf. List every difference in values, percentages, dates, periods and obligations, and every clause that appears in only one version.
```

**What you should see:** three discrepancies, each a breach of Art. 7.

| Item | English | Arabic |
|---|---|---|
| Estimated contract value | AED 12,500,000 | AED 15,200,000 |
| Delivery period | 180 days | 120 days, in both the text and the milestone table |
| Performance guarantee clause | Present (10%) | Absent |

Everything else matches, including bid validity, advance payment, closing date and delay penalties.

![Section 9, Commercial Terms, Arabic version](guide-images/commercial-terms-ar.png)

*The same Commercial Terms section in Arabic. The estimated value is 15,200,000, against 12,500,000 in English.*

**Why it matters:**

- If the two versions disagree, the Arabic text prevails.
- A discrepancy between versions can change what bidders price and what the authority can enforce.
- Bob compares every value, date and clause, so none of them depends on a reader catching the difference.

---

## Exercise 7: Create the report skill and generate the report

⏱ 12 minutes

**Goal:** create a skill that turns all the findings into a standard report that can be shared.

### 7a. Create the tender-report skill

A skill is a reusable set of instructions that Bob loads when a task needs it. This one tells Bob to fill the fixed report template in `templates/report_template.html`, so every report has the same layout.

1. In Bob, open **Settings > Skills** and create a new skill.
2. Fill in the form:

   | Field | Value |
   |---|---|
   | Skill name | `tender-report` |
   | Description | `Generates the tender validation report as an HTML file from the fixed template. Use whenever the user asks for a validation report, an HTML report, or to save or export tender validation findings.` |
   | Allow Bob to use this skill | **On** |
   | Scope & location | **Workspace** |

3. In **Skill instructions**, paste:

   ```markdown
   # Tender validation report

   Produce the report by filling the fixed template `templates/report_template.html`.
   Never design your own layout.

   ## Steps

   1. Read `templates/report_template.html`.
   2. Make sure you have all findings for the tender(s) the user named. If a check has not been run
      yet in this conversation, run it first:
      - key information extraction
      - completeness, against the rules in `.bob/rules-tender-validator/`
      - regulatory compliance, against `documents/procurement_regulation.pdf`
      - Arabic-English consistency, only if both language versions are being validated
   3. Fill the template:
      - Replace every `{{PLACEHOLDER}}`. Use the tender's own reference, title and entity.
      - For each `<!-- REPEAT:name -->` block, write one copy per row, then remove the example row.
        Keep the REPEAT/END comments.
      - Key information rows: estimated value, advance payment, bid validity, bid security,
        performance guarantee, delay penalty, delivery period, publication date, closing date,
        clarification deadline.
      - Completeness: all 16 required sections, in order.
      - Compliance: one row per applicable regulation article (at least Art. 6, 7, 8, 10, 11, 12,
        13, 14, 15, 18). If the tender has no clause for an article, set Tender says to
        "Not found in the document" and the status to Missing.
      - Use only the badge classes `present`, `missing`, `compliant`, `noncompliant`, `mismatch`.
        In the bilingual table, label matching rows "Match" with the `present` class.
      - Bilingual rows: always compare estimated value, advance payment, bid validity, closing
        date, delivery period, delay penalty, and every clause that exists in only one version.
      - Wrap every piece of Arabic text in `<span class="ar" dir="rtl" lang="ar">...</span>`.
      - If only one language version was validated, delete the `bilingual` section and set
        `{{MISMATCH_COUNT}}` to "n/a".
   4. Verdict: `ready` / "READY FOR RELEASE" only if there are zero Missing, Non-compliant and
      Mismatch findings. Otherwise `notready` / "NOT READY FOR RELEASE", and give the counts in
      the summary.
   5. Save the file as `output/validation_report_<variant>.html`, where `<variant>` is `defective`
      for tenders in `documents/defective-tender/` and `clean` for tenders in
      `documents/clean-tender/`. Create the `output/` folder if it does not exist.
   6. Keep the file self-contained: no external CSS, scripts, fonts or images.
   7. Tell the user the file path and the verdict in one or two sentences.
   ```

4. Save the skill.

**What you should see:** **tender-report** appears in the list of skills.

### 7b. Generate the report

Start a **new task** in 📋 Tender Validator mode and send:

```
Generate the validation report for the defective tender, covering both the English and Arabic versions.
```

> [!NOTE]
> This is a new task, so the skill first reruns the checks from Exercises 2–6 (its step 2). This takes a minute or two.

Bob then:

1. loads the **tender-report** skill;
2. runs any checks it does not have yet;
3. reads the fixed template;
4. fills in every section;
5. saves the report.

When it finishes, open `output/validation_report_defective.html` in your browser.

### What you should see

- The verdict **NOT READY FOR RELEASE**.
- **13 / 16** sections present.
- **3** missing sections.
- **4** regulatory violations.
- **3** EN–AR mismatches.
- Five sections:
  1. Key information
  2. Completeness
  3. Regulatory compliance
  4. Arabic–English consistency, with the Arabic text shown right to left
  5. Required actions before release

> [!NOTE]
> If Bob also flagged Art. 5 in Exercise 5, your report may show 5 violations instead of 4. Both results are correct.

![Top of the validation report](guide-images/report-top.png)

*Top of the validation report. [See the full report](#appendix-example-validation-report).*

**Why it matters:** the skill fixes the layout, so every report has the same structure no matter who runs the check. A manager can read any report the same way.

---

## Exercise 8: Test the guardrail

⏱ 5 minutes

**Goal:** confirm that the Tender Validator cannot change the documents it reviews.

In the same task, send:

```
Fix the bid validity in @documents/defective-tender/tender_EN.pdf to 120 days.
```

### What you should see

- Bob explains that in this mode it can write only HTML reports in `output/`, and leaves the tender unchanged.
- Bob may offer the correction as a recommended action instead.

**Go further (optional).** These two requests test the file pattern you set in Exercise 1:

```
Save a short summary of the findings as output/summary.md
```

```
Write a findings note to documents/notes.html
```

Bob should decline both:

- The first file is in `output/` but is not an HTML file.
- The second is an HTML file but is outside `output/`.

**Why it matters:** in government procurement, the tender is an official record. A validator that **cannot** alter the record is safer than one that is only told not to.

---

## Exercise 9: Validate the clean tender

⏱ 7 minutes

**Goal:** confirm that the corrected tender passes every check.

Start a **new task** in 📋 Tender Validator mode and send:

```
Run the full validation on @documents/clean-tender/tender_EN.pdf and @documents/clean-tender/tender_AR.pdf and generate the report.
```

Open `output/validation_report_clean.html` in your browser.

### What you should see

- The verdict **READY FOR RELEASE**.
- **16 / 16** sections present.
- **0** violations.
- **0** mismatches.

### What was corrected in the clean version

| Item | Defective draft | Clean version |
|---|---|---|
| Bid security | Missing | 2% bank guarantee, valid 150 days |
| Evaluation criteria | Missing | 70% technical / 30% financial, minimum technical score 70 |
| Approval | Missing | Approved 28 October 2026, ref. CTA-PCD-APR-2026-0912 |
| Closing date | 11 November 2026 (10 days) | 2 December 2026 (31 days) |
| Bid validity | 60 days | 120 days |
| Advance payment | 40% on signature, no guarantee | 10% against an equal guarantee |
| Delay penalty | 0.5% per day, capped at 20% | 0.1% per day, capped at 10% |
| Estimated value (Arabic) | AED 15,200,000 | AED 12,500,000 |
| Delivery period (Arabic) | 120 days | 180 days |
| Performance guarantee (Arabic) | Missing | Present |

---

## Wrap-up

### What you built

| Component | Where it lives | What it does |
|---|---|---|
| **Custom mode** | `.bob/custom_modes.yaml` | Defines the reviewer: its role, its working rules and its tools |
| **Guardrail** | The mode's Edit file pattern | Allows Bob to write HTML reports in `output/` only |
| **Rule file** | `.bob/rules-tender-validator/` | Your organization's completeness checklist |
| **Skill** | `.bob/skills/tender-report/` | Fills the report template the same way every time |
| **Report template** | `templates/report_template.html` | Your organization's fixed report layout |

Together these turn a general assistant into a specialist reviewer:

- it follows your process;
- it cites its sources;
- it works in both languages;
- it cannot change the records it checks.

### Take it further

The same pattern works for other document reviews. Swap in:

- **A different checklist:** edit the rule file for contracts, RFP responses, policies or job descriptions.
- **A different reference:** replace the regulation with your own standards, policies or service-level terms.
- **A different report:** change the skill's template to match your organization's format and branding.

---

## Troubleshooting

| What happens | What to do |
|---|---|
| Tender Validator is not in the mode picker | 1. Check that the mode was saved to **Project** and that `.bob/custom_modes.yaml` exists in the repository folder.<br>2. Reload the Bob window. |
| The completeness check ignores the 16-section checklist | 1. Check the path `.bob/rules-tender-validator/01-completeness.md`.<br>2. Start a **new task**. |
| Bob creates its own report layout | 1. Check that the **tender-report** skill exists, is allowed, and was saved to the **Workspace** ([Exercise 7a](#7a-create-the-tender-report-skill)).<br>2. Send: `Use the tender-report skill to generate the report.` |
| Bob writes a file outside `output/` | Open `.bob/custom_modes.yaml` and check the `fileRegex` under `edit` ([Exercise 1](#exercise-1-create-the-tender-validator-mode), step 9). |
| An `@` file reference does not link | Delete the path and type `@` to pick the file from the list. |
| Bob answers in English to an Arabic question | Check that the custom instructions include "Always respond in the language the user writes in". |

*The City Transport Authority, tender CTA/2026/T-0147 and the Government Procurement and Contracts Regulation (Training Edition) are fictional and were created for training only. The regulation is loosely modelled on UAE procurement practice and is not a legal text.*

---

## Appendix: example validation report

The full validation report for the defective tender, as described in [Exercise 7](#exercise-7-create-the-report-skill-and-generate-the-report).

![Report header and section 1, Key tender information](guide-images/report-1-key-information.png)

![Report section 2, Completeness](guide-images/report-2-completeness.png)

![Report sections 3 and 4, Regulatory compliance and Arabic–English consistency](guide-images/report-3-4-compliance-consistency.png)

![Report section 5, Required actions before release](guide-images/report-5-actions.png)
