# Test Plan: OWASP Juice Shop

Version: 0.1 (draft)

## 1. Scope
### 1.1 In scope: full coverage

Every requirement in the baseline (requirements v1.0, 349 requirements in 30 feature groups) is tested for these features:

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

- Impact 3: money, account access, data loss or privacy or the core flow is blocked. 2: a feature is wrong or unusable but there is a workaround. 1: cosmetic or rarely used.
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
| Single tester | No independent review | Peer review of the plan and test cases if possible |
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