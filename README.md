# look-first

[English](README.md) · [简体中文](README.zh-CN.md)

An agent skill that makes code changes start with the codebase that already exists.

Before writing new code, it asks the agent to inspect the relevant implementation, look for a reusable capability, and choose the smallest design that solves the current task. It prevents duplicate helpers, premature abstraction, speculative configuration, and unrelated refactors without forcing reuse when the semantics do not fit.

## Install

```bash
npx skills add wiks-ai/look-first --skill look-first
```

Some clients can install a repository's default skill with:

```bash
npx skills add wiks-ai/look-first
```

## What it changes

The skill adds a bounded workflow:

1. Define the requested behavior and its change boundary.
2. Read the target and the most relevant callers, dependencies, and nearby patterns.
3. Reuse an existing capability when it fits.
4. Extend the existing owner when a small compatible change is enough.
5. Create a new implementation only when the semantics genuinely differ or the relevant area is empty.
6. Keep the design proportional and verify the affected behavior.

It does not require an API key, project configuration, a test suite, a specific language, or an external service. It uses the host agent's normal repository tools and stops searching when the design decision is clear.

## Before and after

Without the skill, an agent may add a second request wrapper because it did not inspect the existing utility, or introduce a strategy/registry layer for a one-call feature.

With the skill, the agent first checks the relevant code, reuses the existing wrapper or makes a small compatible extension, and implements a new function only when the old semantics do not match.

## Scope

`look-first` guides implementation decisions. It does not promise that every change will reuse existing code, prevent all duplication, or replace project-specific review and testing practices.

## License

MIT
