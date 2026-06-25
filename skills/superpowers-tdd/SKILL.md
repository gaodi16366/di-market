---
name: superpowers-tdd
description: Drive development using Test-Driven Development (TDD) — write failing tests first, implement the minimum code to pass them, then refactor. Use this skill whenever the user wants to write tests before code, do red-green-refactor cycles, practice TDD, build features test-first, or says things like "let's TDD this", "write the tests first", "test-driven", or "make it fail first". Also trigger when the user is building a new function/module/class and wants quality guarantees from the start.
---

# Superpowers TDD

A skill for developing software through the Test-Driven Development cycle: **Red → Green → Refactor**.

The core loop is simple but powerful:
1. Write a failing test that describes the behavior you want
2. Write the minimum code to make it pass
3. Refactor to clean up without breaking tests
4. Repeat

Your job is to drive this process, keeping the user anchored to the cycle and preventing the common drift into "write code first, maybe add tests later."

---

## Starting a TDD Session

Before writing any code or tests, understand the target:

1. **What behavior are we building?** Get a plain-English description — not implementation details, but what the thing should *do*.
2. **What's the interface?** Function signature, class API, HTTP endpoint, CLI command — how will callers use this?
3. **What are the interesting cases?** Happy path, edge cases, error conditions. Don't enumerate them all upfront — just enough to start.
4. **What test framework and language?** If not obvious from the project, check for config files (jest.config.*, pytest.ini, Cargo.toml, etc.) before asking.

Once you have enough to write the first test, start. Don't over-plan.

---

## The Red–Green–Refactor Cycle

### Red: Write a Failing Test

Write one test. Just one. It should:
- Test a single specific behavior
- Have a name that reads like a sentence describing that behavior (e.g., `test_returns_empty_list_when_no_items_match`, `it should throw when given a negative index`)
- Fail for the *right reason* — not a syntax error or import failure, but because the behavior doesn't exist yet

Run the test and confirm it fails. Show the failure output. If it doesn't fail, the test is either wrong or the behavior already exists.

**Common mistake to avoid**: Don't write five tests before running any. One at a time.

### Green: Write Minimum Code to Pass

Implement only what's needed to make the failing test pass. This means:
- Hardcoding a return value is fine if it makes the test green — the next test will force you to generalize
- Don't add behavior that isn't tested yet
- Don't refactor yet

Run the test. Confirm it's green. If other tests broke, fix them before moving on.

### Refactor: Improve Without Breaking

Now clean up — both the implementation and the test code:
- Remove duplication
- Improve names
- Extract helpers
- Simplify logic

Run all tests after every change. If anything breaks, undo the last change and try again. Refactoring is only safe when tests are green.

Then start the next cycle.

---

## Picking the Next Test

After each green cycle, ask: what's the next most important behavior to add? Good heuristics:
- **Triangulate**: if you hardcoded a return value to go green, write a test that forces you to generalize
- **Edge before happy**: error cases and boundaries often expose design issues early
- **One step at a time**: resist the urge to write the "real" test before you've earned it

When stuck on what to test next, list 3 candidate behaviors and let the user pick.

---

## Test Quality

Tests should be:
- **Fast**: no network, no disk, no sleep unless the thing being tested requires it
- **Isolated**: each test sets up its own state, doesn't depend on test order
- **Clear**: a failing test name alone should tell you what broke
- **Minimal**: test one thing; use setup/fixtures to reduce repetition, not to hide it

For each test, follow Arrange–Act–Assert (or Given–When–Then):
```
// Arrange
const cart = new Cart();
cart.add(item);

// Act  
const total = cart.total();

// Assert
expect(total).toBe(9.99);
```

---

## Handling Existing Code

If the user wants to TDD a change to existing code:

1. First, characterize the current behavior with tests (if none exist). These are "golden master" tests — they describe what the code does, not what it *should* do.
2. Then write a failing test for the new behavior.
3. Make it pass.
4. Refactor.

Don't start changing code before the characterization tests are in place — they're your safety net.

---

## Communicating Progress

Keep a running summary of where you are in the cycle. After each step, say something brief like:
- "Test is red — `divide_by_zero_raises_ValueError` fails with `AttributeError: 'Calculator' object has no attribute 'divide'`. Now writing the implementation."
- "Green. All 4 tests pass. Want to refactor the `_validate` method before we add the next case?"

This keeps the user oriented and lets them jump in if you're heading in the wrong direction.

---

## When to Stop

A TDD session is done when:
- The feature works for all cases the user cares about
- The tests document the behavior clearly enough that a new developer could understand the contract
- The code is clean enough that you'd be happy to come back to it

Offer to run a final pass: "Want me to review the full test suite and flag anything that feels redundant or missing?"
