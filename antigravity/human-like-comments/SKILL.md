---
name: human-like-comments
description: Enforce pragmatic, human-like code comments. Ban AI-style "stating the obvious" and filler words. Emphasize the "WHY" over the "WHAT". All comments must be in English.
origin: custom
---

# Human-Like Code Comments

Use this skill to ensure code comments read like they were written by a pragmatic, experienced human engineer, not an AI generating boilerplate text. 

## When to Activate

- Writing new code, functions, or classes.
- Refactoring or reviewing existing code.
- Adding documentation or docstrings to a codebase.
- The user complains about "too many comments", "robotic tone", or "AI slop".

## The Golden Rules

1. **ENGLISH ONLY**: All comments, docstrings, and TODOs must be written in English, regardless of the language used in the prompt.
2. **Explain the WHY, not the WHAT**: Code tells you *what* is happening and *how*. Comments must tell you *why* it was done that way, the business logic behind it, or why a specific weird approach was chosen.
3. **Silence is better than noise**: Do not comment obvious code. If the code is self-explanatory, leave it uncommented.
4. **No AI Vocabulary**: Ban words like "robust", "seamlessly", "elegantly", "leverage", "utilize", "crucial", or "it is important to note".

## Anti-Patterns (AI-Slop to Delete)

Delete and rewrite any of these AI-characteristic comment patterns:

### 1. Stating the Obvious (Play-by-play commenting)
```javascript
// FAIL: BAD (AI Style)
// Check if the user exists
if (!user) {
    // Return an error message
    return { error: "Not found" };
}

// PASS: GOOD (Human Style)
// Empty comment. The code is obvious.
if (!user) {
    return { error: "Not found" };
}
```

### 2. Robotic & Pompous Vocabulary
```javascript
// FAIL: BAD (AI Style)
/**
 * This robust function elegantly leverages the formatting utility 
 * to seamlessly transform the user input into a standardized string.
 */
function formatName(input) { ... }

// PASS: GOOD (Human Style)
/**
 * Strips special characters and normalizes casing for legacy DB support.
 */
function formatName(input) { ... }
```

### 3. Over-explaining standard framework features
```javascript
// FAIL: BAD (AI Style)
// Use React's useEffect hook to fetch data when the component mounts
useEffect(() => { ... }, []);

// PASS: GOOD (Human Style)
// Prefetch catalog data to prevent waterfall loading on the next screen.
useEffect(() => { ... }, []);
```

## Human Patterns (What to do instead)

Real engineers use comments to warn their colleagues, explain business rules, and document technical debt.

### 1. Explaining Business Rules
```typescript
// Tax rate is hardcoded to 15% here because the dynamic tax API 
// doesn't support the legacy 'wholesale' account type yet.
const effectiveTax = isWholesale ? 0.15 : calculateDynamicTax(user);
```

### 2. Warning about Edge Cases or "Hacks"
```python
# HACK: Sleep for 50ms to allow the external audio buffer to flush.
# Without this, Firefox truncates the last frame of the recording.
time.sleep(0.05)
```

### 3. Professional TODOs
```java
// TODO: Replace this O(n^2) loop when the backend team ships the bulk-fetch endpoint (Expected Q3).
```

### 4. Docstrings (API/Public methods)
Docstrings should be direct. Use imperative mood (e.g., "Return the item", not "This function returns the item").

```python
# GOOD:
def parse_date(date_str):
    """Parse ISO-8601 string to UTC datetime. 
    Returns None if the string is malformed or empty.
    """
```

## Tone & Voice Checklist

Before writing any comment, run it through this filter:
- [ ] Is it written in **English**?
- [ ] If I delete this comment, is the code harder to understand? (If no, delete it).
- [ ] Does it sound like a tired senior engineer leaving a helpful note for a teammate?
- [ ] Are all filler words ("leverage", "seamless", "moreover") removed?
- [ ] Does it focus on business context, limitations, or external constraints rather than syntax?
