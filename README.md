# Contribution [2]: [Show the full prompt text in the composer input
#5370]

**Contribution Number:** 2 
**Student:** Kameti Kumbi
**Issue:** https://github.com/nexu-io/open-design/issues/5370#issuecomment-4928544248  
**Status:** [Phase I ] [Complete]

---

## Why I Chose This Issue

I chose this issue because it's a small, clearly-scoped frontend bug that fits my React/TypeScript skills and lets me practice reading a new codebase, fixing a real UI problem, and going through the PR review process with an actual maintainer. Also, I need to weigh a few valid fixes (wrap, scroll, or expand the input) rather than just copying one obvious solution, which is good practice for making design decisions on my own.

---

## Understanding the Issue

### Problem Description
The placeholder text in the composer input box (the hint/prompt text shown before real typing happens) gets cut off after just one line, even though there's more visible space in the box. This only affects the placeholder — text you actually type or paste works fine.

### Expected Behavior
The placeholder should use the space available in the composer box, wrapping across a few lines if needed, and only get cut off with "..." if it's still too long after using that space.

### Current Behavior
As soon as a long placeholder appears (for example, after clicking a suggestion card), it's forced onto a single line and cut off almost immediately with "...", even though the box has room below it for more lines.

### Affected Components
- `apps/web/src/styles/chat.css` — the `.composer-input-placeholder` CSS rule (the actual bug)
- `apps/web/src/components/composer/LexicalComposerInput.tsx` — renders the placeholder element (structure is fine, no changes needed here)

---

## Reproduction Process

### Environment Setup
Set up Node 24 through fnm (had to fix my PowerShell profile so fnm could actually switch versions — it wasn't picking up until I added `fnm env --use-on-cd` to my `$PROFILE`), then used Corepack to get the pinned pnpm version (10.33.2). Ran `pnpm install` and `pnpm tools-dev run web`, and signed in using a Gemini API key (BYOK) since I don't have a local coding agent CLI installed.

### Steps to Reproduce
1. Open a project (or design-system flow) and click one of the suggestion cards (e.g. "编辑风路演 PPT") to seed the composer with a long prompt.
2. Look at the composer box right after it's seeded, before typing anything.
3. Observed result: the text is cut off after one line with "...", even though there's empty space below it in the box.

### Reproduction Evidence
- **Commit showing reproduction:** _(not yet — will link once branch is created)_
- **Screenshots/logs:** Captured a screenshot of the composer showing the clipped placeholder text, plus a DevTools screenshot confirming the exact CSS on `.composer-input-placeholder` (`white-space: nowrap`, `text-overflow: ellipsis`, `overflow: hidden`, no `max-height`).
- **My findings:** Confirmed the bug is isolated to the placeholder overlay, not the real editable text (which already wraps/scrolls correctly). Also confirmed this isn't a duplicate of issue #2084, since that one's fix only touched the real textarea's auto-resize, not the placeholder.

---

## Solution Approach

### Analysis
The placeholder is a separate floating element (`position: absolute`) sitting on top of the real input, not inside the same scrollable container the real typed text uses. Its CSS forces `white-space: nowrap`, which stops it from wrapping at all, and it has no `max-height`, so simply removing that one property would let it grow past the bottom of the box instead of behaving nicely.

### Proposed Solution
Let the placeholder wrap across multiple lines like the real input does, but keep it inside the same visible height as the box. If the text is still too long after filling that space, cut it off gracefully with "..." at the end — no separate scrollbar needed.

### Implementation Plan

**Understand:** The placeholder truncates too aggressively (one line) instead of using the box's available space.

**Match:** The real editable text area (`.composer-input-editor`) already handles long content well, using `max-height: min(184px, 34vh)`. The placeholder should follow that same height limit. (Note: `PlaceholderCarousel.tsx`'s rotating example text uses a similar single-line style on purpose — that one stays as-is.)

**Plan:**
1. In `chat.css`, remove `white-space: nowrap` from `.composer-input-placeholder` so it can wrap.
2. Add a `max-height` to `.composer-input-placeholder` matching `.composer-input-editor`'s `min(184px, 34vh)`.
3. Keep `text-overflow: ellipsis` and `overflow: hidden` as the fallback for when text still doesn't fully fit.
4. Add a small test file (`apps/web/tests/styles/composer-input-placeholder.test.ts`) checking these two rules, following the existing CSS-contract test pattern used elsewhere in the repo.

**Implement:** https://github.com/Kameti77/open-design/tree/fix-issues-5370

**Review:** Self-check before opening the PR — single-quote style, English-only comments, one concern per PR (CSS-only, no unrelated changes), fill out the PR template fully, and clearly note that the home-page carousel text was reviewed and intentionally left unchanged.

**Evaluate:** Run the new test (`pnpm vitest run -c vitest.config.ts tests/styles/composer-input-placeholder.test.ts`), plus manually recheck the fix in the browser — confirm the placeholder wraps across a few lines, stays inside the box without overlapping other UI, and only shows "..." if it's genuinely still too long.
---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
