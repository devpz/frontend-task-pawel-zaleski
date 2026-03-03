# Submission: Paweł Załęski

## Time Spent

Total time: ~4 hours

## Ticket Triage

### Tickets I Addressed

List the ticket numbers you worked on, in the order you addressed them:

- **CFG-148** : Fixed a crash caused by messing with state directly.
- **CFG-142** : Fixed pricing glitch when older requests overwrote newer ones.
- **CFG-147** : Fixed the share link so special characters don’t break it.
- **CFG-152** : Improved keyboard support for interactive elements.
- **CFG-156** : Switched from index keys to proper IDs.
- **CFG-143** : Added proper cleanup for the resize event to avoid potential performance issues.

### Tickets I Deprioritized

List tickets you intentionally skipped and why:

| Ticket  | Reason                                                                                    |
| ------- | ----------------------------------------------------------------------------------------- |
| CFG-153 | Compare Configurations is a pretty big feature and would take more than 4 hours.          |
| CFG-155 | Dark mode is just a UI improvement, not something critical for the demo.                  |
| CFG-144 | Removing this feature doesn’t affect app stability.                                       |
| CFG-145 | Nice enhancement but not essential for the demo to work correctly.                        |
| CFG-154 | Discount logic would need a deeper look into the pricing engine, so it’s not a quick fix. |
| CFG-157 | Confirmation dialog improvement, it's good for UX but it's not blocking the demo.         |
| CFG-150 | Minor CSS alignment issue, it has low impact.                                             |
| CFG-151 | Just wording in an error message, it's UX polish.                                         |

### Tickets That Need Clarification

List any tickets where you couldn't proceed due to ambiguity:

| Ticket  | Question                                                                                   |
| ------- | ------------------------------------------------------------------------------------------ |
| CFG-154 | Should we calculate quantity discounts on the client, or double-check them on the backend? |
| CFG-153 | Should the comparison be saved between sessions, or is it just temporary?”                 |

---

## Technical Write-Up

### Critical Issues Found

#### Issue 1: Runtime Crash Due to State Mutation

**Ticket(s):** CFG-148

**What was the bug?**

`selectedAddOns` was being modified directly with `splice()`. That can cause weird behavior — including crashes and stale state.

**How did you find it?**

While checking the add-on dependency logic, I noticed the app was crashing when deselecting dependent options. Tracing it back led me to the direct state mutation.

**How did you fix it?**

I replaced the mutation with an immutable update:

setSelectedAddOns((prev) => prev.filter((id) => id!==addOn.id));

**Why this approach?**

This approach keeps state updates predictable and follows standard React patterns, so re-renders behave correctly.

---

#### Issue 2: Async Race Condition in Price Calculation

**Ticket(s):** CFG-142

**What was the bug?**

When someone changed options really quickly, older async responses sometimes came back later and overwrote the newer price — so totals could be wrong.

**How did you find it?**

I changed configuration and noticed that outdated responses were replacing the correct values.

**How did you fix it?**

I added a simple request tracking mechanism and made sure only the newest request is allowed to update the state:

if (requestTime===latestRequestRef.current)

**Why this approach?**

It blocks stale async responses from messing with the state, without adding extra complexity like abort controllers.

---

### Other Changes Made

Brief description of any other modifications:

- I made the Base64 link URL-safe using `encodeURIComponent`.
- I added ARIA roles and keyboard handlers for accessibility improvements.
- I stopped using array indexes as React keys and switched to stable IDs.
- I added cleanup for the resize event in `useEffect`.

---

## Code Quality Notes

### Things I Noticed But Didn't Fix

List any issues you noticed but intentionally left:

| Issue                                           | Why I Left It                                                   |
| ----------------------------------------------- | --------------------------------------------------------------- |
| Global pricing logic tightly coupled to UI      | It would require architectural refactor beyond time constraint. |
| Some error handling lacks user-friendly mapping | It needs product alignment on UX messaging.                     |
| Mixed formatting conventions                    | This is only cosmetic improvement.                              |

If you had more time, what would you improve?

- I would introduce request cancellation.
- I would extract pricing logic into domain-layer abstraction.
- I would add unit tests for async hooks.
- I would implement memoization to reduce recalculations.
- I would implement error boundary for crash protection.

---

## Questions for the Team

Questions you would ask in a real scenario:

- Is price calculation intended to be fully client-side or validated server-side?
- Do you prefer to optimize performance first over feature delivery in demo scenarios?
- What accessibility level do you expect for this project?

---

## Assumptions Made

List any assumptions you made to proceed:

1. Stability of demo is the highest priority.
2. Backend is the source of information for prices.
3. Partial improvement of accessibility is expected.

---

## Self-Assessment

### What went well?

- I quickly identified stability issues.
- I prioritized critical problems.
- I divided changes into small commits.

### What was challenging?

- To balance the async with time constraints.
- To choose which tickets should be deprioritized.

### What would you do differently with more time?

- I would add tests for async prices.
- I would refactor pricing for more clear separation of concerns.

---

## Additional Notes

Anything else you want us to know:

I focused on runtime stability and demo reliability taking into consideration time limit of 4 hours.

I prioritized predictable behavior over the feature development.
