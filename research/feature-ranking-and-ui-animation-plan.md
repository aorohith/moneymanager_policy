# Feature ranking and UI/animation plan

Date: 2026-10-29
App repo checked: `aorohith/money_manager` (Flutter, Riverpod, go_router, Isar, fl_chart, flex_color_scheme, Plus Jakarta Sans)

## Part 1: Feature ranking (keep it simple)

Rule used: add a feature only if it fixes a top complaint, fits the offline-only promise, and reuses code that already exists.

The code already has transfers (`transferGroupId`), recurring transactions (`RecurrenceType`, `recurrence_service.dart`), import duplicate detection, and multi-currency fields. So two items from the review list are done.

### Do now

| Rank | Feature | Why | Size |
|------|---------|-----|------|
| 1 | Backup and restore to a file | No backup exists in `lib/`. A lost or reset phone wipes everything. Isar can copy the DB to a file; share it with `share_plus`, restore with `file_picker`. Both packages are already in pubspec. | Small |
| 2 | Bill reminders from recurring entries | Recurrence and `flutter_local_notifications` both exist. Only the glue is missing. | Small |
| 3 | Dedupe between notification detection and file import | Same payment can arrive from a bank notification and later from a statement import. `duplicate_detector.dart` exists for import; reuse it for detected items. Verify first. | Small |

### Later, only if users ask

| Feature | Reason to wait |
|---------|----------------|
| Lent / borrowed tracking | Common in India, but can be a category + tag for now |
| Subcategories | Schema change to `CategoryModel` and every report |
| Home screen widget | Native code per platform |
| Receipt photos | New permission, privacy policy update |

### Don't build

| Feature | Reason |
|---------|--------|
| Split expenses / shared wallets | Needs a server. Breaks "data stays on device". |
| Live currency rates | Needs network calls. Manual rate is enough. |
| Bank sync | The #1 source of 1-star reviews in other apps |

## Part 2: UI and animation research

### What good finance apps do in 2026

- One big, readable number on home: what's left this month. [Likely]
- Calm colours. Harsh red everywhere makes people avoid opening the app. Use red only for overspend. [Likely]
- Adding an expense must take seconds, or people stop logging. [Likely]
- Glass/gradient visuals are popular in Dribbble concepts. Skip them: they cost performance on cheap Android phones and add little. [Guessing]

### Motion rules for a money app

- Motion explains a change. It never decorates. [Likely]
- Use the restrained motion style, not the bouncy "expressive" one, for everyday screens. Save bounce for one moment: goal reached. [Likely]
- Numbers must never look wrong mid-animation. Use tabular figures so digits don't jump width. [Certain]
- Respect "Remove animations" in Android accessibility: check `MediaQuery.disableAnimationsOf(context)`. [Certain]
- Pair key motions with haptics. The app already uses `HapticFeedback` in 10+ places; keep it consistent. [Certain]

### Package choice

| Option | Use? | Reason |
|--------|------|--------|
| Flutter built-ins (`AnimatedSwitcher`, `TweenAnimationBuilder`, `AnimatedList`, `Easing`) | Yes, main tool | Free, no dependency, already used in the code |
| `animations` (Flutter team) | Yes | Ready Material transitions: shared axis, fade through, container transform. Use **2.2.0**: 3.0.0 needs Dart 3.12, the app is on `^3.9.2`. |
| `lottie` | Keep for one asset, or remove | It's in pubspec but `assets/animations/` is empty and no code uses it. Dead weight today. |
| `flutter_animate` | No | Last release 2024-11-25. Built-ins cover the need. |
| `rive` | No | Needs a designer and the Rive editor. Overkill for this app. |

Net change: add one small official package, possibly remove one.

### What the code does today

- `AppDurations` tokens exist (`fast` 150ms, `standard` 300ms, `emphasis` 400ms). Good base.
- Each screen writes its own `AnimationController` (dashboard, budgets, donut chart, category bars, spending ring, splash, PIN pad). Same patterns repeated, no shared curves.
- No custom page transitions in `app_router.dart`. Every screen uses the platform default.
- `dashboard_screen.dart` is 1186 lines. Split it when touching it for motion work, not before.

## Part 3: Plan

Each phase is one PR. Ship and test on a low-end Android phone before starting the next one.

### Phase 1: Motion foundation (no visible change yet)

1. Add `AppCurves` next to `AppDurations` in `lib/core/constants/`: `standard`, `enter` (decelerate), `exit` (accelerate) built from Flutter's `Easing` constants.
2. Add `lib/core/widgets/animated_amount.dart`: counts from old to new value with `TweenAnimationBuilder`, uses `FontFeature.tabularFigures()`, formats through the existing `formatters.dart`.
3. Add a `motionEnabled(context)` helper that returns false when the system disables animations. Every new animation checks it.
4. Remove `lottie` from pubspec if Phase 4 won't use it.

Done when: `flutter analyze` is clean and nothing on screen changes.

### Phase 2: Navigation

1. Add `animations: ^2.2.0`.
2. Bottom nav tab switch: fade through.
3. Push to detail screens (account, category, goal): shared axis, horizontal.
4. Android back: enable predictive back via `PageTransitionsTheme` in `app_theme.dart`.

Done when: every route change animates the same way, and back gesture previews the previous screen.

### Phase 3: Key moments (highest value)

| Moment | Animation | File |
|--------|-----------|------|
| Balance on home | Count up to the new value after a transaction is added | `balance_card.dart` |
| Add transaction | Sheet slides up, amount field focused, success haptic and snackbar on save | `add_transaction_sheet.dart` |
| Transaction list | New row slides in, deleted row collapses, undo in snackbar | `transactions_screen.dart`, `transaction_tile.dart` |
| Budget bar | Fills on first view; colour shifts to warning at 80% and red only over 100% | `budget_card.dart` |
| Detected transaction inbox | Swipe to approve/reject, row collapses | `sms_inbox_screen.dart` |
| Empty states | Fade in icon + text, no looping animation | `empty_state.dart` |

### Phase 4: Charts and the one celebration

1. Use fl_chart's built-in swap animation when the period changes, instead of rebuilding the chart from zero.
2. Move donut and category bar controllers onto the shared curves and durations.
3. Goal reached: one short celebration (confetti via a small `CustomPainter`, or one Lottie file if `lottie` was kept). Plays once, never loops.

### Phase 5: Check

- Run in profile mode on a cheap Android phone. Target: no frame over 16ms on home and transactions.
- Turn on "Remove animations" in Android settings. App must still work and show final values instantly.
- Wrap animated charts and the balance card in `RepaintBoundary` if profiling shows repaint cost.

## Sources

- [Best Aesthetic Budget Apps for iPhone in 2026 (getfinny.app)](https://getfinny.app/blog/aesthetic-budget-apps-2026)
- [Maven finance tracker concept (Contra)](https://contra.com/p/lNeXq3kW-maven-finance-tracker-mobile-app-design)
- [Fintech business finance app concept (Contra)](https://contra.com/p/vlOZl2mR-fintech-mobile-app-design-for-business-finance-management)
- [Rive vs Lottie for Flutter in 2026 (DEV)](https://dev.to/mascotengine/rive-vs-lottie-which-animation-tool-should-flutter-developers-choose-in-2026-lp5)
- [High-quality animation in Flutter (Ambeego)](https://ambeego.substack.com/p/how-to-create-high-quality-animation)
- [Material 3 motion summary (skills.cat)](https://skills.cat/skills/shelbeely/shelbeely-agent-skills/material-design-3-motion)
- [Compose MotionScheme expressive reference](https://kotlinlang.org/api/compose-multiplatform/material3/androidx.compose.material3/-motion-scheme/-companion/expressive.html)
- [Micro-interactions in Material Design (UXPin)](https://www.uxpin.com/studio/blog/micro-interactions-ux/)
- [Fintech animation principles (playbooks.com)](https://playbooks.com/skills/dylantarre/animation-principles/fintech-banking)
- pub.dev API, checked 2026-10-29: `animations` 3.0.0 (2026-08-19, Dart ^3.12.0), 2.2.0 (Dart ^3.9.0); `lottie` 3.7.0; `flutter_animate` 4.5.2 (2024-11-25)
