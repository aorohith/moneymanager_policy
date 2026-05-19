# Privacy Policy — Money Manager

**Effective date:** May 19, 2026  
**App:** Money Manager  
**Package name:** `com.rapps.moneymanager`  
**Developer:** Rohith A O  
**Contact:** rohithao.dev@gmail.com

---

## 1. Introduction

Money Manager (“the App”) is a personal finance application that helps you track spending, budgets, and goals. This policy explains what information the App processes, how it is stored, and what choices you have.

**Summary:** The App is designed to work **entirely on your device**. We do not operate servers that collect or store your financial data. We do not sell your data.

---

## 2. Information we process

### 2.1 Information you enter

- Transactions (amount, date, category, account, notes)
- Accounts, categories, budgets, and savings goals
- Optional display name and profile color
- Currency and display preferences
- Import files you explicitly select (e.g. CSV, Excel, PDF bank statements)

### 2.2 Information detected on Android (optional)

If you enable **Notification access** for the App, it reads **notification title and text** from a fixed list of banking and payment apps (e.g. UPI and mobile banking apps) **only when** the notification appears to describe a transaction (e.g. debited, credited, UPI, ₹).

From those notifications the App may derive:

- Transaction amount and date  
- Merchant or description  
- Payment method (e.g. UPI, card)  
- Limited hints (e.g. last digits of an account) if present in the notification  

You review detected items before they become permanent transactions. You can disable access at any time in Android **Settings → Apps → Special app access → Notification access**.

The App does **not** request the `READ_SMS` permission and does **not** read your SMS inbox.

### 2.3 Security and device data

- **App PIN:** Stored only as a salted hash (PBKDF2) in the device’s secure storage (Android EncryptedSharedPreferences / iOS Keychain). We never store your PIN in plain text.
- **Biometric unlock (optional):** If you enable it, authentication is handled by your device’s operating system (fingerprint / face). Biometric data never leaves the secure enclave and is not accessible to us.
- **Lockout counters:** Failed PIN attempt counts and lockout timers are stored locally in app preferences.

### 2.4 What we do not collect

- No account registration with us  
- No cloud backup of your ledger to our servers  
- No advertising identifiers for ad targeting  
- No contact list, photos, microphone, or location tracking for core features  

---

## 3. How we use information

We use processed information **only on your device** to:

- Display balances, charts, and reports  
- Categorise and reconcile transactions  
- Send **local notifications** (e.g. budget alerts) if you allow notification permission  
- Export or share data **when you choose** (CSV/PDF via the system share sheet)  

We do **not** use your data for advertising or sell it to third parties.

---

## 4. Storage and retention

| Data | Where stored | Retention |
|------|----------------|-----------|
| Transactions, accounts, budgets, goals | Local database (Isar) on device | Until you delete them or use **Clear all data** |
| App settings & optional profile name | Local preferences | Until cleared |
| PIN hash | Platform secure storage | Until you change/clear PIN or reset app |
| Pending auto-detected notifications | Local database | Raw notification text removed after you approve/skip; old records purged automatically (reviewed ~30 days, pending ~90 days) |

Uninstalling the App removes app data from your device subject to Android/iOS backup behaviour.

---

## 5. Sharing and third parties

**We do not transmit your financial data to our servers** because we do not operate syncing backends for this App.

Data may leave your device only when **you** take an action:

- **Export / Share:** You share a CSV or PDF to another app (email, Drive, etc.). That recipient’s privacy policy applies.  
- **Import:** You select a file from storage; only that file is read.  
- **Fonts:** The App may download open-source fonts (Plus Jakarta Sans) from Google Fonts infrastructure for typography. This request does not include your transaction data.  

The App does not embed third-party analytics or advertising SDKs in its current version.

---

## 6. Permissions

| Permission / access | Why |
|---------------------|-----|
| Notification access (Android) | Optional: read banking/payment **notifications** to suggest transactions |
| Post notifications (Android 13+) | Optional: local alerts |
| Biometric / Face ID (optional) | Unlock the App |
| Storage / file picker | User-initiated import; export to files you choose |

You can revoke permissions in system settings at any time. Some features will stop working if access is revoked.

---

## 7. Children

The App is not directed at children under 13 (or the minimum age in your country). We do not knowingly collect personal information from children.

---

## 8. Your rights and choices

Depending on your region, you may have rights to access, correct, or delete personal data. Because data stays on your device, you can:

- Edit or delete transactions, accounts, and categories in the App  
- Turn off notification-based detection in Android settings  
- Use **Clear all data** in Settings to reset the App  
- Uninstall the App  

For requests you cannot perform in the App, contact us at rohithao.dev@gmail.com.

---

## 9. Security

We use industry-standard practices appropriate for a local-first app: hashed PIN, platform secure storage, and app sandboxing. No method of storage is 100% secure; protect your device with a screen lock and do not share your PIN.

---

## 10. International users

Data is processed on your device in the country where you use the phone. We do not transfer data to our own servers across borders.

---

## 11. Changes

We may update this policy. We will post the new version at the same URL and update the effective date. Continued use after changes means you accept the updated policy.

---

## 12. Contact

Rohith A O  
Email: rohithao.dev@gmail.com
