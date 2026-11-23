# Test strategy (v1, draft)

## Risk ranking

Scale: 1 (low) to 3 (high). Score = impact × likelihood, using the baseline likelihood.
Likelihood is change-based: it goes up when the related code, a dependency or the pinned Juice Shop version changes.
Assumptions marked "to verify" are checked with DevTools once the local container runs.

| Feature        | Impact | Likelihood | Score |
|----------------|--------|------------|-------|
| Checkout       | 3      | 3          | 9     |
| Login          | 3      | 1          | 3     |
| Add to basket  | 3      | 1          | 3     |
| About Us       | 1      | 2          | 2     |
| Product search | 1      | 1          | 1     |

## Reasoning per feature

### Checkout (score 9)
- Impact 3: a failure means losing money.
- Likelihood 3: many modules have to work in a chain (login, basket, address, payment), so there are more points of failure. Verified with DevTools during a full checkout (address, card, order): 92 of 93 requests went to localhost and the only other one came from a browser extension, so there is no external payment provider.
- Trigger: a new payment method or integration. The score is already at the maximum, so a change means deeper testing, not a higher number.
- Test level: API + E2E. API checks what is sent and received (payloads, nulls, invalid data). E2E is one full journey that checks what the user sees.

### Add to basket (score 3)
- Impact 3: the client cannot add products, which blocks buying and means losing money.
- Likelihood 1: the version is pinned, so the code does not move between runs; it only changes when we upgrade.
- Verified with DevTools: adding a product is `POST /api/BasketItems/` (200), the basket is read with `GET /rest/basket/<id>`, and a later add triggered a `PUT`. The POST payload is `{ProductId, BasketId, quantity}` and the PUT payload is `{quantity}`; the item id is in the PUT URL (`PUT /api/BasketItems/<id>`). That id comes from the POST response (`data.id`), so a test can chain both calls without hardcoding it (verified). Open question for Module 7: the client sends `BasketId`, so does the server check that the basket belongs to the user?
- Trigger: likelihood goes to 3 when new integrations, modules or third-party code are added.
- Test level: API + E2E. API: add an item, change the quantity, check the total and that each user has their own basket (to verify). E2E: one journey where the user adds a product and sees it in the basket.

### Login (score 3)
- Impact 3: the user cannot log in, so they cannot use any other feature and cannot be a customer.
- Likelihood 1: a login is rarely modified and its code is mostly static (not verifiable: it is third-party code, and the pinned version controls when it changes). Credentials and tokens can expire or disappear when the data is reset, so tests create a user and log in during setup. Several pieces are involved (form, endpoint, database, token). Verified with DevTools: login is `POST /rest/user/login` on the local server (payload: email and password; the response includes a token) and the request has no external provider.
- Trigger: likelihood goes to 3 when auth code, dependencies or the pinned Juice Shop version change. Then each piece is reviewed separately and afterwards together.
- Test level: API smoke test that always runs (status 200 and a token) plus one E2E. Login is also used as setup through the API in other tests.

### Product search (score 1)
- Impact 1: all products are listed on the main page, so if search fails the user can still browse and buy (verified in the UI).
- Likelihood 1: it is a single read-only GET, simple and stateless.
- Trigger: likelihood goes to 2 when new products or filters are added, when product names contain special characters, or when the catalog grows a lot (more items, slower search).
- Verified by calling it directly: `GET /rest/products/search?q=<term>` returns 200 and JSON `{status, data: [...]}`. Whether the UI calls this endpoint was not verified; it does not change the plan, because search is tested at the API level only.
- Observation: searching `apple` also returned Pineapple Juice, so matching is on any part of the name (verified in the response). Product descriptions can contain HTML (Module 7 question).
- Open question for Module 7: how does search handle user-typed input (injection)? Not verified.
- Test level: API only. Many input combinations can be checked quickly with GET requests (different terms, special characters), which keeps the test fast.

### About Us (score 2)
- Impact 1: it does not block any purchase.
- Likelihood 2: it also receives data from the server (the Customer Feedback carousel is fed by the `Feedbacks/` request), so it is not static content: the data can change and the page could look broken.
- Verified with DevTools: the page has static text, a Customer Feedback carousel and links to social media. Feedback comments are written by users and can contain HTML (Module 7 question).
- Trigger: likelihood goes to 3 when the API integration that provides the feedback data changes.
- Test level: API only, checking the server response. Accepted blind spot (score 2): it does not detect a carousel that looks broken on screen.

## Tie-break rules
The feature that goes first is the most critical for the system or the most used.

## Test levels
- Unit: tests specific lines of code, a block in isolation, checking its logic on its own.
- Integration/API: requests (POST, GET, PUT, DELETE, etc.) that check not only that it works, but also the responses and payloads.
- UI/E2E: functional test of the whole system, what the client sees.

Blind spots:
- An API test does not detect E2E problems, design problems or anything visual.
- A unit test does not detect the impact a change can have on the code of another feature.
