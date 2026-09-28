# Master Product Requirement Document (PRD)

## Changelog
- **2026-09-28**: Reorganized document into standard `/docs/PRD.md` location, linked user stories (`docs/user-stories/`), flows (`docs/flows/`), and business rules (`docs/rules/`).
- **2026-09-22**: Documented Rapor Financial Reporting Suite and dark analytical charts.
- **2026-09-16**: Synchronized functional specifications with Plane.so issue tracker and User Stories US-001 through US-017.

**Project Name:** Stir — Indonesian-Native AI Personal Finance Manager  
**Target Platform:** Mobile-First (Android / Google Play) + WhatsApp Bot Frontend + Desktop Web Dashboard  
**Target Audience:** Indonesian Working Class, Fresh Graduates, and Young Professionals

## **1\. Product Vision & Core Architecture**

This app rejects traditional, high-friction accounting schemas and aggressive SaaS paywalls. It is engineered as a **zero-friction financial tracking ecosystem** tailored specifically to Indonesian spending behavior, payment fragmentation, and social dynamics.

### **The Triad Frontend Strategy**

1. **Primary Input Layer (Asynchronous WhatsApp Bot):** A low-friction interface where users log daily transactions via everyday Indonesian slang, voice notes, or e-wallet/QRIS screenshots. Operates on an asynchronous "Daily Digest" model to reduce notification fatigue and slash API token costs.  
2. **Secondary Management Layer (Android App \- Google Play):** A 16-screen interactive hub for visual dashboards, two-level budgeting, debt management, goal tracking, custom manual input (IDR-native numpad), and social split-bill interactions.  
3. **Tertiary Command Center (Web Dashboard):** A desktop-optimized portal focused strictly on multi-chart wealth analytics, bulk e-Statement PDF ingestion, and tax-ready Excel/PDF audit exports.

## **2\. Comprehensive Feature Breakdown & System Mechanics**

### **A. Automated Recurring Expenses & Admin Fees (NEW)**

* **The Problem:** Bank admin fees (*biaya admin bulanan BCA/Mandiri*), e-wallet top-up deductions, and digital subscriptions occur silently. Forcing users to manually log a Rp 2.500 admin fee creates friction, while ignoring it causes wallet balances to drift over time.  
* **The Solution:** An automated recurring ledger with intelligent background execution.  
* **System Logic:**  
  * **Creation:** When logging a transaction, users can toggle \[Make Recurring\] and select frequencies: *Daily, Weekly, Monthly, or Endless / Auto-Deduct* (for bank fees).  
  * **The "Silent Admin" Template:** Pre-built templates during onboarding for common Indonesian banks (e.g., *"Potong Rp 15.000 dari BCA setiap tanggal 20"*).  
  * **Execution:** A background cron job executes the deduction automatically at 00:01 AM on the due date.  
  * **User Notification:** Recurring deductions are bundled into the evening WhatsApp Daily Digest: *"💡 Info: Biaya admin BCA Rp 15.000 & Langganan Spotify Rp 55.000 otomatis tercatat hari ini."*

### **B. Asynchronous WhatsApp Logging ("Daily Digest" Model)**

* **The Problem:** Real-time conversational AI bots cause notification spam and drive up LLM API token costs per user.  
* **The Solution:** A "chat-to-yourself" fire-and-forget ingestion engine.  
* **System Logic:**  
  * **Daytime Input:** Users text multiple unstructured messages throughout the day (*"makan gopay 55rb"*, *"parkir 15rb"*). The system acknowledges receipt silently with a minimalist 👍 reaction emoji. No instant chat replies.  
  * **Batch Processing:** At a user-defined time (e.g., 8:00 PM WIB) or via a /recap command, the backend concatenates the day's raw strings into a single LLM prompt, extracting amounts, accounts, and categories in one cost-effective API call.  
  * **Evening Output:** The bot delivers a single structured summary with remaining daily safe-to-spend budgets and quick-action correction links.

### **C. Indonesian Payment Fragmentation & "Catch-Up Mode"**

* **The Problem:** Transacting across 6–10 payment methods (GoPay, OVO, ShopeePay, BCA Mobile, Livin' Mandiri, Cash) leads to reconciliation fatigue and Month 1 app abandonment.  
* **The Solution:** Batch historical logging via document parsing.  
* **System Logic:**  
  * Users upload monthly bank e-Statements (PDFs) or batch-forward up to 20 transaction screenshots to the bot.  
  * An OCR/Vision pipeline extracts timestamps, merchants, amounts, and wallets, auto-mapping them against a localized Indonesian merchant database (e.g., *"SPBU PERTAMINA"* $\\rightarrow$ Fuel; *"STARBUCKS"* $\\rightarrow$ Coffee).  
  * Users review and approve a full month of data in under 10 seconds.

### **D. The "Bayarin Dulu" (Split-Bill) & Social Ledger**

* **The Problem:** Upfront group dining payments (e.g., paying Rp 500.000 via credit card/QRIS) artificially spike budget expense reports, and subsequent cross-account reimbursements from friends skew income statements.  
* **The Solution:** Isolating split-bill portions into a temporary virtual asset ledger (Virtual\_Pocket: Piutang).  
* **System Logic:**  
  1. **Upfront Outflow:** User logs *"McD 500k BCA (bayarin 350k)"*. System deducts 500k from BCA, writes 150k to Expense: Food, and moves 350k to Virtual\_Pocket: Piutang.  
  2. **Reimbursement:** Friend transfers 100k to GoPay. User logs *"Budi bayar utang 100k ke GoPay"*. System adds 100k to GoPay and deducts 100k from Piutang. **Zero impact on monthly Income/Expense reports.**  
  3. **Automated Nagging (*Tagih Temen via WA*):** 1-click button in the app that triggers the bot to send a polite, humorous payment reminder with an embedded payment link/QRIS to the debtor, eliminating social awkwardness (*sungkan*).  
  4. **Bad Debt Write-Off (*Ikhlasin*):** Receivables unsettled after \>60 days trigger a UI prompt. Tapping *Ikhlasin* converts the remaining balance from Piutang into Expense: Social / Donation.

### **E. Guilt-Free Balance Reconciliation ("Uang Gaib")**

* **The Problem:** Small cash discrepancies (*Pak Ogah* parking, uncounted snacks) cause users to abandon apps when real-world balances do not match app ledgers.  
* **The Solution:** 1-click balance syncing that preserves data integrity.  
* **System Logic:**  
  * Users tap \[Sesuaikan Saldo\] on any wallet card. If the app says Rp 1.450.000 but the real balance is Rp 1.300.000, the system generates a balancing entry of \-Rp 150.000 categorized as Uang Gaib / Lupa Catat.  
  * **Database Rule:** Entry is tagged with is\_reconciliation\_adjustment \= true.  
  * **Analytics Rule:** Category breakdown charts (e.g., Food, Shopping) **exclude** these rows so behavioral ratios remain unpoisoned. Net worth and total cash flow dashboards **include** them so bank balances match reality.  
  * **Sherlock Mode:** Discrepancies \>Rp 500.000 trigger an AI investigative prompt suggesting checks on unrecorded *Bayarin Temen* transactions or recent bank mutation scans.

### **F. Explicit Transfer vs. Expense Segregation**

* **The Problem:** Salary routing (e.g., Payday salary arrives in BCA $\\rightarrow$ 30% moved to Bibit mutual funds $\\rightarrow$ 20% to GoPay) is frequently misclassified by standard apps as double income or false expenses (*Gaji Numpang Lewat*).  
* **The Solution:** Dedicated UI flows and AI intent recognition that strictly categorizes internal wallet-to-wallet movements and investments as **Wealth Transfers**, keeping monthly cash-flow burn rates accurate.

### **G. Safe Debt & "Pinjol" Installment Tracker**

* **The Problem:** Users avoid tracking SpayLater, Kredivo, credit cards, or legal P2P loans due to shame and fear of illegal payday loan (*Pinjol*) data harvesting.  
* **The Solution:** A judgment-free debt snowball tracker backed by strict privacy guarantees.  
* **System Logic:**  
  * **Anti-Pinjol Pledge:** A prominent banner guaranteeing 100% passive data usage, localized/encrypted storage, and zero contact-list scraping.  
  * **Snowball Dashboard:** Displays exact installment schedules and dynamically calculates a celebratory **"Bebas Utang Date"** (Debt-Free Date).  
  * **Bill Radar:** Automatically isolates fixed monthly obligations (rent, debt, utilities) from salary on Day 1, presenting only the **True Liquid Disposable Income** for daily spending.

### **H. Two-Level Budgeting & Rebalancing**

* **The Problem:** Rigid category budgets cause anxiety when unexpected expenses occur, while lack of overall caps leads to overspending.  
* **The Solution:** A dual-layer safety net combining macro monthly caps with flexible micro-categories.  
* **System Logic:**  
  * **Level 1 (Macro):** Total Monthly Spending Target (e.g., Rp 8.000.000). This is the hard guardrail.  
  * **Level 2 (Micro):** Individual Category Caps (e.g., Food Rp 3.000.000).  
  * **Rebalancing Flow:** If Food overspends by Rp 200.000 but Shopping is underspent by Rp 900.000, the system suppresses warning alerts and presents a supportive UI prompt: *"Budget Makan lewat 200rb, tapi total pengeluaranmu aman\! Mau geser sisa budget Belanja ke Makan?"*

### **I. Wishlist Pockets & Gamified Retention (Defeating Month 3 Churn)**

* **The Problem:** Expense tracking alone becomes boring by Month 3 once users understand their baseline habits.  
* **The Solution:** Tying daily discipline directly to visual personal dreams using Duolingo-style gamification.  
* **System Logic:**  
  * **Tabungan Impian:** Users create visual target pockets (e.g., "iPhone 18 \- Rp 18.000.000") with custom images.  
  * **Positive Reinforcement:** Underspending in a category prompts an immediate transfer suggestion: *"Kamu hemat Rp 200rb di budget makan minggu ini\! Masukkan ke pocket iPhone 18? Jarak impianmu maju 4 hari\!"*  
  * **Hyper-Specific AI Insights:** Time-series clustering identifies specific behavioral leaks: *"Dalam 2 minggu terakhir, 80% jajan kopimu terjadi antara jam 14:00-16:00 WIB (Total Rp 380rb). Kalau dikurangi separuh, iPhone 18 bisa kebeli sebulan lebih cepat\!"*

### **J. Notification Governance & "Tutup Buku"**

* **The Problem:** Automated alerts congratulating users on "low spending" look foolish if the user simply forgot to log their transactions.  
* **The Solution:** Strict separation between active velocity tracking and historical reporting.  
* **System Logic:**  
  * **Mid-Month Alerts:** System monitors **Logging Velocity**, not total spend. A 72-hour logging gap triggers a gentle nudge: *"Bro, udah 3 hari nggak ada catatan masuk. Benar-benar lagi hemat, atau ada struk yang lupa dicatat?"*  
  * **Historical Comparisons:** Month-over-month spending comparisons are suppressed until the user explicitly taps \[Tutup Buku / Close Month\] at the end of the billing cycle, confirming data completeness.

## **3\. Onboarding & UI/UX Guidelines**

### **The 60-Second AI Hook Onboarding**

1. **"Roast My Vibe" Quiz (10s):** Self-selection of financial sins and system tone (Gentle Coach vs. Savage Roaster / Strict Akuntan vs. Pragmatic Santai). Sets humor\_style and strict\_mode database flags.  
2. **Interactive Sandbox (30s):** An active text box prompting: *"Test the AI. Type what you spent today in everyday slang (e.g.,* kopi 25rb gopay*)."*  
3. **The Payoff (20s):** Instant visual parsing animation, dashboard balance update, and localized AI reaction. User lands on an active dashboard with zero blank-state friction.

### **Progressive Categorization**

* **UI Default:** 5 Universal Macro-Buckets (Food & Drink, Transport & Bills, Lifestyle & Shopping, Financial & Savings, Income & Transfers).  
* **Silent Backend Sub-Tagging:** Natural language inputs are silently tagged with metadata in the database: \[Macro: Food & Drink\] \-\> \[Sub: Coffee\] \-\> \[Merchant: Kopi Kenangan\].  
* **On-Demand Unfolding:** Toggling \[Expand Sub-Categories\] in analytics dynamically generates granular charts from stored metadata without requiring manual onboarding setup.

### **IDR-Native Custom Numpad**

* A specialized mobile numeric keyboard replacing standard keyboards for manual data entry.  
* Eliminates trailing-zero fatigue by replacing standard decimal keys with dedicated 000 **(rb)** and 000.000 **(jt)** multiplier buttons.

## **4\. Technical Architecture & Ingestion Pipeline**

To ensure near-zero unit economics on free tiers, backend routing follows a strict tiered processing funnel:  
\[User Input: Text / Slang / Voice / Image via WA or App\]  
                              │  
                              ▼  
┌───────────────────────────────────────────────────────────┐  
│ Layer 1: Regex & Local Keyword Dictionary                 │  
│ Cost: $0 | Latency: \<10ms                                 │  
│ Handles structured strings (e.g., "kopi 25rb gopay")      │  
└─────────────────────────────┬─────────────────────────────┘  
                              │ (If syntax fails / Messy slang / Batch digest)  
                              ▼  
┌───────────────────────────────────────────────────────────┐  
│ Layer 2: Small Open-Weight LLMs (Llama 3 8B / Mistral)    │  
│ Cost: Micro-pennies via OpenRouter/Groq | Latency: \~500ms │  
│ Parses multi-item sentences and extracts intent           │  
└─────────────────────────────┬─────────────────────────────┘  
                              │ (If input is Image / QRIS / PDF Statement)  
                              ▼  
┌───────────────────────────────────────────────────────────┐  
│ Layer 3: Vision LLM / OCR Ingestion Engine                │  
│ Cost: Standard API rates (Reserved for Pro / Quoted)      │  
│ Extracts tabular data from e-Statements and receipts      │  
└───────────────────────────────────────────────────────────┘

## **5\. Mobile App Screen Architecture (16-Screen Limit)**

The Android application is strictly budgeted to 16 screens to prevent bloat, utilizing a 4-tab bottom navigation bar paired with context-aware slide-up bottom sheets.

### **Auth & Setup (3 Screens)**

1. **Auth & Vibe Setup:** Google Sign-in, humor style selection, strict/fast mode toggle.  
2. **Interactive AI Sandbox:** The 60-second live onboarding test.  
3. **Wallet Quick-Select:** Logo grid (GoPay, BCA, Cash, SpayLater) to activate initial accounts.

### **Main Navigation Bar (4 Tabs)**

4. **Home / Beranda:** Safe-to-spend daily countdown, Liquid vs. PayLater split card, Daily AI Roast, recent activity feed.  
5. **Aktivitas (Ledger):** Chronological transaction list, multi-parameter search/filter, Sherlock Mode discrepancy banners.  
6. **Piutang (Social Ledger):** Active debtors list, \[Tagih via WA\] automated reminder trigger, \[Terima Bayar\] settlement modal, \[Ikhlasin\] bad-debt write-off button.  
7. **Rapor (Analytics & Goals):** Macro-category donut chart, \[Expand Sub-Categories\] toggle, Tabungan Impian wishlist cards, Duolingo-style badge trophy case.

### **Action Modals / Bottom Sheets (4 Modals)**

8. **Quick-Log FAB Modal:** IDR Numpad (rb/jt), Expense/Income/Transfer selectors, Bayarin Temen contact assigner, **Recurring Expense toggle**.  
9. **Catch-Up Ingestion Modal:** PDF e-Statement upload zone, multi-image screenshot uploader, batch review/approve table.  
10. **Uang Gaib Reconciliation Modal:** Real vs. App balance comparison, 1-click adjustment button to zero out discrepancies.  
11. **Dompet Tunai Burn-Down Modal:** Petty cash countdown tracker with \[Dompet Tunai Habis\] automated historical distribution trigger.

### **Governance & Settings (5 Screens)**

12. **Wallets & PayLater Guardrail Hub:** Bank/wallet balance management, credit limit monitors, upcoming billing due-date countdowns.  
13. **Flexible Budget Hub:** Month 1 AI Observation status, Month 2+ macro/micro budget setups, interactive "Geser Budget" (rebalancing) cards.  
14. **Recurring Expense Manager (NEW):** Master list of automated subscriptions, silent bank admin fees, and scheduled salary routing.  
15. **Settings & Archetype Config:** Strict/Fast mode toggles, humor tone sliders, WhatsApp bot connection management.  
16. **Subscription & Viral Share Modal:** Free tier usage meter, Pro / Lifetime upgrade cards, 9:16 Instagram/TikTok meme and badge generator.

## **6\. Business & Monetization Model**

Tailored to Indonesian consumer purchasing psychology, avoiding mandatory monthly SaaS paywalls for basic access:

| Tier | Price | Included Capabilities |
| :---- | :---- | :---- |
| **Free Tier** | **Rp 0** | • Full offline/online mobile app access & IDR numpad (rb/jt) • Up to **30 WhatsApp AI text logs/month** (Daily Digest mode) • 5 Macro Categories, basic budgeting, and manual recurring rules |
| **Pro Sub** | **Rp 19.000/mo** *or*  **Rp 149.000/yr** | • **Unlimited** WhatsApp AI logging & automated *Tagih Temen* nagging • **Receipt & QRIS Screenshot OCR** via WhatsApp bot • **Full PDF e-Statement parsing** (*Catch-Up Mode*) • Multi-device sync, advanced Excel/PDF audit exports, & AI behavioral coaching |
| **Lifetime Pro** | **Rp 299.000**  *(One-time)* | • Full lifetime ownership of all Pro features. • Directly targets subscription-averse users while injecting upfront working capital to fund LLM API infrastructure. |

## **7\. Open R\&D Items for Future Development Sprints**

1. **Petty Cash Burn-Down Algorithm:** Researching behavioral patterns of physical cash usage across urban Indonesia (parking, street food, tips) to refine the automated distribution math when users trigger \[Dompet Tunai Habis\].  
2. **Notification Rate-Limiting Engine:** Establishing mathematical cooldown timers to guarantee that users experiencing an anomalous spending week are not overwhelmed by AI roasting alerts, protecting retention rates.  
3. **Offline-to-Online Deduplication:** Perfecting time-window and amount-matching algorithms to automatically catch and merge duplicate entries when a user logs manually offline in a mall basement and later forwards the digital e-wallet receipt via WhatsApp.

### **How to Use This Document with Your AI Agent / Developer:**

You can copy and paste this entire specification directly into an AI coding assistant (like Cursor, Devin, Claude, or GitHub Copilot) or hand it to a human engineering team. Start by instructing your developer/agent with the following prompt:  
*"Read this Master PRD carefully. We are building the MVP starting with the **Database Schema** and the **Layer 1/Layer 2 Ingestion Pipeline for the Asynchronous WhatsApp Bot**. Please generate the SQL database relational schema (including tables for Users, Accounts, Transactions, Virtual\_Pockets, Budgets, Wishlists, and Recurring\_Expenses with the appropriate boolean flags like* is\_reconciliation\_adjustment *and* strict\_mode*), and then outline the API routing logic for handling the 8:00 PM Daily Digest text processing."*  
