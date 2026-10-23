# Smart Tender Validation with IBM Bob

Hands-on lab: build a **Tender Validator** mode in IBM Bob that checks a government tender for completeness, regulatory compliance and Arabic–English consistency, then writes a standard HTML validation report.

**Duration:** about 75 minutes · **Level:** beginner, no coding required

## What you need

- IBM Bob, installed and signed in
- A web browser, to view the generated reports

## Get started

1. Clone or download this repository.
2. Open **[Lab-Guide.pdf](Lab-Guide.pdf)** and follow it from Exercise 0.
3. In Bob, choose **File > Open Folder** and open the **`workspace/`** folder itself, not the whole repository.

## What's in this repository

| Path | Contents |
|---|---|
| `Lab-Guide.pdf` | Step-by-step lab guide |
| `workspace/` | The folder you open in Bob |
| `workspace/input/defective/` | Draft tender (EN + AR) with problems to find |
| `workspace/input/clean/` | Corrected tender (EN + AR) |
| `workspace/reference/` | Government Procurement and Contracts Regulation |
| `workspace/.bob/skills/tender-report/` | Pre-built skill that produces the HTML report |
| `workspace/output/` | Where Bob saves your reports |
| `lab-assets/` | Text you copy in during the lab: mode fields (Exercise 1) and the completeness rule file (Exercise 4) |

## Start over

Delete `workspace/.bob/custom_modes.yaml`, the `workspace/.bob/rules-tender-validator/` folder and any reports in `workspace/output/`. Keep `workspace/.bob/skills/`.

---

The City Transport Authority, tender CTA/2026/T-0147 and the Government Procurement and Contracts Regulation (Training Edition) are fictional and were created for training only. The regulation is not a legal text.
