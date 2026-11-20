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