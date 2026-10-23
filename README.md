# Smart Tender Validation with IBM Bob

Hands-on lab: build a **Tender Validator** in IBM Bob. It checks a government tender for completeness, regulatory compliance and Arabic-English consistency, then produces a standard HTML validation report.

**Duration:** about 80 minutes · **Level:** beginner, no coding required

## Getting started

1. Clone or download this repository.
2. In IBM Bob, choose **File > Open Folder** and open this repository's folder.
3. Open the **[Lab Guide](LAB-GUIDE.md)** and follow the exercises. Everything you need to paste into Bob is in the guide. A PDF copy is in [Lab-Guide.pdf](Lab-Guide.pdf).

## Repository contents

| Path | What it is |
|---|---|
| `LAB-GUIDE.md` | The lab guide. Start here. |
| `Lab-Guide.pdf` | PDF copy of the lab guide |
| `documents/defective-tender/` | The draft tender, in English and Arabic. It contains problems for you to find. |
| `documents/clean-tender/` | The corrected tender, in English and Arabic |
| `documents/procurement_regulation.pdf` | The Government Procurement and Contracts Regulation (Training Edition) |
| `templates/report_template.html` | The fixed report layout that your report skill fills in |
| `guide-images/` | Screenshots used in the lab guide |

Bob saves your validation reports to an `output/` folder, which it creates the first time it writes a report.

The City Transport Authority, the tender and the regulation are fictional and were created for training only.
