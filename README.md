# Privacy materials (Google Play)

Documentation for publishing **Money Manager** (`com.rapps.moneymanager`) on Google Play.

## What’s in this folder

| File | Purpose |
|------|---------|
| [PRIVACY_POLICY.md](./PRIVACY_POLICY.md) | Full privacy policy (Markdown). Source of truth. |
| [privacy-policy.html](./privacy-policy.html) | Same policy as HTML — host this for the Play Console **Privacy policy URL**. |
| [playstore-data-safety.md](./playstore-data-safety.md) | Answers for **App content → Data safety** (derived from code review). |
| [playstore-notification-access.md](./playstore-notification-access.md) | Justification text for **Notification access** (sensitive permission). |

## Code review summary (May 2026)

- **No backend / no cloud sync.** All finance data stays in the local Isar database on the device.
- **No ads, no third-party analytics SDKs, no Firebase.** In-app “Analytics” is spending charts only.
- **Auto-detect (Android):** Uses `NotificationListenerService`, **not** `READ_SMS`. Only notifications from an allowlisted set of banking/UPI apps **and** containing transaction keywords are processed (`SmsListenerService.kt`).
- **Optional profile:** Display name and avatar color in `SharedPreferences` only.
- **Security:** App PIN stored as PBKDF2 hash in `FlutterSecureStorage`; optional biometric unlock via OS APIs (`local_auth`).
- **Retention:** Parsed notification bodies are cleared after review; old rows are purged (`SmsRepository.pruneOldParsedTransactions`).
- **User export/import:** CSV/PDF export and file import are **user-initiated** via system pickers / share sheet.
- **Fonts:** `google_fonts` may download Plus Jakarta Sans from Google servers on first use (no personal data sent).

## Before you submit

1. **Developer contact** (already set): Rohith A O — rohithao.dev@gmail.com (no website).
2. **Host the policy** at a public HTTPS URL (required by Play):
   - GitHub Pages: enable Pages on `main` / `docs` and point to `privacy/privacy-policy.html`, or
   - Copy HTML to your website, or
   - Use a gist / Notion public page (must be stable URL).
3. In Play Console → **App content**:
   - Paste the hosted URL under **Privacy policy**.
   - Complete **Data safety** using [playstore-data-safety.md](./playstore-data-safety.md).
   - Complete **Sensitive app permissions** / notification access using [playstore-notification-access.md](./playstore-notification-access.md).
4. **Account note:** If Play Console shows “You need permission to create new apps,” ask the account **Admin** to grant **Create app** (or create the listing for you). A suspended app on the same account does not block a new app, but policy/compliance history may affect review.

## Suggested Play Console declarations (high level)

- **Data collected:** Yes — financial info, optional name; all **on-device**, not transmitted to developer servers.
- **Data shared with third parties:** No (for your servers — you have none). Disclose that **export/share** sends data to apps the user chooses.
- **Encryption in transit:** N/A for app backend (none). User exports use the OS share channel.
- **Encryption at rest:** PIN uses platform secure storage; database is app-sandboxed (not E2E encrypted by default — state this honestly in Data safety if asked).
- **Account deletion:** Users can **Clear all data** in Settings (wipes prefs + secure storage keys you own + Isar DB when implemented in settings flow).

## Regenerating HTML from Markdown

If you edit `PRIVACY_POLICY.md`, update `privacy-policy.html` to match (or use a static site generator). Keep both in sync so the hosted URL stays accurate.
