---
name: run-prompt-protocol
description: Execute a "run prompt" instruction by reading a specified prompt at a timestamp in a conversation file, executing the instruction, then writing the response back to that conversation file as an [AI Assistant | TIMESTAMP] entry plus a [User | +2min] stub. Use when the user says "run prompt [User | DATE TIME] in FILE.md".
---

# Run-Prompt Protocol

This is the central workflow pattern for this repo's run-prompt-driven AI sessions. The user issues a structured invocation; the AI reads the specified prompt, executes, writes back the response, and adds a stub for the user's next entry.

## When to invoke

Whenever the user says something matching:

- "run prompt [User | DATE TIME] in FILE.md"
- "execute [User | DATE TIME] in FILE.md"
- "read prompt at [User | DATE TIME] in FILE.md and do what it says"

The invocation specifies a timestamp and a file. The prompt content is at that timestamp inside that file.

## Inputs

- A conversation file path (FILE.md)
- A timestamp matching a `### [User | DATE TIME]` entry inside that file
- (Implicit) the file's prior content as context

## Procedure

### 1. Find the prompt

```bash
grep -n "DATE TIME" <FILE.md>
```

Or search by timestamp pattern. Read lines starting at the matched line to find the user's prompt content under `### [User | DATE TIME]`.

If the timestamp doesn't exist in the file: flag it to the user. The user may have typed a wrong date or the prompt may be in a different file.

### 2. Read context

Skim the surrounding entries (typically the last 3-5 [User] / [AI Assistant] exchanges) for context. The prompt makes more sense in the conversation flow.

### 3. Execute the instruction

Do what the prompt asks. Could be: edit files, run commands, answer a question, propose a plan, etc.

Per AICONFIG.md rules:

- No autonomous commits — file edits OK, git commit/push require explicit instruction
- Respect all behavioral rules (no markdown tables, no meta-evaluation, no load-bearing figuratively, no em-dashes, etc.)
- Engage directly with content; no meta-evaluation of user input

### 4. Run `date` for an accurate timestamp

Right before writing the response, run:

```bash
date "+%B %-d, %Y %-I:%M%p"
```

This returns the actual system time. Do NOT extrapolate from earlier-session timestamps. Drift compounds.

### 5. Write the response into the conversation file

Append to the file under the user's prompt:

```markdown
### [User | DATE TIME]

<user's prompt content>

---

### [AI Assistant | ACTUAL CURRENT TIMESTAMP]

<your response>

---

### [User | DATE TIME + 2 minutes]
```

The stub at the bottom uses the +2-minute timestamp:

```bash
date -v+2M "+%B %-d, %Y %-I:%M%p"
```

Note: uppercase `M` is minutes (lowercase `m` is months — verified empirically).

### 6. Avoid duplicating the response in chat

The response was written to the file. The user reads it there. In chat, give a brief summary (1-3 sentences) acknowledging the work was done, NOT the full response. The conversation file is the durable record; chat is ephemeral.

If you accidentally only respond in chat without writing to the file, the user will (rightly) flag this as a protocol violation. The file is the canonical location.

## Outputs

- Updated conversation file with:
  - `[AI Assistant | TIMESTAMP]` entry containing the response
  - `[User | TIMESTAMP+2min]` stub for the user's next entry
  - All preceding content intact
- Brief chat summary acknowledging completion

## Verification

- File now contains the AI Assistant entry
- Stub for next user entry is in place
- Timestamps are accurate (run `date` immediately before writing)
- Response uses `[AI Assistant | ...]` label (vendor-agnostic), not `[Claude | ...]`
- All behavioral rules from AICONFIG.md applied (no tables, no meta-evaluation, etc.)

## Common failure modes

- **Responding only in chat, not in the file** — protocol violation; the file is the durable record
- **Using `[Claude | ...]` label instead of `[AI Assistant | ...]`** — vendor-agnostic preference per AICONFIG.md
- **Extrapolated timestamp** — wall-clock time advances during reasoning; always run `date` immediately before the entry
- **Markdown tables in the response** — per AICONFIG.md formatting rules, use bullet lists or prose instead
- **Meta-evaluation opener** ("Good catch", "Good point") — per AICONFIG.md, engage directly

## References

- AICONFIG.md `## STANDARDS - OPERATIONS` → "Conversation Files" for the convention details
- AICONFIG.md `## STANDARDS - WRITING` for all the writing rules to apply in the response
- `operations/cwos/memory/feedback_conversation_file_protocol.md` (in memory after Session C) for the feedback memory backing this convention
- `operations/cwos/reference/taxonomy.md` for terminology (conversation file, run-prompt, stub)

---

[End of Skill]
