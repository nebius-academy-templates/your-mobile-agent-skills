# Practice: Skills

⌨️ In this practice task, you'll create on-demand convention skills and an explicit workflow skill for a mobile PR review agent.

## 👩‍💻 Workflow at a glance

1. Set up your environment
2. Study the two examples
3. Write the Swift conventions skill
4. Write the Kotlin conventions skill
5. Write the workflow skill
6. Test selective loading
7. Test the workflow skill
8. Capture your test session
9. Submit

## Two worked examples

Here are two skills from the Release Notes Generator you saw in the walkthrough — a quick recap before you write your own.

### **A knowledge skill**

```markdown
---
name: "pr-label-format"
description: "PR label conventions for the development team — how to map PR labels and commit prefixes to user-facing release note categories. Load when processing PR lists for release notes."
---

## Rules 
 Map `feat` or `type: feature` labels → Features section.
 Map `fix` or `type: fix` labels → Fixes section.
 Map `perf` or `type: improvement` labels → Improvements section.
 Exclude PRs labeled `chore`, `internal`, or `refactor`.
## Template
PR label: `feat` → Category: Features
PR label: `chore` → Action: Exclude from release notes
```

### **A workflow skill**

```markdown
---
name: "draft-release-notes"
description: "Draft release notes for the app from merged PRs. Triggered explicitly with /draft-release-notes <version>."
disable-model-invocation: true
---

## Steps
1. Fetch all merged PRs from the repository's main branch since the last release using the GitHub MCP server.
2. Apply the `pr-label-format` skill to categorize each PR.
3. Group PRs into Features, Fixes, and Improvements.
4. Draft each entry as a single user-facing sentence. Do not invent details — use only what's in the PR title or description.
5. Present the complete draft to the user with the version number `$ARGUMENTS`. Ask: "Review complete. Approve to publish, or reply with edits."
6. Only publish to GitHub releases after receiving explicit approval.
```

Two things to notice in the frontmatter:

- The description says it's triggered explicitly with `/draft-release-notes`.
- `disable-model-invocation: true` blocks auto-invocation.

## 1. Set up your environment

Keep working in `building-your-mobile-agent` repo. 

## 2. Write the Swift conventions skill

Create `skills/swift-conventions/SKILL.md`. Follow the knowledge-skill example above.

- **Description**: The skill must name `.swift` as a trigger.
- **Body**: At least three rules covering naming, concurrency, and UIKit/SwiftUI usage. Tag severity for each.
- **Template**: One compliant and one non-compliant Swift snippet.

## 3. Write the Kotlin conventions skill

Create `skills/kotlin-conventions/SKILL.md`.

- **Description**: Must name `.kt` files as the load trigger.
- **Body**: At least three rules covering naming, coroutines, and Jetpack/lifecycle. Tag severity for each.
- **Template**: One compliant and one non-compliant Kotlin snippet.

## 4. Write the workflow skill

Create `skills/review-pr/SKILL.md` that runs the full review on a single command.

- **Frontmatter**: `name: review-pr` and a description naming explicit invocation. The skill should make the agent follow the steps exactly as written.
- **Steps**: Fetch the PR via GitHub MCP, apply `swift-conventions` to Swift files, apply `kotlin-conventions` to Kotlin files, flag security issues, compile findings into your output format, present them, and ask for confirmation before posting. Post only on `yes` or `post`.

## 5. Test selective loading

Test that each skill is invoked when intended.

PR #2 changes a Swift file. Run:

> Review the Swift files in PR #2 from https://github.com/nebius-academy-templates/mobile-pr-review-practice for style issues.
> 

**Verify:**

- [ ]  `swift-conventions` loads
- [ ]  `kotlin-conventions` does not load

PR #4 changes a Kotlin file. Run:

> Review the Kotlin files in PR #4 from https://github.com/nebius-academy-templates/mobile-pr-review-practice for style issues.
> 

**Verify:**

- [ ]  `kotlin-conventions` loads
- [ ]  `swift-conventions` does not load

If the wrong skill loads, your description is likely the problem. Tighten it to name the file extension explicitly, then test again.

## 6. Test the workflow skill

Run `/review-pr 1`.

**Verify:**

- [ ]  The agent works through the steps in order
- [ ]  It pauses and asks for confirmation before posting
- [ ]  It does not post when you answer `no`

## 7. Capture your test session

Copy the full conversation or have your tool capture it. Save it as `test-session-3.md`.

## 8. Check your work

- [ ]  `skills/swift-conventions/SKILL.md`: description names `.swift` as the trigger, body has at least three rules, and one template
- [ ]  `skills/kotlin-conventions/SKILL.md`: description names `.kt` as the trigger, body has at least three rules, and one template
- [ ]  `skills/review-pr/SKILL.md`: follows the steps in order
- [ ]  `test-session-3.md` shows selective loading working and the workflow run pausing before posting

## 9. Submit your task

1. Commit your changes.
2. Push to GitHub.
3. Return to the lesson and click "Submit."
