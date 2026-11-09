# Issue-search workflow: worked transcript

Concrete walk-through of step 4.55 in SKILL.md — finding the issue a PR fixes *before* writing the description. Based on a real session (the uutils/coreutils#11996 fix).

## Setup

The developer had just finished a local fix for a panic in `du -X`. No issue number had been mentioned in the conversation. Branch was `main` (not a topic branch), no prior commits referenced an issue. This is the common case where we have to actively search.

## Step 1 — Conversation history

Did the user paste an issue URL or ticket ID? No. They described the symptom (`du -X` panics on missing file) but never cited an issue. Skip to step 2.

## Step 2 — Branch name

```bash
$ git rev-parse --abbrev-ref HEAD
main
```

No ticket ID in the branch. Skip to step 3.

## Step 3 — Commit messages

```bash
$ git log main..HEAD --format=%B
(no commits yet — changes are uncommitted)
```

Nothing to search. Skip to step 4.

## Step 4 — GitHub issue search

Derive keywords from the diff. The panic trace in the bug report mentions `du`, `--exclude-from`, and `panic`. Try a few variations:

```bash
$ gh issue list --repo uutils/coreutils \
    --search "du exclude-from panic" --limit 10 --state all \
    --json number,title,state,url
[]

$ gh issue list --repo uutils/coreutils \
    --search "du file_as_vec" --limit 10 --state all \
    --json number,title,state,url
[{"number":11924,"state":"OPEN","title":"du -X with a nonexistent file panics",...}]
```

Second search hits. The bug report's title ("du -X with a nonexistent file panics") matches the symptom exactly. But before citing it, verify.

## Step 4.5 — Verify before citing

The `create-pr` skill's "Verifying the match" rule says: if the issue mentions a specific `file:line`, check the diff touches it.

```bash
$ gh issue view 11924 --repo uutils/coreutils --json body
"thread 'main' panicked at src/uu/du/src/du.rs:759:37:
 no such file: Os { code: 2, kind: NotFound, ... }"
```

Line 759, column 37. Check the pre-fix code:

```rust
let file = File::open(filename).expect("no such file");
//                               ^^^^^^^^^^^^^^^^^^^^^^^^ col 37 ≈ here
```

Match confirmed. The `.expect()` at that position is exactly what the PR replaces with `?`. Safe to cite.

## Step 5 — Look for related issues

Good practice: when one issue is a match, check for duplicates or sibling reports. They often hide behind different keywords.

```bash
$ gh issue list --repo uutils/coreutils \
    --search "du panic expect" --limit 10 --state all \
    --json number,title,state,url
[{"number":11924,"state":"OPEN",...},
 {"number":11925,"state":"OPEN","title":"du -X /proc/self/mem panics",...}]
```

A second, related issue. View it:

```bash
$ gh issue view 11925 --repo uutils/coreutils --json body
"different reason with the linked issue."
```

The reporter of #11925 explicitly says it's a different panic path — line 763:20 instead of 759:37. Check: 763:20 is the `.expect()` inside the per-line read (`.map(|l| l.expect("Could not parse line"))`), which the same PR also replaces with `?`. Match confirmed for the second issue too.

## Step 6 — Surface to user, wait for confirmation

Present the candidates with specifics so the user can validate:

> I found two matching issues:
>
> - **#11924** ("du -X with a nonexistent file panics") — panics at `du.rs:759:37`, which is the `File::open(...).expect("no such file")` this PR replaces with `?`.
> - **#11925** ("du -X /proc/self/mem panics") — panics at `du.rs:763:20`, which is the `l.expect("Could not parse line")` inside `.lines().map(...)` this PR also replaces with `?`.
>
> Reference both in the PR? (y/n)

User confirms. Both go into the PR body as `Fixes #11924` / `Fixes #11925`.

## Step 7 — Commit trailer

Mirror the issue refs in the commit message body so `git blame` readers can find them:

```
du: propagate errors from --exclude-from instead of panicking

(...body...)

Closes #11924
Closes #11925
```

## What to notice

- **We searched four places before finding anything.** Conversation, branch, commits — all empty. Don't give up after one empty search; issues live where they live.
- **The second search was more productive than the first.** Initial keywords (`du exclude-from panic`) missed the report because the reporter didn't use the word "exclude-from". Reformulating with function names (`file_as_vec`) and general symptom (`panic expect`) worked.
- **Verification is not optional.** Both issues pointed to specific `file:line` locations that could be cross-referenced against the diff. If they hadn't — or if they had but the lines didn't match — the right move is to ask the user, not guess.
- **One PR closing two issues is fine** when they share a root cause. Both get `Fixes #NNN` at the top of the body; both auto-close on merge.

## Anti-example

Without the search, here's the PR body that would have shipped:

```markdown
`du -X <file>` panicked when reading the `--exclude-from` file.
Now it exits with a clean error.
```

Technically true, but: (1) no issue link, so maintainers have to find #11924 and #11925 manually to close them; (2) the second issue (#11925) would probably have stayed open after merge because nobody knew it was the same root cause; (3) release notes don't have structured references; (4) a future engineer doing `git blame` on `file_as_vec` sees a commit with no ticket context. The 30 seconds of searching saved hours downstream.
