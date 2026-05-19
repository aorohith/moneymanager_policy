# Google Play — Notification access declaration

Money Manager uses Android’s **Notification Listener Service** to optionally detect transactions from banking and UPI apps.

**Package:** `com.rapps.moneymanager`  
**Component:** `com.rapps.moneymanager.SmsListenerService`  
**Manifest permission:** `android.permission.BIND_NOTIFICATION_LISTENER_SERVICE`

---

## Core functionality (use in Play Console form)

> Money Manager helps users track personal expenses. On Android, users can **opt in** by enabling Notification access in system settings. The App reads notifications **only** from a fixed allowlist of banking and payment apps (e.g. major Indian banks, PhonePe, Google Pay, Paytm) and **only** when the notification title or body contains transaction-related keywords (e.g. debited, credited, UPI, payment).  
>  
> Parsed amounts and merchants are shown for **user review** before being saved as transactions. Raw notification text is deleted after the user approves or skips an item, and old records are automatically purged.  
>  
> The App does **not** read SMS messages, does **not** upload notification content to our servers, and does **not** access notifications from non-financial apps on the allowlist.

---

## Why notification access (not SMS)

- Banks increasingly deliver alerts as **push notifications**, not SMS.  
- `READ_SMS` would access the entire SMS inbox; we deliberately avoid that permission.  
- Access is **user-controlled** and can be revoked in Android Settings at any time.

---

## Technical safeguards (for review / appeals)

1. **Package allowlist** — `SmsListenerService.kt` `trustedPackages` (HDFC, ICICI, SBI, Axis, Paytm, PhonePe, GPay, etc.).  
2. **Keyword gate** — Both allowlisted package **and** transaction keyword required (`debited`, `UPI`, `₹`, etc.).  
3. **No background upload** — `MethodChannel` forwards to Dart; `SmsIngestionService` writes only to local Isar DB.  
4. **Rate limit** — Max 20 notifications per 60 seconds.  
5. **Retention** — `rawText` cleared on approve/skip; `pruneOldParsedTransactions` removes stale rows.  
6. **In-app disclosure** — SMS onboarding screen states: *“100% on-device. Your bank messages never leave your phone.”*

---

## User-facing steps (document in Help / policy)

1. Open Money Manager → enable auto-detect / notification setup.  
2. Android opens **Notification access** settings.  
3. User toggles **Money Manager** on.  
4. User reviews pending detections before they affect the ledger.

---

## If Google requests a video or demo

Show:

1. Granting notification access in system settings.  
2. A sample bank notification creating a **pending** item (test device).  
3. Approve flow saving a transaction.  
4. Settings → notification access off → no new detections.  
5. Settings → Clear all data (optional).

Do **not** record real account numbers or full PAN/card numbers in demo media.
