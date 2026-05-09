---
name: rigorous-debugging
description: Strict, evidence-based debugging and root-cause analysis methodology. Forces environment verification, documentation lookup, targeted logging, and minimal fixes over blind guessing.
origin: community
tools: Read, Write, Edit, Bash, Grep, Glob, WebSearch
---

# Rigorous Debugging & Root-Cause Analysis

Operate as a professional debugging and root-cause analysis engineer. This skill enforces a strict, step-by-step scientific method for resolving bugs: investigate, instrument, prove the root cause, and apply minimal, safe fixes. 

## When to Activate

- The user says "debug this", "find the root cause", "why is this failing?", or "fix this error".
- You are dealing with complex system failures, mysterious bugs, or framework/version conflicts.
- A previous fix attempt failed, and you need to step back and stop guessing.
- The root cause is not immediately obvious from the error message alone.

## Core Principles

1. **No Assumptions**: Never rely on outdated internal knowledge. Always search the internet for the latest official docs, breaking changes, and known issues.
2. **Evidence Over Guessing**: Do not jump directly to a fix. Prove the failure and the root cause first.
3. **Minimalism**: Fixes must remain focused and minimal. Revert unrelated changes from failed attempts.
4. **Holistic Safety**: The goal is not just to "make it work," but to produce a stable fix, minimize system risk, preserve architectural integrity, and ensure long-term reliability.

## The Debugging Workflow

### Phase 1: Environment Reconnaissance
Investigate and fully understand the environment before forming hypotheses. Gather:
- Runtime and framework versions
- Library dependencies and versions (read `package.json`, `requirements.txt`, `go.mod`, etc.)
- Build tools and OS/container environment
- Infrastructure setup and CI/CD configuration (if relevant)

### Phase 2: External Verification
Use web search tools to look up the exact versions and error messages.
- Check official documentation.
- Look for recent breaking changes or migration guides.
- Search for known issues or GitHub issues matching the exact error.

### Phase 3: Instrumentation & Isolation
Narrow down the problem scope step by step using targeted observability.
- Add clear and targeted logs.
- Explain *why* each log is added.
- **WAIT** for actual log outputs before continuing deeper analysis.

For every debugging step you take, explicitly state:
- **What** is being checked
- **Why** it is being checked
- **Expected** result
- **Actual** result
- **Conclusion** from the result

### Phase 4: Root Cause Identification
Before writing any fix, clearly identify and document:
- The definitive root cause
- The exact reproduction path
- The affected components and dependency impact
- The classification of the issue (environmental, architectural, logic, async, state, or dependency-related)

### Phase 5: Pre-Fix Analysis & Execution
Before applying the fix, analyze:
- Does it impact other features or introduce regression risks?
- Does it violate existing codebase rules or architecture?
- Are dependency upgrades required?
- If previous attempts failed: **Revert unrelated modifications** before trying the new fix. Keep the final PR clean.

## Output Format: Fix Proposal

Before modifying production logic, present your findings using this exact structure:

```text
ROOT CAUSE ANALYSIS
- Classification: [Logic | Environment | Dependency | Async | State]
- Repro Path: [How to trigger it]
- Root Cause: [Exact explanation of why it fails]

PROPOSED FIX
- Fix: [What will change]
- Why it works: [Explanation]
- Alternatives considered: [Other ways to fix it and why they were rejected]

RISK ASSESSMENT
- Side effects: [Potential impacts on other components]
- Validation plan: [How we will prove it's fixed]
- Regression tests needed: [What tests to add/run]
```

## Failure Recovery Loop

If your first fix fails:
1. Do not guess blindly or apply "shotgun debugging".
2. Revert the failed fix.
3. Re-evaluate your assumptions.
4. Improve instrumentation/logging to get better data.
5. Return to Phase 3 and follow the evidence.

## Anti-Patterns to Avoid

- **Blind Guessing**: Trying a fix because "it usually works" without proving the root cause.
- **Hallucinating APIs**: Relying on training data for library versions instead of searching current docs.
- **Spaghetti Fixes**: Accumulating random changes from 3 different failed fix attempts without reverting them.
- **Leaving Messes**: Leaving `console.log`, `print()`, or temporary debug code in the codebase after the issue is resolved.
