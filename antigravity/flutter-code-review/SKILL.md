---
name: flutter-code-review
description: Perform enterprise-grade code reviews on Flutter and Dart codebases. Detects architectural drift, state management leaks, performance bottlenecks, and concurrency issues.
origin: ECC
---

# Flutter & Dart Code Review Skill

This skill guides the automated or semi-automated review of Flutter applications and Dart packages. It evaluates code modifications to ensure they align with industry standards, maintainable architecture patterns, and the specific conventions of the target codebase.

## When to Activate

- Reviewing pull requests or git diffs containing `.dart` files.
- Evaluating architectural changes or refactoring tasks in a Flutter project.
- Investigating memory leaks, rendering lag, or state management issues.
- Performing a security or accessibility sweep before a production release.

## Core Objectives

- **Identify bugs & regressions**: Pinpoint logic errors, type safety violations, and unhandled edge cases.
- **Verify feature correctness**: Ensure implementation meets the stated functional requirements.
- **Enforce best practices**: Verify idiomatic Dart and Flutter usage.
- **Ensure resource safety**: Detect undisposed controllers, uncancelled subscriptions, and memory leaks.
- **Optimize performance**: Prevent redundant widget rebuilds, heavy operations in build blocks, and layout thrashing.
- **Validate architectural alignment**: Confirm clean separation between presentation, domain, and data layers.

---

## Review Categories & Checklists

### 1. Functionality & Logic
- [ ] **Type safety**: Ensure no implicit `dynamic` types are introduced. Run with strict-casts enabled if configured.
- [ ] **Async boundaries**: Verify that `Future` operations are properly awaited and that error paths are handled.
- [ ] **Nullable variables**: Check that null-safety is handled defensively with safe navigation (`?.`) or pattern matching, avoiding force-unwraps (`!`).

### 2. Flutter Widget Best Practices
- [ ] **Context across async gaps**: Ensure `mounted` is checked on `BuildContext` before using it after any `await` statement.
- [ ] **Widget decomposition**: Check that large `build` methods are split into independent widget classes rather than helper methods to leverage compiler optimizations and rebuild boundaries.
- [ ] **Const propagation**: Ensure `const` constructors are used wherever compile-time evaluation is possible to minimize widget instantiation overhead.

### 3. State Management
- [ ] **Rebuild scopes**: Verify that reactive builders (e.g., `BlocBuilder`, `Consumer`, `Observer`) are wrapped around the smallest necessary widget subtrees.
- [ ] **Immutability**: For immutable state patterns (like BLoC or Riverpod), verify that states are updated using `copyWith` and that data classes override `==` and `hashCode` (e.g., via `Equatable` or `freezed`).
- [ ] **Disposal**: Ensure all state managers, controllers (`TextEditingController`, `AnimationController`), and streams are closed or disposed of when their lifetime ends.

### 4. Performance
- [ ] **Build block discipline**: Verify that no synchronous I/O, heavy parsing, or network calls occur within a `build()` method.
- [ ] **Lazy lists**: Ensure list views displaying dynamic or large datasets use `ListView.builder` or `GridView.builder` rather than standard layout constructors.
- [ ] **Image sizing**: Check that network or asset images utilize `cacheWidth` and `cacheHeight` constraints when rendered inside small containers to optimize memory usage.

### 5. Code Quality & Idiomatic Dart
- [ ] **Logging**: Ensure `print()` is not used for debugging in production paths. Prefer `dart:developer` `log()` or the project's logger.
- [ ] **Pattern matching**: Use modern Dart 3 switch expressions and pattern matching where applicable instead of verbose type checks and cascading `if-else` blocks.
- [ ] **Finality**: Ensure variables are declared `final` when reassignment is unnecessary.

### 6. Error Handling
- [ ] **Specific catching**: Verify that `catch (e)` is avoided in favor of typed catching (e.g., `on SocketException catch (e)`).
- [ ] **Graceful degradation**: Ensure failures at the network or database level map to clean, localized user notifications rather than surfacing raw stack traces.

---

## How to Execute the Review

When this skill is activated:
1. Examine the modified files (`git diff` or specific code blocks).
2. Scan the code systematically using the checklists above.
3. Formulate objective, constructive findings based on observable evidence in the code.
4. Structure your response using the exact output format defined below.

---

## Output Format

Review findings must be structured using the following sections:

```markdown
## Summary

[A brief, high-level overview of the changes reviewed, detailing which modules or components were modified.]

## Findings

### [Severity: Critical | Major | Minor] Title of Finding

**Issue:** [Clear description of the observed issue, referencing specific files and line numbers.]

**Impact:** [Explanation of the risks associated with this pattern, such as memory leaks, thread blockage, or runtime crashes.]

**Recommendation:** [Actionable steps to resolve the issue.]

**Suggested Code:**
```dart
// Example of the corrected implementation
```

## Positive Observations

- [List well-implemented patterns, effective use of Dart idioms, or strong test coverage observed in the changes.]

## Final Assessment

- [Approve | Approve with minor comments | Request changes]
```

All review comments must maintain a highly professional, objective, and constructive tone, focusing entirely on code quality and architectural health.
