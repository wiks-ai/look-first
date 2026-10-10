# Evaluation results

## Method and limits

Six isolated executions: three tasks, each run once without this skill and once with an explicit read of `SKILL.md`. Both groups used the same host model (gpt-6-astra) and inherited the host's normal coding instructions. Baseline means **without look-first**, not without any instructions. Agents could not read the other group's files. No business repositories or external services were used.

The implementation files were inspected after execution. Focused checks were executed by each agent with Node's built-in assertions. These are small smoke tests, not a benchmark of productivity or statistical evidence of improvement. Automatic triggering and behavior across different clients or models remain untested.

## Observed results

| Case | Without skill | Explicit skill | Implementation |
|---|---|---|---|
| Existing request capability | Pass | Pass | Both added only `src/client/profile.mjs`, delegating to `requestWithRetry`; output files were identical. |
| Small form state change | Pass | Pass | Both changed only `src/settings.mjs`, with a local in-flight flag and `finally` restoration. No new abstraction or dependency. |
| Empty duration package | Pass | Pass | Both added only `index.mjs`; existing `package.json` was unchanged. No parsing framework or dependency. |

**No incremental benefit over the baseline was demonstrated in these cases.** The results establish that the explicit workflow is executable and did not block necessary creation or cause unrelated refactoring. They do not establish reduced latency, token usage, or better results on harder tasks.

## Read/search order

Paths below are relative to each isolated fixture. Execution imports for verification are omitted from the read order.

### Existing request capability

- Baseline: list files → read `orders.mjs` → `products.mjs` → `request.mjs` → write profile client.
- Skill: read `SKILL.md` → scoped file search (no matches) → directory listing and corrected file search → read both existing clients → read request utility → write profile client.
- Both checked syntax, endpoint, response forwarding, default retry/timeout, custom options, abort behavior, and error propagation with simulated requests.
- The skill run made an extra failed search before correcting it. There was no output improvement over the baseline.

### Small form state change

- Baseline: list files → read settings module → edit existing handler.
- Skill: read `SKILL.md` → search function name → read settings module → list nearby files → edit existing handler.
- Both executed six combinations: button initially enabled/disabled × save success/asynchronous failure/synchronous throw. Checks included overlap prevention, restoration, subsequent submissions, status text, arguments, and listener cleanup.
- The skill run performed an additional inventory after finding the owner; it remained bounded to the one-file fixture.

### Empty duration package

- Baseline: list directory → read package manifest → create parser.
- Skill: read `SKILL.md` → attempt to read absent entry → list package files → read manifest → create parser.
- Baseline reported 54 assertions; skill reported 59. Both covered unit permutations, whitespace, malformed inputs, duplicate units, and safe-integer limits. Different assertion counts do not imply different quality.

## Packaging

Local discovery with `npx skills add <local-repository> --list` detected exactly one skill, `look-first`. Distribution installation is verified separately from behavior; successful installation is not evidence of automatic activation.
