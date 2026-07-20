# Fundus Workbench Examples

Use these examples to calibrate agent behavior. They are not a replacement for current source evidence.

## Search

User: "Search Fundus for BACKEND-2291."

Agent behavior:

- Scan the inferred scope for `BACKEND-2291`, then perform a corpus search because a Jira ticket may connect project, Epic, Domain, and cross-repository notes.
- Read the strongest match if the scan result is directly relevant.
- Follow every `next_cursor`, retaining only one stable path, target, and revision, until `complete: true`.
- If continuation returns `READ_CURSOR_STALE`, discard partial content and restart before summarizing.
- Return a compact answer.
- Mention the note title/path briefly.
- If nothing relevant exists, say that no relevant Fundus note was found.
- Fully read no more than five directly relevant notes across no more than three scopes.

Example response shape:

```text
Fundus has related context in `Prompt Authoring Boundary`. It frames BACKEND-2291 as an authoring-surface boundary; current code still needs to be checked before treating that as implemented behavior.
```

## Opportunistic Research

User: "Research this ticket and summarize what matters."

Agent behavior:

- Check primary sources such as Jira, GitHub, and source code as needed.
- Optionally scan Fundus when prior context is likely useful.
- If Fundus is relevant, cite it briefly.
- If opportunistic Fundus search finds nothing useful, silence is fine unless the user explicitly asked for Fundus.

## Save

User: "Remember this domain rule: suppliers can have multiple remittance accounts, but only one default per currency."

Agent behavior:

- Infer scope from the conversation.
- If the conversation is domain or cross-repository work, prefer an area such as `Domains/...`.
- Search the inferred scope first.
- Use `propose_update` for a matching note or `propose_create` only when no good match exists.
- Apply the current proposal because the user's explicit save request supplies write intent; review every duplicate candidate before any override.
- Confirm with the title or path.

Example response shape:

```text
Saved in `Supplier Payment Rules`.
```

## Update

User: "We learned that BACKEND-2291 is actually about prompt-template lineage. Update everything relevant."

Agent behavior:

- Treat this as broad update intent.
- Use `propose_update`, then `apply_update` when the proposal remains current.
- Also update Jira or other requested systems when the user asks for them.
- Summarize what changed afterward.
- If known paths reveal ticket IDs or named parent areas, search the corpus and read the relevant parent before declaring the knowledge current.
- Do not mutate every corpus match. Add bidirectional links only when write intent covers both notes and each link improves navigation.

Example response shape:

```text
Updated Fundus note `Prompt Template Lineage` and linked BACKEND-2291 as an alias.
```

## Stale Note Proposal

User: "Research whether this behavior is still true."

Agent behavior:

- Source code and current primary work sources win over Fundus.
- If Fundus disagrees, do not silently mutate Fundus during ordinary research.
- Use `mark_stale` only when write intent covers recording the contradiction; otherwise tell the user the note appears stale and present a concise proposed replacement without applying it.

Example response shape:

```text
Fundus note `LLM OCR Retry Budget` appears stale: the code now applies the retry budget per page, not per document. Proposed Fundus update: replace the old retry paragraph with "Retry budget is page-scoped as of the current implementation."
```

## Scope Inference

Use project scope for:

- repository-specific architecture
- local implementation details
- tests
- module behavior
- deployment details for one repo

Use area scope for:

- epics
- domains
- capabilities
- interviews
- business rules
- story maps
- cross-repository decisions

When uncertain, infer the best scope and report where the note was saved. The user can move it later.

## Area Maintenance

User: "Simplify the old epic folders."

Agent behavior:

- Use the administrative CLI `area layout plan` first; planning is read-only.
- Review every move, collision, warning, source revision, and link rewrite.
- Apply only the exact proposal file when the user authorized the bulk change.
- Report the verified backup ID, move/link counts, corpus result, and rollback point.
- Do not imitate the proposal with shell moves or direct Markdown edits.

## Exact-Path Cross-Scope Maintenance

User: "Update these known project notes for BACKEND-2289 and make the knowledge current."

Agent behavior:

- Read the named notes completely and extract ticket IDs and parent-area references.
- Search the inferred project scope first, then use corpus search for `BACKEND-2289` and the parent-area name.
- Read only directly relevant results, capped at five notes and three scopes.
- Reconcile the project notes with the parent Epic or Domain before applying revision-bound proposals.
- Maintain useful links in both directions only when the user's update intent covers every affected note.
- Do not update weak matches merely because corpus search or `relationships audit` returned them.
