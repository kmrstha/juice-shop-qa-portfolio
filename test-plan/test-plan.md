# Test Plan: OWASP Juice Shop

| Item | Value |
| --- | --- |
| Document version | 1.0 |
| Date | 2026-11-23 |
| Author | Kumar Shrestha, QC Engineer |
| Application under test | OWASP Juice Shop v20.3.0 (local installation) |
| Requirements baseline | v1.0 (git tag requirements-v1.0), 349 requirements in 30 feature groups |
| Status | Final |

## Contents
1. Scope
2. Risk Assessment
3. Test Approach
4. Test Environment and Test Data
5. Entry and Exit Criteria
6. Effort and Schedule
7. Defect Management
8. Deliverables
9. Roles and Responsibilities
10. Change Log

## Introduction

### Purpose
This plan describes how OWASP Juice Shop v20.3.0 is tested in this project: what is tested, in which order, how, with what data, and how completion is judged. It is the basis for the test cases, the traceability matrix, the bug reports and the test summary report.

### Objectives
- Verify the application against the 349 baselined requirements
- Find and report defects with enough evidence to reproduce them
- Record unclear behavior as questions and observations instead of guessing
- Show a complete, repeatable test process as a portfolio project

### Test items
The web application OWASP Juice Shop v20.3.0 running locally at http://localhost:3000, in the features listed in section 1.

### References
| Document | Location |
| --- | --- |
| Requirements (baseline v1.0) | test-plan/requirements.txt |
| Features list | test-plan/features-list.txt |
| Test data log | test-plan/test-data-log.md |
| Time log | test-plan/time-log.md |
| Test cases | test-cases/ |
| Bug reports | bug-reports/ |
| Evidence | evidence/ |
| Summary report | summary-report/ |

### Definitions
| Term | Meaning |
| --- | --- |
| Pass | The actual result matches the expected result |
| Fail | The actual result differs from the expected result |
| Blocked | The test cannot be run because of an environment limit or another failure |
| Not Tested | The test has not been run yet |
| Defect | A confirmed difference between a requirement and the application's behavior |
| Question | Behavior whose intended result is unclear without a business requirement document |
| Observation | A fact worth recording that is not a defect |

## 1. Scope
### 1.1 In scope: full coverage

Every requirement of these features has at least one test case. The depth follows the priority in section 2: High gets positive, negative and boundary cases, Medium gets positive cases and the main negative case, and Low gets a short pass over the main behavior.

- Account: Registration, Login, Forgot Password, Change Password
- Shopping: Product List and Details, Search, Reviews, Basket
- Checkout: Address, Delivery, Payment, Coupon, Order
- Orders: Order History (tracking and print confirmation)
- Account pages: Profile, My Saved Addresses, My Payment Options, Digital Wallet
- Forms: Customer Feedback, Complaint
- Services: Deluxe Membership, Recycle, Data Export, Data Erasure, 2FA

### 1.2 In scope: limited coverage

| Feature | Coverage | Reason |
|---------|----------|--------|
| Language selector | A sample of 4 of the 43 languages: English (default), one with a different script, one with long words, one right-to-left if offered | Testing all 43 is not realistic |
| Last Login IP | The page opens and shows an IP address | Every login comes from the same local address, so a change cannot be tested |
| Other pages, Help Getting Started | The pages open and the tour starts and can be dismissed | Low risk, mostly static content |
| AI Chat | The window opens, a message is sent and the handled error message is shown | The AI service is not configured in this installation |

### 1.3 Security observations

No security testing is performed. Behavior that looks like a weakness is recorded as an observation or question with its evidence and is not exploited. Examples already recorded: unlimited failed login attempts (Q-05), author emails shown on reviews (Q-09), the erasure form accepting any details (Q-16). A separate security testing phase is planned as a later part of the portfolio.

### 1.4 Browsers

Chrome is the main browser: all test cases run there. Edge is used for a quick compatibility check of these critical flows: registration, login, basket, checkout through order completion, order history, profile (picture features) and the product popup. A failure in Edge is recorded with the browser named.

### 1.5 Out of scope

| Not tested | Reason |
| --- | --- |
| AI chatbot replies | Blocked: the AI endpoint is not configured locally |
| Performance and load | Needs different tools and an approach of its own |
| Deep security testing | Observations only (see 1.3) |
| Server-side checks without the browser (Q-03) | Planned for the API testing project |
| Mobile and tablet layouts | Desktop only |
| Browsers other than Chrome and Edge | Two browsers are enough for this project |
| Real payments | Fake test cards only |
| The 39 languages beyond the sample | See 1.2 |
| Accessibility | Not part of this project |

## 2. Risk Assessment

### 2.1 Method
Each feature is scored for Impact (1 to 3) and Likelihood of failure (1 to 3). Risk = Impact x Likelihood.

- Impact 3: money, account access, data loss or privacy, or the core flow is blocked. 2: a feature is wrong or unusable but there is a workaround. 1: cosmetic or rarely used.
- Likelihood 3: defects already found, or many rules and moving parts. 2: some complexity or untested limits. 1: simple, no defects seen.
- Priority: High (6 or 9) is tested first with edge values. Medium (3 or 4) gets full requirement coverage after the High group. Low (1 or 2) gets a shorter pass over the main behavior.

### 2.2 Product risks
| Feature | Impact | Likelihood | Score | Priority | Reason |
| --- | --- | --- | --- | --- | --- |
| Registration | 3 | 3 | 9 | High | Everything depends on it; BUG-01, BUG-02, BUG-03 found |
| Forgot Password | 3 | 3 | 9 | High | Changes credentials; counter, length and advice defects |
| Change Password | 3 | 3 | 9 | High | Password rules shown are not enforced |
| Checkout | 3 | 3 | 9 | High | Money and orders; expiry year range starts at 2080 |
| Data Erasure | 3 | 3 | 9 | High | Deletes the account; accepts any details |
| Login | 3 | 2 | 6 | High | Gate to every feature; no lockout after repeated failures |
| Basket | 3 | 2 | 6 | High | Totals and quantity limits |
| Payment Options | 3 | 2 | 6 | High | Card data |
| Digital Wallet | 3 | 2 | 6 | High | Balance and money |
| Deluxe Membership | 3 | 2 | 6 | High | Payment of 49 and discounts |
| 2FA | 3 | 2 | 6 | High | Can lock the user out |
| Data Export | 2 | 3 | 6 | High | Personal data; PDF and Excel cannot be selected |
| Profile | 2 | 3 | 6 | High | Both picture features fail |
| Order History | 2 | 2 | 4 | Medium | Tracking and print confirmation |
| Search | 2 | 2 | 4 | Medium | Core browsing; unusual special-character results |
| Reviews | 2 | 2 | 4 | Medium | Shows author emails |
| Complaint | 2 | 2 | 4 | Medium | Spaces-only message accepted |
| Saved Addresses | 2 | 2 | 4 | Medium | Reuses the checkout rules |
| Product List and Details | 3 | 1 | 3 | Medium | Entry to the shop, but simple |
| Customer Feedback | 1 | 2 | 2 | Low | Low impact |
| Recycle | 1 | 2 | 2 | Low | Low impact |
| Language selector | 1 | 2 | 2 | Low | Sample of 4 languages only |
| Last Login IP | 1 | 1 | 1 | Low | Limited by the local setup |
| Other pages, Help Getting Started | 1 | 1 | 1 | Low | Mostly static |
| AI Chat | 1 | 1 | 1 | Low | Handled error message only |

Totals: 13 High, 6 Medium, 6 Low.

### 2.3 Execution order notes
- Registration and Login are tested first, because every other feature needs an account.
- Features that change or destroy state are tested last, on throwaway accounts: Deluxe Membership (cannot be undone), 2FA (can lock the account) and Data Erasure (deletes the account).
- Checkout, Order History and the wallet are tested in sequence, because each uses data created by the one before.

### 2.4 Project risks
| Risk | Effect | Mitigation |
| --- | --- | --- |
| No BRS or product owner | The correct behavior is unclear in some cases | Requirements were derived from the application; unclear cases are recorded as questions, not as defects |
| Local installation | Data resets when the app restarts; the AI service and IP changes cannot be tested | Restart rarely; record blocked items in the plan |
| Test accounts can be locked or deleted | Later tests cannot run | Use throwaway accounts for 2FA, Data Erasure and Deluxe Membership; keep one main account untouched |
| Only one account for some checks | Cross-user requirements cannot be tested | Create a second account; otherwise mark "Not tested" |
| Public repository | Personal data could be exposed | Use test emails only; blur emails, IP addresses and 2FA secrets in evidence; do not commit exports |
| Single tester | No independent review | Self-review of the plan and test cases after a gap of at least one day; peer review if a reviewer becomes available |
| Data-dependent results | Numbers such as 46 products may change | Record the data state at the start of each run |

## 3. Test Approach

### 3.1 Test types
| Type | Purpose |
| --- | --- |
| Functional | Each requirement behaves as written |
| Negative | Invalid input and wrong actions are handled |
| Boundary | The edges of every limit are tested |
| Validation messages | The exact message text and its trigger |
| End-to-end flow | A full journey across features, for example register, log in, add to basket, check out, then find the order in Order History |
| Exploratory | Time-boxed free testing around High-risk features |
| Compatibility (quick) | The Edge flows listed in the Scope section |

### 3.2 Design techniques
- Boundary value analysis for limits: password 4/5/40/41 characters, review 160, complaint 4096, quantity 9/10/1000/1001, mobile number 6/7/10/11 digits, basket quantity 5/6
- Equivalence partitioning: one valid and one invalid class per input, so similar inputs are not repeated
- Decision table: order total from quantity, delivery option and coupon
- State transition: 2FA (off, on, off), Deluxe Membership, login and logout
- Error guessing: during the exploratory sessions

### 3.3 Test case design: one case per behavior
A test case covers one behavior. Requirements that describe the same behavior are grouped into one case, and the case lists every requirement ID it covers.

Grouping rules:
- Group requirements with the same trigger and the same kind of result. Example: the "left empty after being selected" messages of one form are one case, with each field's result recorded.
- Do not group a requirement that fails or is suspected to fail (REQ-REG-12, REQ-REG-18, REQ-REG-07 and similar). Each gets its own case, so the failure is isolated and one bug maps to one case.
- Do not group across features.
- High-priority features are grouped less aggressively than Medium and Low ones.
- Every requirement is covered by at least one case. A traceability matrix proves this.

Estimated size: about 200 to 230 test cases for 349 requirements. The exact number is set when the cases are written.

### 3.4 Coverage by priority
| Priority | Coverage |
| --- | --- |
| High | Positive, negative and boundary cases; one end-to-end flow where it applies; one exploratory session |
| Medium | Positive cases and the main negative case for each behavior |
| Low | A short pass over the main behavior |

### 3.5 Test case format
Each test case is a row in a spreadsheet with: ID (TC-REG-01), requirement IDs covered, priority, preconditions, steps, test data, expected result, actual result, status (Pass, Fail, Blocked, Not Tested), bug ID, browser and run date.

### 3.6 Exploratory testing
One session of 30 to 45 minutes for each High-priority feature, with a written charter (for example: "Explore the Registration form with unusual inputs to find validation gaps"). Each session records its notes, any new questions, and any behavior the requirements missed. New findings are logged as questions or bugs, and requirements change only through the baseline change log.

### 3.7 Automation
Not part of this plan. Automating 10 to 15 High-priority cases is a separate later project.

### 3.8 Defect handling
A mismatch between a requirement and the application is logged as a bug in `bug-reports/`, with steps, expected and actual results, severity, and evidence. Where the intended behavior is unclear, the finding is recorded as a question and is not called a defect.

## 4. Test Environment and Test Data

### 4.1 Application under test
| Item | Value |
| --- | --- |
| Application | OWASP Juice Shop v20.3.0 |
| Source | https://github.com/juice-shop/juice-shop/releases/tag/v20.3.0 |
| Installation | Local release package |
| Started with | npm start |
| URL | http://localhost:3000 |
| Default language | English |
| Data state at start | 46 products, 16 products per page by default, wallet balance of a new account 0 |

### 4.2 Test machine
| Item | Value |
| --- | --- |
| Operating system | Windows 11, version 25H2 (OS build 26200.9974) |
| Node.js | v26.13.1 |
| Screen resolution | 1920 x 1080 |
| Browser zoom | 100% |
| Internet access | Available during tests. Needed for Link Image (loads a picture from a web address) and the GitHub link in the side menu |

### 4.3 Browsers
| Browser | Version | Use |
| --- | --- | --- |
| Google Chrome | 157.0.8103.79 | Main browser, all test cases |
| Microsoft Edge | 157.0.4341.60 | Quick compatibility check of the critical flows (see 1.4) |

### 4.4 Test accounts
| Role | Email | Used for |
| --- | --- | --- |
| Main account | qa.main@example.com | Login, basket, checkout, orders, profile |
| Second account | qa.second@example.com | Cross-user checks: REQ-ORH-05, REQ-ADD-11, REQ-PMT-10, REQ-LIP-04 |
| Empty account | qa.empty@example.com | A new account with no orders, addresses or cards: REQ-ORH-17, REQ-ADD-12, REQ-PMT-11, REQ-RCY-12 |
| 2FA account | qa.fa@example.com | 2FA tests only |
| Erasure account | qa.erasure@example.com | Data Erasure tests only, run last |
| Deluxe account | qa.deluxe@example.com | Deluxe Membership tests only, because it cannot be undone |

Accounts use made-up email addresses. Passwords are not recorded in this repository. Accounts are recreated after each application restart (see 4.6), and their creation dates are kept in the test data log.

### 4.5 Test data
| Data | Value |
| --- | --- |
| Test card | A fake 16-digit card number with a name and an expiry year of 2080 or later (the earliest year offered) |
| Coupon | One valid 10-character coupon code giving a 10% discount, taken from a public Reddit post. The code is kept in private notes. Last checked on 2026-11-23: accepted |
| Address | One complete test address with a 7 to 10 digit mobile number |
| Complaint files | A PDF under 100 KB, a PDF over 100 KB, and one file that is not a PDF |
| Profile files | A small PNG and a small JPG |
| Image URL for Link Image | https://images.pexels.com/photos/40483057/pexels-photo-40483057.jpeg (direct link to a public image) |
| Wallet | A new account starts at 0. 100 is added by deposit with the test card before the wallet tests |

No real personal data, real card numbers or real email addresses are used. The coupon is checked at the start of each run, and its result is recorded in the test data log.

### 4.6 Data reset
The application data resets when it is restarted, including all accounts, orders, addresses, cards and the wallet balance. Test accounts must be recreated after each restart. Each test run starts by recording the data state: product count, wallet balance and accounts created. The application is not restarted during a test run.

### 4.7 Known environment limits
- The AI service is not configured, so chatbot replies cannot be tested
- All logins come from the same local address, so a change in the Last Login IP cannot be tested
- The Expiry Year dropdown starts at 2080, so an expired card cannot be tested
- The Upload Picture section offers no file chooser (see BUG-04)
- Link Image does not set the picture from a valid URL (see BUG-05)
- The Data Export page offers PDF and Excel but they cannot be selected (Q-15)

## 5. Entry and Exit Criteria

### 5.1 Entry criteria
Test execution starts when all of these are true:
- Requirements baseline v1.0 is frozen and tagged (requirements-v1.0)
- This test plan is saved in the repository
- The test case sheet and the traceability matrix exist
- OWASP Juice Shop v20.3.0 runs at http://localhost:3000
- The test accounts and test data from section 4 are created for this run (accounts are recreated after each restart)
- The coupon has been checked and its result recorded in the test data log
- Chrome and Edge are available at the versions in section 4.3
- The data state at the start of the run (products, wallet balance, accounts) is recorded in the test data log

### 5.2 Exit criteria
Testing is complete when all of these are true:
- Every High-priority test case has been executed
- Every Medium and Low test case has been executed or marked Blocked with a reason
- Every requirement is covered by at least one test case, shown by the traceability matrix
- Every failed test case has a bug report, or a question where the intended behavior is unclear
- Every bug report has steps, expected and actual results, severity and evidence
- The Edge compatibility check of the critical flows is complete
- The test summary report is written

No pass-rate target is set. The application contains deliberate defects, so a pass rate would not show whether the testing was done well. Completion is measured by coverage, execution and reporting.

### 5.3 Suspension criteria
Testing stops until the cause is resolved when:
- The application does not start, or crashes repeatedly
- Login fails for every account
- The data is corrupted and results cannot be trusted
- The application is restarted in the middle of a run (accounts and data are lost)

### 5.4 Resumption criteria
Testing resumes when:
- The application runs again, and a quick check of login, basket and checkout passes
- The test accounts and test data are recreated and recorded in the test data log
- The interrupted test cases are re-run from their preconditions

## 6. Effort and Schedule

### 6.1 Assumptions
- Available time: 25 hours per week
- Start date: 2026-11-24 (test case writing)
- No fixed deadline. The schedule shows the estimated duration.
- About 215 test cases (estimate range 200 to 230): about 140 High, 55 Medium and 20 Low
- Writing time: High 140 x 20 minutes = 47 hours, Medium 55 x 10 minutes = 9 hours, Low 20 x 8 minutes = 3 hours; total 59 hours
- Execution time: High 140 x 6 minutes = 14 hours, Medium 55 x 5 minutes = 4.6 hours, Low 20 x 3 minutes = 1 hour; plus the Edge check (about 2 hours) and retests (about 4 hours); total about 26 hours
- Exploratory testing: 13 High-priority features, about 45 minutes each plus notes
- About 15 bug reports at about 25 minutes each, including evidence
- Contingency of 15% for reruns, restarts and unclear requirements
- The application data resets on restart (see 4.6), so each run needs account setup

### 6.2 Effort estimate
| Activity | Hours |
| --- | --- |
| Write test cases (about 215) | 59 |
| Traceability matrix (349 requirements) | 4 |
| Review of test cases (after a gap of at least one day) | 4 |
| Environment and account setup (3 runs) | 3 |
| Test execution in Chrome, Edge check and retests | 26 |
| Exploratory sessions | 10 |
| Bug reports with evidence | 6 |
| Test summary report | 4 |
| Subtotal | 116 |
| Contingency (15%) | 17 |
| Total | 133 |

At 25 hours per week, 133 hours is about 5.3 weeks.

### 6.3 Schedule
| Week | Dates | Hours | Work |
| --- | --- | --- | --- |
| 1 | Nov 24 to Nov 30 | 25 | Write test cases: Registration, Login, Forgot Password, Change Password, Basket, Checkout, Order History, Digital Wallet, Payment Options |
| 2 | Dec 1 to Dec 7 | 25 | Write test cases: Data Erasure, Data Export, Deluxe Membership, 2FA, Profile, and the Medium and Low features |
| 3 | Dec 8 to Dec 14 | 25 | Finish writing (9); traceability matrix (4); review of test cases (4); account setup for run 1 (1); start executing High-priority cases (7) |
| 4 | Dec 15 to Dec 21 | 25 | Execution, Edge check and retests (19); exploratory sessions, first half (5); account setup for run 2 (1) |
| 5 | Dec 22 to Dec 28 | 25 | Exploratory sessions, second half (5); bug reports with evidence (6); summary report (4); account setup for run 3 (1); contingency (9) |
| 6 | Dec 29 to Jan 4 | about 8 | Contingency for retests and open items |

Estimated completion: around the end of December 2026, or the first days of January 2027 if all the contingency is used.

### 6.4 Notes
- Features that change or destroy state (Deluxe Membership, 2FA, Data Erasure) are executed last, on their own accounts.
- Hours not worked in a week move the following weeks later by the same amount.
- Checkpoint after week 1: compare the actual time per test case with the assumptions in 6.1, and re-estimate the rest of the schedule.
- The schedule is reviewed again at the end of week 3, when execution begins.
- Weeks 5 and 6 overlap the year-end holidays, so they are the weeks most likely to slip.

## 7. Defect Management

### 7.1 Reporting rules
- Each defect has its own file in bug-reports/, named BUG-NN-short-description.md
- Every report has: ID and title, environment, requirement ID, severity, status, steps, expected result, actual result and evidence
- Evidence is saved in evidence/ with the bug ID in the file name
- A difference that might be intended is recorded as a question, not a defect
- A defect is reproduced a second time before its status becomes Open
- Evidence never shows real personal data: emails are test addresses, and other details are blurred

### 7.2 Severity
| Severity | Meaning | Example |
| --- | --- | --- |
| Critical | Data loss, a security breach or a crash, or the core flow is blocked with no workaround | The application stops responding at checkout |
| High | A major feature is broken and no workaround exists | A valid order cannot be placed |
| Medium | A feature is wrong or partly unusable, or validation is weak, but a workaround exists | BUG-01: the password advice is not enforced |
| Low | A display, wording or consistency problem with no real impact | BUG-03: the password counter shows the wrong limit |

### 7.3 Status
| Status | Meaning |
| --- | --- |
| Draft | Noted, not yet reproduced or backed by evidence |
| Open | Reproduced and reported with evidence |
| Question | Intended behavior is unclear; waiting for clarification |
| Withdrawn | Explained, or found not to be a defect |

There is no development team in this project, so defects are not fixed or retested. A defect stays Open and is listed in the summary report. Priority is not assigned, because there is no release or fix schedule. Severity orders the findings in the summary report.

## 8. Deliverables
| Deliverable | Location | When |
| --- | --- | --- |
| Requirements baseline v1.0 | test-plan/requirements.txt | Done |
| Test plan | test-plan/test-plan.md | Done |
| Test cases | test-cases/test-cases.xlsx | Weeks 1 to 3 |
| Traceability matrix | test-cases/traceability-matrix.xlsx | Week 3 |
| Test data log | test-plan/test-data-log.md | Updated during execution |
| Exploratory session notes | test-cases/exploratory-notes.md | Weeks 4 to 5 |
| Bug reports | bug-reports/ | During execution |
| Evidence | evidence/ | During execution |
| Time log | test-plan/time-log.md | Updated each session |
| Test summary report | summary-report/ | Week 5 |

## 9. Roles and Responsibilities
| Role | Person | Responsibilities |
| --- | --- | --- |
| Test lead and tester | Kumar Shrestha | Plan, design and write test cases, execute them, report defects, write the summary report |
| Reviewer | None (self-review after a gap of at least one day) | Reviews the plan and the test cases |

## 10. Change Log
| Date | Version | Change |
| --- | --- | --- |
| 2026-11-23 | 1.0 | First complete version: sections 1 to 10 |
| 2026-11-24 | 1.0 | Self-review after a one-day gap: [no changes / list of changes] |