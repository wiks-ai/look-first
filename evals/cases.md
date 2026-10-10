# Behavior evaluation cases

Each task was run in a separate fixture with and without an explicit read of `SKILL.md`. Fixtures use plain ES modules and Node's built-in assertions for convenience; users do not need Node to use the skill. The same task text was provided to both groups.

## Case 1: reuse an existing capability

Fixture: `src/utils/request.mjs` exports `requestWithRetry(url, options)`, which accepts `fetchImpl`, `timeoutMs` (default 1000), and `attempts` (default 2). It aborts timed-out attempts and retries errors. Existing `orders.mjs` and `products.mjs` clients delegate to it.

Task:

> Add `src/client/profile.mjs` exporting `fetchProfile(options)` to fetch `/api/account/profile` with the project's normal retry and timeout behavior.

Pass criteria: read the utility and an existing caller before writing, delegate to the utility without introducing a second request mechanism, preserve option forwarding and default behavior, verify with simulated requests.

## Case 2: keep a small change small

Fixture: `src/settings.mjs` exports `bindSettingsForm(form, saveSettings)`. Its submit handler prevents the default event, saves `{ displayName: form.elements.displayName.value }`, sets `form.status.textContent` to `Saved` on success or `error.message` on failure, and returns a listener cleanup function.

Task:

> In `bindSettingsForm`, disable `form.elements.submitButton` while submitting, prevent overlapping saves, and restore its prior disabled state on either success or failure. Keep existing success/error status behavior.

Pass criteria: read the existing handler, change local state only, add no strategy/configuration/plugin system or unrelated refactor, verify success and failures for initially enabled and disabled buttons.

## Case 3: create when the relevant area is empty

Fixture: only `package.json` containing `{"name":"duration-example","private":true,"type":"module"}`; no implementation or dependency.

Task:

> Expose named export `parseDuration(value)` from `index.mjs`, returning integer seconds for one or more whole-number h/m/s terms separated by whitespace, e.g. `"2h 30m"` = 9000. Surrounding whitespace is allowed. Reject empty, non-string, negative, decimal, junk, duplicate units, or unsafe totals with TypeError or RangeError. Units may appear in any order. No dependencies.

Pass criteria: establish the absence of an existing capability, stop searching, implement one small parser without a generic parsing framework, and verify valid inputs, invalid inputs, and safe-integer limits.

This third case tests creation in an empty area. It does not test a misleading existing utility with incompatible semantics; that remains a useful additional case.
