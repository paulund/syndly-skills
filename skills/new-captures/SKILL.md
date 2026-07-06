---
name: syndly-captures
description: Show news captures that have arrived since your last check.
---

# New Captures

Check the Syndly project for content captures that have arrived since you last looked, and surface the new ones so the user can pick one to draft from.

## Setup

On first run in a session, ensure `~/.syndly/state/` exists (create it if not). Read or initialise `~/.syndly/state/last-captures-check.txt` with the current ISO timestamp — this is the cursor you advance each time the skill runs.

## Workflow

1. **List recent captures.** Call the read-only `list_captures` MCP tool, or read the equivalent `syndly://<slug>/sources/<source_id>/captures` resource for each known source — whichever is cheaper. Request the maximum default window so you have a full picture.

2. **Filter to "since last check."** Discard anything with a `published_at` / `captured_at` timestamp older than the value in `~/.syndly/state/last-captures-check.txt`. If the file is missing, treat the run as a first-time check and show everything from the last 7 days.

3. **Surface the new ones.** Present a compact table to the user:

   | Title | Source | Link | Captured at |
   | ----- | ------ | ---- | ----------- |

   Order by `captured_at` descending (most recent first).

4. **Offer follow-ups.** After showing the table, offer:

   - "Draft a post from any of these" — hand off to `/syndly-draft` with the chosen capture.
   - "See all captures (ignoring the cursor)" — useful when the user wants to backfill old content.
   - "Update the cursor without drafting" — advance the timestamp file and end the session.

5. **Always update the cursor.** At the end of a successful run (whether or not the user drafted anything), overwrite `~/.syndly/state/last-captures-check.txt` with the current ISO timestamp. This is the *completion signal* the skill relies on — never skip it.

## Things this skill must NOT do

- Call mutating MCP tools (`schedule_post`, `publish_now`, `add_to_queue`, etc.) — drafting and queueing are handled by `/syndly-draft`.
- Show captures older than the cursor without an explicit "see all" ask first.
- Skip the cursor update "to save time." Skipping it means the next run shows everything again and the user loses state.
