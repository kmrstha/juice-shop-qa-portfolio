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

| --- | --- | --- |

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