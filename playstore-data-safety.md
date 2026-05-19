# Google Play — Data safety questionnaire

Use this when completing **Play Console → App content → Data safety**.  
Answers reflect the codebase as of May 2026 (`com.rapps.moneymanager`).

> **Important:** Play Console wording changes. Map each bullet to the closest official category. If unsure, choose the more transparent option.

---

## Top-level questions

| Question | Answer | Notes |
|----------|--------|-------|
| Does your app collect or share any of the required user data types? | **Yes** | Data is processed on-device; you still declare types users enter or that notifications provide. |
| Is all of the user data collected by your app encrypted in transit? | **Not applicable** or **No** | No data is sent to *your* servers. If Play forces an answer for “in transit,” explain: no developer backend; optional user export uses OS channels. |
| Do you provide a way for users to request that their data is deleted? | **Yes** | Settings → clear all data; uninstall. |
| Is your app’s data collection required or optional? | **Mixed** | Core manual tracking works without notification access. Auto-detect is optional (Android). |

---

## Data types to declare

### Financial info — **Collected** (not shared with you as developer)

| Field | Detail |
|-------|--------|
| Types | Purchase history / other financial info (transaction amounts, merchants, accounts, budgets) |
| Collected | Yes |
| Shared | **No** (with third parties for your business — you have no server) |
| Processed ephemerally | No |
| Required | Optional for notification path; required only if user uses those features |
| Purpose | App functionality |
| Collection | User-provided + optional notification parsing (Android) |

### Personal info — **Collected** (optional)

| Field | Detail |
|-------|--------|
| Types | Name (display name only, if user sets profile) |
| Collected | Yes, optional |
| Shared | No |
| Purpose | App functionality (personalisation) |

### App activity — **Collected** (Android optional feature)

| Field | Detail |
|-------|--------|
| Types | Other user-generated content — parsed notification metadata (merchant, amount) |
| Collected | Yes, only if user grants Notification access |
| Shared | No |
| Purpose | App functionality (transaction suggestions) |

### Authentication / security — **Collected**

| Field | Detail |
|-------|--------|
| Types | Not “account credentials” for a remote service — declare under **App info and performance** or skip if no matching type; PIN is local only |
| Practice | Store as **hashed PIN** in secure storage — do **not** declare as plain-text password collection |

If Play lists **“Other”** under personal or financial, use it for: PBKDF2 PIN hash, lockout timestamps (non-sensitive).

---

## Data types **NOT** collected (verify “No”)

- Location (precise or coarse)  
- Contacts  
- Photos and videos  
- Audio files  
- Files and docs — **except** user-selected import files (declare only if Play asks about “files”; collection is user-initiated, on-device read)  
- Calendar  
- Web browsing history  
- Health and fitness  
- Messages / SMS — **No** (`READ_SMS` not used)  
- Phone number, device ID for ads  
- Advertising ID  

---

## Data sharing

| Question | Answer |
|----------|--------|
| Do you share data with third parties? | **No** for developer/analytics/ad partners |
| User-initiated share (export PDF/CSV) | Disclose in policy; Play may ask under “Is data shared?” — typically **not** “shared” in Play’s sense if user chooses destination each time. If forced, note: **Users may share exports via the system share sheet; we do not receive that data.** |

---

## Security practices (if shown)

| Practice | Answer |
|----------|--------|
| Data encrypted at rest | Partial / device-dependent — PIN in secure storage; main DB in app sandbox (not custom E2E encryption) |
| Users can request deletion | Yes — in-app clear + uninstall |
| Committed to Play Families Policy | Only if you target children (you should not) |

---

## Permissions linkage

| Android capability | Data safety link |
|--------------------|------------------|
| `BIND_NOTIFICATION_LISTENER_SERVICE` | Financial info + app activity; optional; see [playstore-notification-access.md](./playstore-notification-access.md) |
| `POST_NOTIFICATIONS` | No extra data type; local notifications only |

---

## iOS (App Store Connect privacy labels)

| Category | Answer |
|----------|--------|
| Data linked to user | Financial data, user content — **on device only** |
| Tracking | No |
| Face ID | Purpose string already in `Info.plist` (`NSFaceIDUsageDescription`) |
| Notification listener | N/A on iOS for this feature |

---

## Checklist before submit

- [ ] Privacy policy URL live (HTTPS)  
- [ ] Placeholders replaced in policy  
- [ ] Data safety answers match policy text  
- [ ] Notification access declaration filed (Android)  
- [ ] Short description / full description mention on-device processing  
- [ ] Screenshots do not imply cloud sync unless you add it later  
