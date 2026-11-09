# Good PR description: user-facing bug fix

Canonical example of the "failure modes" template from SKILL.md Core Rule 2. Taken verbatim from [uutils/coreutils#11996](https://github.com/uutils/coreutils/pull/11996), which was merged.

## Why this works

- **Issue references are the first two lines.** `Fixes #NNNN` auto-closes on merge and shows up as a card in GitHub's UI. No ceremony around them.
- **One-sentence lead describes the class of bug**, not the mechanism: *"panicked on two failure modes when reading the `--exclude-from` file"*. A reviewer now has the category before they see the specifics.
- **Numbered failure modes.** Each is reproducible in one shell line, each shows the actual panic output the user experienced, and each names which issue it closes. A reviewer can copy the command and verify before/after without reading any code.
- **Comparison to the spec.** *"GNU `du` prints a clean error and exits non-zero."* tells the reviewer what "fixed" means — the acceptance criterion is embedded in the description.
- **One-sentence new-behavior summary.** `du: No such file or directory`, `du: Input/output error`, `du: Permission denied` — concrete, verifiable strings, not "now handles errors gracefully."
- **One trailing paragraph on the subtle root cause.** The `flat_map` + `Result` gotcha would not be obvious from the diff — so it's documented. This is the carve-out Core Rule 2 names: non-obvious root cause is worth keeping.

## The PR body verbatim

```markdown
Fixes #11924
Fixes #11925

`du -X <file>` panicked on two failure modes when reading the
`--exclude-from` file:

1. **Missing file** (#11924):
   `$ du -X nonexistent-file`
   `thread 'main' panicked at src/uu/du/src/du.rs:759:37:
   no such file: Os { code: 2, kind: NotFound, ... }
   Aborted (core dumped)`
GNU `du` prints a clean error and exits non-zero.

2. **Per-line I/O error** (#11925), e.g. a pseudo-file that fails on read:
   `$ du -X /proc/self/mem`
   `thread 'main' panicked at src/uu/du/src/du.rs:763:20:
   Could not parse line: Os { code: 5, kind: Uncategorized, ... }`

Both now exit with code 1 and print to stderr (`du: No such file or directory`, `du: Input/output error`, `du: Permission denied — message follows the OS error kind`).

The call site was also switched from `.flat_map(file_as_vec)` to a
`.map(...).collect::<UResult<_>>()?` chain, because `flat_map` over a
`Result`-returning function silently drops `Err` (zero items) — hiding
any I/O failure instead of reporting it.
```

## What this description deliberately *doesn't* contain

- No "Files Changed" section. GitHub renders that automatically.
- No "Test plan" section. uutils CI runs the tests.
- No "Risks" section. The user didn't raise any specific concern.
- No narration of the `file_as_vec` signature change or the `?` propagation. The diff shows it.
- No explanation of *why* `.map().collect::<Result<_, _>>()?` works — only that it does. Type-system trivia belongs in code review, not the PR body.

The trailing flat_map paragraph *does* describe the call site, but notice what it names: the **bug** (silent drop on Err) and the **root cause** (Result's IntoIterator impl). It doesn't say "I replaced `.flat_map` with `.map(...).collect::<UResult<_>>()?`". The diff shows the replacement; the prose explains why the old code was wrong.
