# Investigate

Use this playbook when something is wrong, unclear, or surprising — **before**
editing production paths.

## Goal

Establish a shared, checkable understanding of the current behavior and the
evidence that supports it.

## Steps

1. **Restate the question** in one or two sentences. What would “answered”
   look like?
2. **Collect primary evidence**: logs, failing tests, stack traces, API
   responses, config diffs. Prefer artifacts over recollection.
3. **Name the boundary**: which service, package, or file owns the behavior?
4. **Form a falsifiable hypothesis**. Write what you expect to observe if it
   is true, and what would disprove it.
5. **Run the smallest check** that can confirm or reject the hypothesis
   (one command, one request, one test).
6. **Record findings** in the PR or issue: what you saw, what you ruled out,
   and what remains unknown.

## Done when

- You can point to concrete evidence for the leading theory, or
- You have a short list of remaining unknowns with a next probe for each.

## Do not

- Patch while still guessing.
- Expand scope into unrelated cleanups.
- Treat chat speculation as evidence.
