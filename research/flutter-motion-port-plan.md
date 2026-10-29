# Plan: port the motion preview to Flutter

Date: 2026-10-29
Source: `research/motion-preview.html` (published preview)
Target repo: `aorohith/money_manager`, commit `dd21010`

## The main point

Don't convert the HTML into new screens. The app already has every screen in the preview, and 6 of the 8 animations already exist in some form. The job is to change existing widgets so they behave like the preview. That keeps the change small.

Only motion and small visual details move over. The HTML layout, the Planned/None toggle and the slow-motion checkbox stay in the preview. In the app, "None" comes from Android's "Remove animations" setting.

## What exists today vs the preview

| # | Preview demo | In the app now | Gap |
|---|---|---|---|
| 1 | Balance counts to new value | `_DigitRollBalance` in `balance_card.dart` already tweens (400 ms, easeOutCubic) | No tabular figures, so digits shift width. No "+₹450" chip. Ignores reduce-motion. |
| 2 | Tab switch fade through | `ShellRoute` + `NavigationBar` in `app_router.dart`. Tab body swaps instantly. | Needs a fade-through page transition on the 5 tab routes. |
| 3 | Detail screen shared axis | Nested routes (analytics category, goal detail, account detail) use the platform default transition | Needs a shared-axis page on those routes. |
| 4 | List add / delete + Undo | `TransactionTile` uses `Dismissible` with a "This action cannot be undone" dialog. The repo already soft-deletes (`isDeleted`). | Swap the dialog for an Undo snackbar. Add an entrance animation for new rows. |
| 5 | Budget bar colours | `_AnimatedProgressBar` already animates. 4 colour states exist (`budgetLow/Mid/High/Over`). | Thicker bar (6 to 10), status chip text, colour fades instead of switching. |
| 6 | Swipe detected payments | `sms_inbox_screen.dart` has approve/skip buttons only | Add swipe right = approve, left = skip, using the same functions. |
| 7 | Donut morphs between months | `AnimatedDonutChart` and `SpendingRing` reset to 0 and sweep again on every change | Animate from the old slices to the new ones. |
| 8 | Goal reached confetti | Nothing | New small confetti widget, played once when a goal becomes complete. |

## Rules for every change

- Use the timings that already exist in `AppDurations`. Add one `AppCurves` file next to it with three curves: standard, enter, exit. Use the same curves the preview uses.
- Every animation checks the system "Remove animations" setting and goes straight to the end state when it is on. Today none of the existing animations check this.
- Every amount that animates uses tabular figures. The analytics screen already does this; balance, budgets and goals don't.
- No new packages except `animations` 2.2.0 (Flutter team). 3.0.0 needs a newer Dart than the app uses.
- Remove `lottie` from `pubspec.yaml`. Nothing uses it and the confetti will be drawn in code.
- One PR per step. Each PR passes `flutter analyze` and `flutter test` and is checked on a real phone before the next one starts.

## Steps

### Step 1: Foundation (no visible change)

- Add `AppCurves` in `lib/core/constants/`.
- Add one shared helper that answers "should this animate?" from the system setting.
- Add one shared "animated amount" text widget: counts from old to new value, tabular figures, currency symbol from settings.
- Remove `lottie`. Add `animations: ^2.2.0`.

Size: small. Risk: none.

### Step 2: Navigation (demos 2 and 3)

- Tab routes in the `ShellRoute` switch from `builder` to a page builder with the fade-through transition.
- Nested detail routes (analytics category, goal detail, account detail) get the shared-axis horizontal transition.
- Keep the current `ShellRoute`. Moving to `StatefulShellRoute` would keep each tab's scroll position, but it is a bigger change and not needed for the animation.

Size: small. Risk: back-button handling in `_AppShell` (`PopScope`) must still work. Test the Android back gesture on every tab.

### Step 3: Balance and budgets (demos 1 and 5)

- `_DigitRollBalance` uses the shared animated amount widget. Add the small "+₹450 / −₹450" chip that floats up after a transaction is saved.
- Budget bar goes from 6 to 10 px. Add the status chip ("On track", "Close to limit", "Over by ₹X"). The colour fades between states.

Size: small.

### Step 4: Delete with Undo, and new-row entrance (demo 4)

- Remove the "cannot be undone" dialog. Swipe deletes right away and shows a snackbar with Undo for 4 seconds.
- Undo needs one new use case that sets `isDeleted` back to false. The soft delete is already there.
- New rows: the list is a `ListView.builder` with date headers, fed by Riverpod, so a full animated list is hard to keep in sync. Simpler option: remember the id of the transaction that was just added, and only that tile plays the slide-in when it first appears.

Size: medium. Risk: if a user deletes and then leaves the screen, the snackbar must still let them undo, or the delete must still be final. Decide on one.

### Step 5: Swipe in detected inbox (demo 6)

- Wrap each inbox tile in a swipe with green "Add" and red "Ignore" backgrounds.
- Swipe right calls the existing `_approve` with the suggested category. If there is no suggested category, open the merchant sheet instead of approving.
- Swipe left calls the existing `_skip`.
- Keep the buttons for people who don't swipe.

Size: small.

### Step 6: Donut morph (demo 7)

- Both donut painters keep the previous slice values. On change they animate from the old angles to the new ones, instead of resetting to 0.
- Slices that are new grow from 0; slices that disappear shrink to 0.
- The centre total uses the shared animated amount widget.

Size: medium. It touches two painters (`donut_chart.dart`, `spending_ring.dart`). Consider making them one shared painter in this step, since they do the same thing.

### Step 7: Goal celebration (demo 8)

- New confetti widget in `lib/core/widgets/`, drawn with a custom painter, about 1.5 seconds, plays once.
- Fires only when a contribution changes a goal from not complete to complete, on the goal detail screen. No setting stored; it is a one-time event.
- The "Goal reached" badge pops in with the only bouncy curve in the app.
- Skipped completely when "Remove animations" is on.

Size: small.

### Step 8: Check

- Profile mode on a low-end Android phone: no dropped frames on home, transactions and analytics.
- Turn on "Remove animations": every screen still works and shows final values right away.
- Light and dark theme pass on each changed screen.
- Widget tests: animated amount shows the final value when animations are off; Undo restores a deleted transaction; inbox swipe calls approve/skip.

## Decisions needed before Step 4

| Question | My recommendation |
|---|---|
| Replace the delete confirmation dialog with Undo? | Yes. Soft delete already exists, so the dialog only adds a tap. |
| Budget colours: keep the app's 4 states or use the preview's 3? | Keep 4. The code already has them. The preview simplified. |
| Swipe right on a detected payment with no category? | Open the merchant sheet. Never save to a guessed category. |

## Bugs spotted while reading (not part of this plan)

- `sms_inbox_screen.dart` `_approve` always saves `isIncome: false`, so a credited UPI payment would be saved as an expense. It also always uses the first account.
- `goal_card.dart` and `goal_detail_screen.dart` show `$` hard-coded instead of the user's currency symbol.

## Blocker

This session can read `aorohith/money_manager` but cannot push to it. To build Step 1, either give this session push access to that repo or start a session on it with this file as the brief.
