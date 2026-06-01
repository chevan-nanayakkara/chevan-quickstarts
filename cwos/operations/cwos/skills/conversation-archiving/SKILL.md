---
name: conversation-archiving
description: Archive content from a conversation file that has exceeded the size threshold. Use when a conversation file is over 75KB and needs to be split into historical archive chunks while preserving an active working file under threshold.
---

# Conversation Archiving

Conversation files grow over time. When they exceed size thresholds, archive older content into chunks so the active file stays processable while history remains accessible.

## When to invoke

- A conversation file is over 75KB (warning) or 100KB (must archive)
- User asks to archive a specific conversation file
- Maintenance check surfaces large files needing attention
- A natural break point exists in the conversation (phase transition, completed work) where archiving creates clean separation

## Size thresholds

- **Under 75KB:** no action needed
- **75-100KB:** consider archiving at next natural break point
- **Over 100KB:** archive immediately
- **Target after archive:** active file back to 30-50KB

## Inputs

- $ARGUMENTS: the path to the conversation file to archive (e.g., `aiconversations/spoke-slcdems/slcdems-tech-committee-conversation.md`)
- Read the file's current frontmatter and content
- Identify natural break points in the conversation

## Procedure

### 1. Verify the file needs archiving

Run `ls -lh` or read the file size. Confirm it's over threshold. If under 75KB, abort with a note.

### 2. Identify the natural break point

Read the file. Find a place where archiving creates clean separation:

- End of a completed sub-project
- Major phase transition
- Session boundary with no active follow-up
- A specific date boundary the user names

If no natural break point exists, archive the oldest ~70% of content (keep most recent third active). Ask the user if the heuristic break point is acceptable before proceeding for large files.

### 3. Compose the archive chunk

Create at the appropriate archive path:

- For conversation files in `aiconversations/<spoke>/`: archive goes to `aiconversations/<spoke>/archives/<conversation-name>/<YYYY-MM-DD-HHMM>-archive.md`
- For top-level conversation files: archive goes to `aiconversations/archives/<conversation-name>/<YYYY-MM-DD-HHMM>-archive.md`

Date-stamp format is the START time of the first archived entry (or session start of the chunk).

The archive chunk needs frontmatter:

```yaml
---
documentType: Conversation Archive Chunk
dateRange: <start> through <end>
companionTo: <path to active companion file or project planning doc>
parentConversation: <path to the active conversation file>
previousChunk: <relative path to prior chunk if any, else ~>
nextChunk: <relative path or "Active conversation">
manifest: <path to archives/<name>/README.md>
status: Archived - Historical Context
location: <full path to this archive file>
---
```

Followed by a brief header noting the archive period and navigation, then the archived content verbatim.

### 4. Update or create the archive manifest

Each archive folder has a README.md manifest listing chunks:

```markdown
# <Conversation Name> Archives

Archive chunks for `<active file path>`.

## Chunks

| File | Date Range | Size |
|------|------------|------|
| YYYY-MM-DD-HHMM-archive.md | Period 1 | ~XXKb |
| ... | ... | ... |
```

Or use bullet lists if avoiding tables per AICONFIG.md formatting rules.

### 5. Trim the active file

Update the active conversation file:

- Remove the archived content
- Update frontmatter: bump version, increment archive count in `archives:` field, add `archivesNote:` describing what was archived
- Update opening section if it references archives
- Keep the active conversation thread continuing where it was

### 6. Verify

- Active file is under 75KB (target 30-50KB)
- Archive chunk is under 100KB (target under that)
- Archive manifest lists the new chunk
- No content lost between active and archive (concatenated, they should reproduce the original minus duplicated header)
- All cross-references intact

### 7. Report to user

State what was archived, the new active file size, the archive chunk's path. If the user is committing, suggest a commit message like:

```
docs(aiconversations): archive <name> chunk YYYY-MM-DD (active: NNk → NNk)
```

## Outputs

- New archive chunk file at `archives/<conversation>/<YYYY-MM-DD-HHMM>-archive.md`
- Updated archive manifest at `archives/<conversation>/README.md`
- Trimmed active conversation file (under threshold)
- Updated frontmatter on the active file

## Verification checklist

- Archive chunk has full frontmatter and content
- Archive manifest reflects the new chunk
- Active file under 75KB after trim
- Active file's frontmatter `archives:` count incremented
- No orphan references between active and archive
- File system: `ls archives/<conversation>/` shows new chunk

## Don't archive when

- File is under 75KB — no need yet
- File is over threshold but there's no natural break point AND the user hasn't authorized a heuristic split
- Active discussion mid-thread that would be confusing to split
- The user wants to wait for a specific milestone before archiving

## References

- AICONFIG.md `## STANDARDS - OPERATIONS` → "Conversation Files" for the convention details
- Existing archive examples: `aiconversations/spoke-slcdems/archives/slcdems-tech-committee/` for a real implementation
- `operations/cwos/reference/taxonomy.md` for terminology (conversation file, archive chunk, manifest, active vs archived)

---

[End of Skill]
