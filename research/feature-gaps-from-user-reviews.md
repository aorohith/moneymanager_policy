# Feature gaps from real user reviews

Date: 2026-10-29

## How this was gathered

Web search over review summaries for Play Store and App Store money manager apps (Money Manager by various devs, Realbyte Money Manager, Cashew, Money Lover, Wallet by BudgetBakers, Indian SMS trackers like Axio, Vrid, Trakio, RuTrack).

Limit: play.google.com and apps.apple.com are blocked in this cloud session's network policy, so raw review pages were not read. Findings come from search snippets that quote reviews. Treat counts as directional, not measured.

Confidence tags: [Certain] = quoted review seen, [Likely] = repeated across sources, [Guessing] = inference.

Baseline (what the app already has, per the privacy policy): transactions, accounts, categories, budgets, goals, notification-based detection with review step, CSV/Excel/PDF import, export, PIN + biometric lock, local notifications, fully offline.

## P0: complaints that cause 1-star reviews

| # | Feature | What users said | Status in our app | Confidence |
|---|---------|-----------------|-------------------|------------|
| 1 | Transfers between own accounts | Users log transfers as income/expense and charts get distorted. One reviewer: transfer was "the only thing missing". | Done (`transferGroupId` in `TransactionModel`) | [Certain] |
| 2 | Recurring transactions | "Very basic" review because no recurring entries; dev called it a top request. | Done (`recurrence_service.dart`) | [Certain] |
| 3 | Backup and restore you control | Sync/resync loses past transactions; trust drops fast. We have no cloud, so a phone loss = total data loss. Offer encrypted local file backup + optional Google Drive backup the user owns. | Missing (policy says no cloud backup) | [Likely] |
| 4 | Accurate auto-detection | Indian users ask whether SMS/notification parsing breaks for certain banks and message formats. Wrong amounts or duplicates kill trust. | Partly present; needs dedupe + per-bank parser coverage | [Likely] |
| 5 | No ads, no loan upsell | Users report SMS-reading apps pushing loans, insurance, BNPL. Trust was called the #1 complaint in 1-star finance reviews. | Already a strength. Say it loudly on the store listing. | [Likely] |
| 6 | Fair free tier | Paywalled import, budgets, analytics, and full-price renewals draw anger. | Pricing decision, not code | [Likely] |

## P1: frequent feature requests

| # | Feature | What users said | Confidence |
|---|---------|-----------------|------------|
| 7 | Subcategories (more than one level) | Cashew's top wish: more than one subcategory per category. | [Certain] |
| 8 | Bigger category icon set | Repeated requests for more icons (e.g. fruits, vegetables). | [Certain] |
| 9 | Custom date range without limits | Custom range capped at about 365 days in one app. | [Certain] |
| 10 | Multi-line notes | Notes field could not take line breaks. | [Certain] |
| 11 | Prominent balance on home | Balance looked hidden; users want it large. | [Certain] |
| 12 | Cross-account reports | Money Lover users can't report by category across wallets. | [Certain] |
| 13 | Subscription tracker | Cashew praised for tracking subscriptions. | [Likely] |
| 14 | Multi-currency with live rates | Praised in Cashew. Useful for NRI users and travel. | [Likely] |
| 15 | Bill reminders / due dates | Monthly bills via recurring entries praised in Money Lover. | [Likely] |
| 16 | Debt / lent / borrowed tracking | Wallet reviewer credited it with paying off debts. Very common in India (friends, family). | [Guessing] on demand size |
| 17 | Split expenses / shared wallet | Money Lover shared wallets for couples; Wallet has bill split. Hard without a server. | [Likely] demand, high cost for offline app |
| 18 | Receipt photo attachment | Listed as a Realbyte feature. Needs camera/storage permission; update privacy policy if added. | [Guessing] |

## P2: polish

| # | Feature | Note | Confidence |
|---|---------|------|------------|
| 19 | Fast first-run setup | "Setup not as simple as other apps", "took a day to setup". Ship default categories and a 3-step onboarding. | [Certain] |
| 20 | Smooth performance | Stutter and unresponsive bottom nav reported in competitors. | [Certain] |
| 21 | Home screen widget | Quick add from home screen. | [Guessing] |
| 22 | Dark mode | Listed as a selling point by Cashew. | [Likely] |

## What I'd build first

Superseded by `feature-ranking-and-ui-animation-plan.md`, which checked the app code: transfers and recurring already exist.

1. (Done in app) Transfers + recurring transactions.
2. Encrypted backup/restore to a file and to the user's own Google Drive. Offline-only is a privacy win until the phone dies; then it's the worst review you'll get.
3. Detection dedupe and per-bank parser tests. Auto-detection is the India differentiator, so accuracy matters more than any new feature.
4. Subcategories and custom date range.

Skip for now: split expenses / shared wallets. They need a sync server, which breaks the "no servers" promise in the privacy policy.

## Next step to firm this up

Allow play.google.com and apps.apple.com in the environment's network settings, then pull 1 to 3 star reviews for Realbyte Money Manager, Money Lover, Wallet, Axio, and Cashew and count mentions per row above.

## Sources

- [Money Manager- Expense, Budget (Play Store)](https://play.google.com/store/apps/details?id=com.ithebk.expensemanager&hl=en)
- [Money Manager Expense Tracker (Play Store)](https://play.google.com/store/apps/details?id=com.frmobile.moneymanager)
- [Money Manager: Expense Tracker (Play Store)](https://play.google.com/store/apps/details?id=com.moneytrack.android&hl=en_IN)
- [Money Manager Expense & Budget reviews (App Store)](https://apps.apple.com/us/app/money-manager-expense-budget/id560481810?see-all=reviews)
- [Money Manager : Expense Tracker (Play Store)](https://play.google.com/store/apps/details?id=expense.tracker&hl=en_IN)
- [Top Android Apps for Expense Tracking in 2026](https://blog.orangedog.net/posts/top-android-apps-for-expense-tracking/)
- [Money Manager Expense & Budget Review 2026](https://appviewable.com/apps/app-money-manager-expense-budget/)
- [CHOICE: Realbyte Money Manager](https://www.choice.com.au/products/money/financial-planning-and-investing/creating-a-budget/realbyte-money-manager)
- [Vrid (Play Store)](https://play.google.com/store/apps/details?id=in.vrid)
- [RuTrack (hunted.space)](https://www.hunted.space/product/rutrack)
- [Finledge (hunted.space)](https://hunted.space/dashboard/finledge-expense-tracker)
- [Trakio (Product Hunt)](https://www.producthunt.com/products/trakio-2)
- [Team Blind: best free expense tracker app](https://www.teamblind.com/post/best-free-expense-tracker-app-kqt0fxjh)
- [Budget apps ranked 2026 (unstar.app)](https://unstar.app/blog/ynab-rocket-money-monarch-everydollar-copilot-budget-apps-ranked-2026)
- [Cashew on AlternativeTo](https://alternativeto.net/software/cashew--expense-budget-tracker/about)
- [Cashew reviews (grand-screen)](https://grand-screen.com/apps/cashew-expense-budget-tracker/reviews/)
- [Money Lover review (CompareHero)](https://comparehero.my/blog/money-lover-app-review-track-your-expenses)
- [Wallet by BudgetBakers (SuperMoney)](https://www.supermoney.com/reviews/budgetbakers)
- [Axio review (Yellow Slice)](https://www.yellowslice.in/blog/axio-app-review-by-yellow-slice-how-to-make-it-better)
