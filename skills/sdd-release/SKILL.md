---
name: sdd-release
description: >
  Final release and handoff for an approved SDD change set. Use after review
  and optional simplification to freeze scope, package release notes, update
  public docs, and prepare a safe publish or handoff. 
---

# sdd-release

Close out a reviewed and approved change set as a release-ready handoff without adding scope. Treat release as a two-part deliverable: first a gate checklist, then a concise user-facing summary.

Announce at start (match user-requested language): "I am using **sdd-release** to prepare the final release and handoff only."

## When to Use

- `sdd-review` has passed, and `sdd-simplify` is either done or explicitly skipped
- The user wants a versioned handoff, release notes, or publish-ready cleanup
- You need to record what shipped, what changed, and what follow-up remains

## Core Rules

- Do not add new scope during release
- Do not reopen feature design or implementation work
- Treat release as a freeze-and-package step, not a re-plan
- Preserve rollback and follow-up information
- Prefer explicit, user-facing release notes over vague summaries

## Process

### 1. Validate Release Readiness

- Confirm review status is acceptable
- Confirm any intended simplification is complete
- Confirm the release scope is frozen
- If critical issues remain open, stop and report them

### 2. Freeze the Release Candidate

- Identify the exact feature set, commits, or tasks being shipped
- Record the version, date, and scope boundary
- Note anything intentionally excluded from this release

### 3. Prepare Release Artifacts

- Update release notes or changelog entries
- Update install or upgrade guidance if the release changes how the skill pack is used
- Record public-facing behavior changes, fixes, and noteworthy constraints
- If the repo uses ADRs or decision docs, link to them instead of re-explaining the decision

### 4. Publish or Hand Off

- If the workflow includes tagging or publishing, do that with the frozen candidate
- Otherwise, package the release summary so a human can publish it safely
- Include rollback notes or known risks if the release is not fully automatic

### 5. Close the Loop

- Confirm the final artifact set
- Confirm nothing outside the release scope changed
- Leave the next maintenance step explicit: patch follow-up, doc update, or next iteration
- Restate the frozen release scope before claiming the release is complete
- Make the final handoff explicit: `merge`, `handoff`, `keep`, or `stop`
- Do not say the release is complete until the release checks in this session have been run and the output supports that claim

## Output Template

```markdown
### Verification Checklist

- [ ] Release scope is frozen
- [ ] Review gate has passed
- [ ] Simplification gate is either complete or intentionally skipped
- [ ] Release notes or changelog are updated
- [ ] Upgrade / install docs are current if user-facing behavior changed
- [ ] Rollback or follow-up notes are captured when needed

### User-Facing Summary

- **Version / date:** …
- **What shipped:** …
- **Scope / exclusions:** …
- **Rollback / follow-up:** … (if needed)
```

## Present Results to User

- Lead with what shipped and the version/date.
- State what was intentionally excluded.
- Include rollback or follow-up notes if they matter.
- Keep the release summary user-facing and concise.

## Verification

- [ ] Release scope is frozen
- [ ] Review gate has passed
- [ ] Simplification gate is either complete or intentionally skipped
- [ ] Release notes or changelog are updated
- [ ] Upgrade / install docs are current if user-facing behavior changed
- [ ] Rollback or follow-up notes are captured when needed
- [ ] Any completion claim is backed by fresh release verification evidence from this session

## Red Flags

- Shipping with unresolved review issues
- Adding polish work that was not in the frozen scope
- Rewriting the implementation during release
- Omitting release notes for user-facing changes

## Prompt Next Step

Say the next step outright (question optional): **`sdd-init`** for the next iteration, or stop when the release is complete.
