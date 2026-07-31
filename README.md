<div align="center">

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 120" width="800" height="120">
  <defs>
    <linearGradient id="grad" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#C00000;stop-opacity:1" />
      <stop offset="50%" style="stop-color:#1F3864;stop-opacity:1" />
      <stop offset="100%" style="stop-color:#C00000;stop-opacity:1" />
      <animateTransform attributeName="gradientTransform" type="translate" values="-800 0;800 0;-800 0" dur="6s" repeatCount="indefinite"/>
    </linearGradient>
    <style>
      .title { font: bold 28px Arial; fill: white; }
      .sub { font: 14px Arial; fill: #aaaacc; }
      .fade { animation: fadeIn 2s ease-in forwards; }
      @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
  </defs>
  <rect width="800" height="120" rx="12" fill="url(#grad)"/>
  <text x="400" y="55" text-anchor="middle" class="title">Change Request Excel Bot</text>
  <text x="400" y="85" text-anchor="middle" class="sub">Oracle EBS Migration Automation — ServiceNow to Excel in one command</text>
</svg>

<br/>

![Python](https://img.shields.io/badge/Python-3.10+-1F3864?style=for-the-badge&logo=python&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-Automation-C00000?style=for-the-badge&logo=playwright&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)
![openpyxl](https://img.shields.io/badge/openpyxl-Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)

</div>

---

## Overview

This bot automates the most tedious part of the Oracle EBS migration process — manually collecting change request details from ServiceNow, cross-referencing Release Note attachments, and compiling everything into a formatted Excel report. What normally takes hours of copy-pasting is reduced to a single command.

The bot handles login (including OTP), navigates to your group's change requests, processes each one individually, and delivers a fully formatted Excel file matching the approved migration template.

---

## How it works

```
ServiceNow Login
      |
      v
Navigate to My Group Changes
      |
      v
Filter In Progress requests
      |
      v
For each Change Request:
  |
  |-- Search CHG number
  |-- Scrape Requested By + Short Description
  |-- Download Release Note attachment
  |-- Extract Developer, Object Types, Object Count (rule-based + ML)
  |
  v
Generate formatted Excel report
```

---

## Features

**Browser Automation**
- Handles multi-step login — email, password, and manual OTP pause
- Navigates ServiceNow's iframe-heavy UI reliably
- Searches each CHG number individually, downloads its Release Note

**Smart Object Type Detection**
- Rule-based engine covers 200+ known variations (PKS, PKB, FMB, LDT, RDF, Personalization Doc, java class, Steps, etc.)
- ML classifier (TF-IDF + Logistic Regression) as a fallback for unseen or unusual entries
- Model is retrain-ready — add new labeled examples and re-run one command

**Excel Report Generation**
- Matches the approved Oracle EBS Migration template exactly
- Red title bar, navy column headers, bordered data rows
- Auto-fills Migration Date with today's date
- Handles missing attachments gracefully — adds "Please share the Release Note" remark

---

## Project Structure

```
Change_Request_Excel_BOT/
|
|-- open_chrome.py                  Main bot — run this
|-- object_type_model.pkl           Trained ML model (auto-loaded at startup)
|
|-- train_object_type_model.py      Retrain the ML model
|-- extract_cells_for_labeling.py   Extract cell values from Excel files for labeling
|-- merge_labeled_data.py           Merge newly labeled data into training set
|-- object_type_training_data.csv   Labeled training dataset
|
|-- test_model_on_excel.py          Test model against real Release Note files
|-- test_object_type_model.py       Interactive model tester
|
|-- downloads/                      Downloaded Release Notes (auto-created)
```

---

## Setup

**1. Install dependencies**
```bash
pip install playwright openpyxl scikit-learn pandas
playwright install chromium
```

**2. Configure credentials**

Open `open_chrome.py` and fill in your details at the top:
```python
Email    = "your.email@company.com"
Password = "your_password"
```

**3. Run**
```bash
python open_chrome.py
```

The browser opens, fills in your email and password automatically, then pauses for you to enter the OTP. After that, everything runs on its own.

---

## Output

The bot saves `Change_Requests_Final_DD-Mon-YYYY.xlsx` in the project folder.

| Column | Source |
|---|---|
| S.No | Auto-numbered |
| Change Request No. | Scraped from CTASK list |
| CTASK No. | Scraped from CTASK list |
| Developer | Release Note attachment |
| Requested By | CHG detail page |
| CB Status | Defaulted to "Approved" |
| Migration Date | Today's date |
| Object Type | Release Note (rules + ML) |
| Total Objects Count | Release Note (rules + ML) |
| Description | CHG detail page (Short Description) |
| Remarks | "Please share the Release Note" if missing |

---

## Improving the ML Model

The model learns what "Object Type" text looks like from your own release notes.

**Step 1 — Extract unique cell values**
```bash
python extract_cells_for_labeling.py "path/to/release_notes_folder"
```

**Step 2 — Label them**

Open `unlabeled_cells.csv` in Excel. Fill the `is_object_type` column:
- `1` — valid Object Type (PKS, FMB, Package Body, View, etc.)
- `0` — not an Object Type (names, dates, instructions, version numbers)

**Step 3 — Merge and retrain**
```bash
python merge_labeled_data.py
python train_object_type_model.py
```

The new `object_type_model.pkl` is loaded automatically next time you run the bot.

---

## Notes

- OTP entry is always manual — MFA cannot be automated
- Credentials are stored in plain text in `open_chrome.py` — move them to a `.env` file before committing to any shared repository
- If a Release Note has a non-Excel attachment (e.g. PNG), the bot pauses and asks you to provide the file manually or skip
- The `(ML)` suffix appears on object types detected by the classifier rather than the rule engine — useful for spotting and verifying new patterns

---

## Built With

- [Playwright](https://playwright.dev/python/) — browser automation
- [openpyxl](https://openpyxl.readthedocs.io/) — Excel reading and writing
- [scikit-learn](https://scikit-learn.org/) — ML classifier
- [pandas](https://pandas.pydata.org/) — data handling

---

<div align="center">

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 600 60" width="600" height="60">
  <defs>
    <linearGradient id="footergrad" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#1F3864"/>
      <stop offset="100%" style="stop-color:#C00000"/>
    </linearGradient>
  </defs>
  <rect width="600" height="60" rx="8" fill="url(#footergrad)"/>
  <text x="300" y="35" text-anchor="middle" font-family="Arial" font-size="13" fill="white">Built for Oracle EBS Migration at Motherson</text>
</svg>

</div>
