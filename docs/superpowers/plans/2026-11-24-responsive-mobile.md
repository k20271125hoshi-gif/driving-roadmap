# Responsive Mobile UI Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Preserve the existing desktop presentation while giving screens at 700px and below a dedicated, touch-friendly single-column UI.

**Architecture:** Keep the existing HTML, JavaScript state, and desktop CSS unchanged. Replace only the current mobile media-query rules and add a narrow-phone refinement, with a dependency-free Node regression test that inspects the responsive stylesheet contract.

**Tech Stack:** HTML5, CSS3 media queries, vanilla JavaScript, Node.js built-in test runner

**Spec:** `docs/superpowers/specs/2026-11-24-responsive-mobile-design.md`

## Global Constraints

- Screens at 701px and above must retain the current desktop rules.
- Screens at 700px and below use the dedicated mobile layout.
- Existing progress, localStorage, history, import/export, and reset behavior must not change.
- The page must not introduce horizontal document overflow at approximately 320px width.
- Existing focus-visible and reduced-motion behavior must remain intact.

## Review Focus

- At 700px exactly, the mobile breakpoint must apply and stack the hero content.
- At 701px, none of the mobile-only overrides may apply.
- At 320px, long labels and milestone metadata must wrap without forcing document overflow.
- Horizontal progress-card and stage-navigation scrolling must stay inside their own regions.
- Tap targets for stamps and mobile action controls must be at least 44px high.

---

### Task 1: Responsive stylesheet contract

**Files:**
- Create: `tests/responsive-css.test.js`
- Modify: `style.css:1-2`

**Interfaces:**
- Consumes: `style.css` as UTF-8 text.
- Produces: a dependency-free regression suite run with `node --test tests/responsive-css.test.js`.

- [ ] **Step 1: Write failing tests for the mobile layout contract**

Add tests named `desktop rules remain outside the mobile media query`, `mobile hero and next action use a single-column layout`, `mobile progress cards scroll within their region`, `mobile controls meet the 44px touch target`, and `narrow phones avoid fixed-width overflow`. Assert the exact `700px` breakpoint, mobile-only layout declarations, and the narrow-phone refinement required by the design.

- [ ] **Step 2: Run the responsive test and verify RED**

Run: `node --test tests/responsive-css.test.js`

Expected: FAIL because the current 700px rules keep the hero and stat cards compressed horizontally and do not contain the new mobile contract.

- [ ] **Step 3: Implement the mobile-only CSS**

In `style.css`, leave all rules before `@media(max-width:700px)` intact. Replace the existing mobile overrides so that the hero stacks, the ring remains prominent, stats become horizontally scrollable cards, next action and settings controls become full-width, roadmap content stays one-column, and text can wrap. Add a narrow-phone breakpoint for 420px and below to reduce spacing and prevent overflow while retaining 44px targets.

- [ ] **Step 4: Run the responsive test and verify GREEN**

Run: `node --test tests/responsive-css.test.js`

Expected: all responsive CSS tests PASS with no warnings.

### Task 2: Browser behavior and visual verification

**Files:**
- Verify: `index.html`
- Verify: `app.js`
- Verify: `style.css`

**Interfaces:**
- Consumes: the static site served over local HTTP.
- Produces: verified layouts at 320px, 390px, 700px, and desktop width with unchanged application behavior.

- [ ] **Step 1: Start a local static server and open the site in a browser**

Run: `python3 -m http.server 8000 --bind 127.0.0.1`

Expected: `index.html`, `style.css`, and `app.js` load without console or HTTP errors.

- [ ] **Step 2: Inspect mobile and desktop layouts**

At 320px, 390px, and 700px, verify stacked hero content, contained horizontal scrollers, readable roadmap cards, 44px controls, and no page-level horizontal overflow. At desktop width, compare the page structure with the original desktop layout and confirm it remains unchanged.

- [ ] **Step 3: Exercise core interactions at mobile width**

Toggle a lesson stamp twice, change the target date, open settings, and verify the data-management controls remain reachable and readable. Confirm no JavaScript source changes were necessary.

- [ ] **Step 4: Run final regression verification**

Run: `node --test tests/responsive-css.test.js`

Expected: all tests PASS. Review `git diff --check` and the final diff for whitespace errors and unintended desktop or JavaScript changes.
