# Diff-narration anti-patterns

Real sentences produced during a single PR-writing session, with rewrites. Each ❌ is a sentence I actually wrote before the user pushed back; each ✅ is what landed in the merged PR body (see [good-pr-description-bugfix.md](good-pr-description-bugfix.md)).

Use this as a self-check before submitting a PR body. If any of your sentences match an ❌ pattern, the fix is in the ✅ column.

## The test, restated

A sentence is diff narration if **any** of these are true:

1. It names the **before** and **after** API / method / call chain.
2. It could be rewritten as "I changed X to Y" without losing meaning.
3. It would be accurate and useful even to a reader who had never read the issue.
4. It describes a function's new signature, return type, or control flow.
5. It walks through what a method chain does step by step.

---

## Rewrite pairs

### Pair 1: return-type change

❌ **"Replaced `.expect()` with `?` and changed the return type to `UResult<Vec<String>>`."**

This names the before API (`.expect()`) and after API (`?`), and describes the new return type. A reviewer can see both in the diff.

✅ **"`du -X nonexistent-file` panicked instead of printing a clean error."**

States the user-visible symptom. A reviewer can reproduce it, verify the fix with the same command, and understand why the change matters without looking at the diff.

---

### Pair 2: method-chain refactor

❌ **"Switched `.flat_map(file_as_vec)` to `.map(file_as_vec).collect::<UResult<_>>()?.into_iter().flatten()`."**

Names both call chains. Fails test #1 hard — this is the most classic "I changed X to Y" form.

✅ **"`flat_map` over a `Result`-returning function silently drops `Err` (zero items), so I/O errors would have been hidden even after removing the panic."**

Names the **bug** (silent error swallowing) and the **root cause** (`Result`'s `IntoIterator` impl). The diff shows the replacement; this sentence explains *why* the old code was wrong. This is the carve-out in Core Rule 2: non-obvious root cause worth documenting.

---

### Pair 3: feature-flag wiring

❌ **"Added a new `GrowthBook` evaluation before the price calculation, gated on the `new-pricing-v2` flag."**

Describes new control flow. Reviewers will see the `if gb.isOn(...)` in the diff and the new import.

✅ **"Price calculation was rolled out to 10% of users via the `new-pricing-v2` flag (ticket ABC-1234). Rollback is instant via the flag."**

States what users will experience (gradual rollout) and how to reverse it (flag flip). Operationally useful context that isn't in the diff.

---

### Pair 4: adding a config option

❌ **"The `ServerConfig` struct now has an optional `timeout_ms: Option<u64>` field, defaulting to 30000."**

Describes the new struct field. The diff shows the field and default.

✅ **"Support for per-request timeouts. Closes #4421, where a slow downstream was blocking the whole pool."**

States the capability being added and the motivating failure. A reviewer now knows what acceptance looks like.

---

### Pair 5: dependency bump

❌ **"Bumped `serde_json` from 1.0.108 to 1.0.113."**

Literally a restatement of the lockfile diff.

✅ **"Picks up the fix for [serde-rs/json#1108](https://github.com/serde-rs/json/issues/1108) (panic on deeply-nested JSON), which we hit in prod on 2026-04-12."**

States *why* the bump — specific upstream fix + specific incident. If there's no "why," a plain `chore: bump serde_json to 1.0.113` commit doesn't need a PR description at all.

---

## Why this keeps happening

The trap is that diff narration **feels informative**. It explains the code, and explanation feels like a reviewer's friend. But reviewers are already going to read the diff — they don't need a prose paraphrase of it. What they can't get from the diff is: what symptom was the user seeing? what decision was the author making? what acceptance criterion was in mind?

Writing PR prose means consciously *not* writing the natural paragraph about what you did. That's hard — it's why the red-flag tests in Core Rule 2 are worded as deletion prompts rather than style advice. A rule that says "delete this sentence if X" is harder to rationalise around than "be concise."
