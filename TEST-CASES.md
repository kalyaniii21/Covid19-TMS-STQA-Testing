# Test Cases: Covid19-TMS

**Application:** Covid19 Testing Management System (PHP + MySQL)
**Base URL:** http://localhost:8888/covid19tms/covid-tms/
**Environment:** macOS (Apple Silicon), MAMP 7.5, PHP 8.3, MySQL on port 8889, any modern browser
**Test type:** Black-box functional testing

**How to read this file:** the *Expected Result* column is what a correct system should do. Run each case and record the real behaviour in `test-cases.csv` (Actual Result and Status). Cases marked **Observed** were already seen during setup. Cases marked **Verify** depend on form rules I have not read from the source, so check the real behaviour before you claim a result.

## 1. Patient registration (`new-user-testing.php`)

| ID | Scenario | Test data | Expected result | Note |
|---|---|---|---|---|
| TC-REG-01 | Register with all valid details | Valid name, new 10-digit mobile starting 6-9, other fields valid | Registration succeeds and the record appears in `tblpatients` | Verify |
| TC-REG-02 | Register with an already registered mobile number | Mobile used in TC-REG-01 | Registration rejected with a "mobile number already registered" message | **Observed** |
| TC-REG-03 | Mobile number shorter than 10 digits | 987650123 (9 digits) | Rejected with a validation message | Boundary, Verify |
| TC-REG-04 | Mobile number longer than 10 digits | 98765012345 (11 digits) | Rejected with a validation message | Boundary, Verify |
| TC-REG-05 | Mobile number with letters | 98765abcde | Rejected | Verify |
| TC-REG-06 | Mobile number starting with 0-5 | 1234567890 | Rejected (not a valid Indian mobile) | Verify |
| TC-REG-07 | Leave all required fields empty | none | Form not submitted, required-field message shown | Verify |
| TC-REG-08 | Name containing digits or symbols | Ravi123@ | Rejected or sanitised | Verify |
| TC-REG-09 | Very long name (255+ characters) | 300 x "a" | Handled without error or broken page | Boundary, Verify |
| TC-REG-10 | Script in a text field | `<script>alert(1)</script>` | Saved and shown as plain text, no alert pops up | Security |

## 2. Input validation with regular expressions

These are the patterns a validation test would apply to the registration fields. Compare them with the patterns in `RegexTest.java` and the form's own checks.

| ID | Field | Pattern | Valid example | Invalid example |
|---|---|---|---|---|
| TC-RGX-01 | Mobile number | `^[6-9][0-9]{9}$` | 9876501234 | 5876501234, 98765 |
| TC-RGX-02 | Name | `^[A-Za-z ]{2,50}$` | Asha Patil | A, Asha123 |
| TC-RGX-03 | Email | `^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$` | admin@gmail.com | admin@, @gmail.com |
| TC-RGX-04 | Pincode (if present) | `^[1-9][0-9]{5}$` | 411044 | 011044, 4110 |

For each row, enter both the valid and invalid examples and record whether the application accepts or rejects them.

## 3. Admin login (`login.php`)

| ID | Scenario | Test data | Expected result | Note |
|---|---|---|---|---|
| TC-LOG-01 | Valid credentials | admin / correct password | Dashboard opens | **Observed** |
| TC-LOG-02 | Wrong password | admin / wrongpass | Login refused, error shown | Verify |
| TC-LOG-03 | Wrong username | nouser / any | Login refused | Verify |
| TC-LOG-04 | Both fields empty | none | Login refused or required-field message | Verify |
| TC-LOG-05 | SQL injection attempt | username `admin' OR '1'='1` , any password | Login refused (a successful login would be a security defect) | Security |
| TC-LOG-06 | Open a dashboard page without logging in | Paste a dashboard URL in a private window | Redirected to login | Security, Verify |
| TC-LOG-07 | Logout then press Back | Log out, click browser Back | Dashboard not accessible | Security, Verify |

## 4. Admin workflow

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-ADM-01 | New request shows on the dashboard | Register a patient, log in as admin | "Total Tests" count increases and the request is listed under Testing |
| TC-ADM-02 | Assign a phlebotomist | Open the new request, assign one | "Total Assigned" count increases |
| TC-ADM-03 | Update sample status | Move the request through the status steps | The matching dashboard counter changes at each step |
| TC-ADM-04 | Deliver a report | Mark the request as report delivered | "Report Delivered" count increases |
| TC-ADM-05 | Dashboard totals match the database | Compare dashboard numbers with the rows in `tbltestrecord` | Numbers are equal |

## 5. Search and download report (`patient-search-report.php`)

| ID | Scenario | Test data | Expected result |
|---|---|---|---|
| TC-SRC-01 | Search an existing patient | Registered mobile number | Patient's record is shown |
| TC-SRC-02 | Search a patient that does not exist | 9000000000 | "No record found" or an empty result, no error |
| TC-SRC-03 | Search with an empty field | none | Validation message or empty result, no error |
| TC-SRC-04 | Download a delivered report | Search a patient whose report is delivered | Report opens or downloads |

## 6. Navigation and general

| ID | Scenario | Expected result |
|---|---|---|
| TC-NAV-01 | Every top navigation link opens (About, Symptoms, Prevention, Testing, Live Updates, Admin) | Each page loads without errors |
| TC-NAV-02 | Live Updates page (`live-test-updates.php`) loads | Page loads and its buttons work |
| TC-NAV-03 | Layout on a narrow browser window | Content stays readable |
| TC-NAV-04 | Open a URL that does not exist | A not-found page, not a PHP error dump |

## Automation notes

The source project includes Selenium WebDriver tests in Java (`LoginTest`, `SignUpTest`, `RegexTest`, `AlreadyRegUser`, `BtnTest`, `NewTest`, `SearchTestReport`, `SearchAndDownloadReportTest`).

They are not runnable on this Mac as they are:
- Every test sets a Windows driver path: `C:\Users\Swapn\OneDrive\Desktop\chromedriver.exe`
- Every test opens `http://localhost/Covid19-TMS%20Project%20Using%20PHP%20and%20MySQL/covid-tms/...` with no port (MAMP serves on 8888) and a different folder name
- The tests call `webdriver.gecko.driver` (the Firefox driver name) but point it at `chromedriver.exe`, a driver/browser mismatch

To run them you would install a JDK and Eclipse, then change the three items above. Mapping to the manual cases:

| Automated class | Related manual cases |
|---|---|
| SignUpTest, RegexTest | TC-REG-*, TC-RGX-* |
| AlreadyRegUser | TC-REG-02 |
| LoginTest, NewTest | TC-LOG-* |
| BtnTest | TC-NAV-02 |
| SearchTestReport, SearchAndDownloadReportTest | TC-SRC-* |

## Defect log

| Defect ID | Test case | Description | Severity | Status |
|---|---|---|---|---|
| D-01 | (setup) | Database host and port hardcoded to `localhost:3308` with an empty password, so the app shows a blank page on any machine with a different MySQL setup | Medium | Worked around in `config.php` |
| D-02 | (setup) | Connection errors produce a blank page instead of a readable message under PHP 8 | Low | Open |
| D-03 | (security) | Admin password stored as unsalted MD5 | High | Open (observed in `tbladmin`) |

Add new defects found while running the cases above.
