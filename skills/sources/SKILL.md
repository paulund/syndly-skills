---
name: syndly:sources
description: List, create, edit, and remove content sources that Syndly uses to discover news captures.
---

# Sources

Lists your project's content sources (RSS feeds, sitemaps) and supports create / update / delete verbs on top of the existing Syndly MCP tools.

## How to determine intent

If the user's message is unambiguous, act. If they say "show my sources" or "what sources do I have", list. If they say "add an RSS feed", create. If they mention changing a setting on an existing source, update. If they say "drop the X source" or "remove X", delete.

When intent is ambiguous, ask one short clarifying question and wait.

## Workflow

1. **Always start with a list.** Call the read-only source-list tool / resource (`list_sources`) at the start of every session so you have a current picture in memory. Don't trust that a previous session's view of the world is accurate.

2. **Match the verb to a tool.**

   - List — read-only tool. No confirmation needed.
   - Create — call the add-source tool. Show the user the URL + the inferred name + the inferred crawl schedule before invoking. Wait for confirmation.
   - Update — call the update-source tool. Show old value vs new value for each changed field. Wait for confirmation.
   - Delete — call the remove-source tool. Show the user which source will be removed and the count of news captures under it. **Always confirm.**

3. **Approval gate.** Every mutating tool must be preceded by a confirmation prompt in the chat. The MCP server enforces `confirmed: true` server-side, so if the user denies the call, do not retry.

4. **No silent failures.** If a tool returns an error, surface the error message verbatim. Don't paraphrase it into something friendlier — the user needs the actual reason.

## Things this skill must NOT do

- Mutate a source without showing the planned change and waiting for confirmation.
- Batch-update or batch-delete sources in a single call without listing them one by one for the user.
- Edit the brand voice profile. That lives under `~/.syndly/brand-voice.md` and is managed by `/syndly:setup`, not here.
