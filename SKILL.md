---
name: look-first
description: Before writing or changing code, inspect the relevant existing implementation, find reusable capabilities, and choose the smallest design that solves the current task. Use when implementing features, fixing bugs, refactoring, adding helpers, or deciding whether to create a new abstraction. Prevent duplicate code, premature abstraction, speculative configuration, and unrelated refactors while allowing a new implementation when existing semantics do not fit.
---

# Look First

Before writing new code, read enough of the existing code to know whether the project already has the capability. Prefer reuse, then a small compatible extension, and create something new only when the existing semantics genuinely do not fit.

This is a short decision loop, not an approval ceremony. For ordinary tasks, inspect the relevant area and keep working. Do not scan the whole repository or write a report unless the task actually needs it.

## Workflow

### 1. Bound the task

State the concrete behavior that must change or be added. Identify the target entry point, the behavior that must remain stable, and the smallest likely change surface. Do not expand a request into a redesign.

### 2. Look in bounded concentric circles

Read in this order, stopping when the answer is clear:

1. The target file and the code around the requested change.
2. Its direct callers, callees, and nearby tests or examples.
3. The most relevant project-wide names, utilities, components, dependencies, or patterns.

Use the repository's existing search and read tools. Search by behavior and domain terms as well as likely symbol names. If access is unavailable, say what could not be inspected and do not claim that reuse was checked.

### 3. Choose reuse before creation

Use this order of preference:

- **Reuse directly** when an existing function, component, command, dependency, or project mechanism already provides the required behavior.
- **Extend compatibly** when an existing implementation is the right owner and only needs a small capability added. Preserve existing callers and defaults.
- **Create minimally** when the existing behavior has different semantics, ownership, lifecycle, failure behavior, or compatibility requirements. Do not force unrelated behavior into an existing abstraction just to avoid a new file.

Do not implement a second request wrapper, logger, cache, error path, state store, formatter, validation layer, or configuration mechanism when the project already has one that fits.

When the choice materially affects the design, leave a brief note such as:

```text
Reuse: src/utils/request.ts::requestWithRetry
New: none
Smallest change: add the timeout option and cover its boundary case
```

Or, when a new implementation is justified:

```text
Not reused: upload() returns a completed file URL; this task needs resumable upload semantics.
Smallest change: add resumableUpload() without changing upload().
```

### 4. Keep the design proportional

Solve the current problem with the fewest concepts that make the behavior clear and safe. Do not add any of these for a hypothetical future caller:

- a strategy, plugin, registry, factory, adapter, or framework layer;
- a configuration file, option matrix, or extension point;
- a generalized interface with no current need;
- a parallel implementation of an existing project mechanism;
- an unrelated cleanup or refactor.

A new abstraction is reasonable when the current task has real repeated behavior, a clear ownership boundary, or a safety/error boundary that the abstraction makes explicit. Necessary validation, error handling, security checks, and separation of responsibilities still apply; simplicity does not mean hiding important failure modes.

### 5. Implement and verify proportionally

Make the smallest coherent change. Follow existing naming, structure, error handling, and state-management conventions. Validate the changed behavior using the narrowest useful existing check: a focused test, type check, linter, build check, or direct reproduction. Broaden verification only when the change crosses a shared boundary or affects multiple paths.

Do not require a test suite, a baseline, a particular language, or a new toolchain merely to use this workflow. If the project has no suitable check, perform a focused inspection or direct verification and report the limitation.

## Stop conditions

Stop looking when you have:

- found a direct implementation that can be reused;
- identified the existing owner that can be extended safely;
- established that relevant existing semantics do not fit; or
- confirmed that the relevant area is empty and a minimal implementation is needed.

Do not keep searching to make the decision feel more certain. Do not turn this skill into a mandatory design document for every small edit.

## Completion check

Before claiming the work is done, confirm:

- the change solves the requested behavior;
- existing behavior and callers remain compatible unless the task explicitly changes them;
- no duplicate project mechanism or speculative abstraction was introduced;
- the verification matches the actual impact of the change.
