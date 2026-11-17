# Covid19-TMS: STQA Mini Project (Testing Documentation)

Software Testing and Quality Assurance (STQA) mini project for SPPU BE Computer Engineering.

This repository documents how the **Covid19 Testing Management System (Covid19-TMS)** was set up on macOS and how it was tested. It contains the setup guide and a manual test-case suite with results.

## Credit

The application under test (PHP + MySQL) is an open-source project by **swapniltake1**:
https://github.com/swapniltake1/SPPU-BE-COMPUTER-ENGINERING-STQA-MINIPROJECT-

The application source code is **not** copied into this repository. This repo only contains my own testing work: setup notes, test cases, results and findings.

## About the system under test

Covid19-TMS is a web application where:
- a **patient** registers for a COVID-19 test through a form,
- an **admin** logs in, assigns a **phlebotomist**, and tracks the sample (assigned, on the way, collected, sent to lab),
- the admin delivers a **report**, which can be searched and downloaded.

**Tech stack:** HTML, CSS, JavaScript, PHP, MySQL (database `covidtmsdb`)

**Tables:** `tbladmin`, `tblpatients`, `tblphlebotomist`, `tbltestrecord`, `tblreporttracking`

## Repository contents

| File | Purpose |
|---|---|
| `SETUP-MAC.md` | Step-by-step setup of the application on macOS using MAMP |
| `TEST-CASES.md` | Manual test cases, readable on GitHub |
| `test-cases.csv` | The same test cases as a spreadsheet to record actual results |

## Testing approach

- **Type:** black-box functional testing (UI level)
- **Techniques:** equivalence partitioning, boundary value analysis, negative testing, regular-expression validation checks, basic security checks
- **Original automation:** the source project includes Selenium WebDriver (Java) tests. They are hardcoded for Windows, so see the "Automation notes" section in `TEST-CASES.md`.

## How to use

1. Follow `SETUP-MAC.md` to get the application running.
2. Open `test-cases.csv` in Excel, Numbers or Google Sheets.
3. Run each case, fill in **Actual Result** and **Status**, and add screenshots to a `screenshots/` folder.
4. Log any defects in the defect table in `TEST-CASES.md`.

## Author

Your Name | Your Roll No. | Your College
