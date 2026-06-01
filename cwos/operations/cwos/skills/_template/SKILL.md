---
name: skill-name
description: Specific description of when to invoke this skill and what it does. Be specific enough that another AI tool reading only this description (not the body) can determine relevance.
---

# Skill Name

Brief one-line summary if the title doesn't capture it.

## When to invoke

Concrete conditions that should trigger this skill. The AI checks these against the current task to decide if the skill applies.

## Inputs

- $ARGUMENTS (if the skill takes arguments)
- Any files or context the skill expects to read
- Any preconditions

## Procedure

1. First step. Be specific. State what to check or do.
2. Second step. If branching logic, use "If X, do A; else do B."
3. Third step. Continue.

For multi-step procedures with several branches, consider using a flowchart-style description in prose rather than nested lists.

## Outputs

What the skill produces. Could be:
- File modifications (and where)
- Git operations (and which)
- Messages to the user
- New artifacts created

## Verification

How to confirm the skill executed correctly. What does success look like? What's the next thing the user should see?

## References

- Related skills (if any)
- Canonical content this skill enforces (link to AICONFIG.md section, CWOS.md section, etc.)
- External documentation

---

**Template usage:**

Copy this directory to `operations/cwos/skills/<skill-name>/`. Edit the frontmatter (name, description) and the procedure body. Test by giving the AI a task that should match the description.

Keep skills focused: one skill per discrete procedure. If a procedure has natural sub-procedures, those can be sub-skills (their own directories) or just sections within one skill.

The `description` field is the central piece: AI tools decide relevance based on it. Generic descriptions get ignored or wrongly invoked. Be specific.

[End of Template]
