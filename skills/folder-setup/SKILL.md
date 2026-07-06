---
name: syndly-setup
description: First-run onboarding. Scaffold ~/.syndly/ and write your brand voice profile plus per-platform tones.
---

# Setup

Walks you through first-run setup of the Syndly skill library on this machine. Run this once per machine before using any other Syndly skill.

## What it does

1. **Check the existing state.** If `~/.syndly/` already exists, ask the user: "The folder already exists. Re-run onboarding (overwrites the brand voice profile) or skip?" Only proceed after confirmation.

2. **Create the directory tree.** Run `mkdir -p ~/.syndly/platforms`. Confirm each directory was created.

3. **Detect the MCP API key.** Look for it in `env` (`SYNDLY_API_KEY` or `SYN_MCP_KEY`). If absent, ask the user to paste it and offer to add a hint to `~/.syndly/README.md` documenting where to put it. **Never persist the key itself to disk under the repo or under `~/.syndly/`.**

4. **Run the brand voice interview.** Ask the user, in this order, one question at a time. Wait for the answer before asking the next.

   - "In one sentence, what does this brand sound like? (e.g. measured-expert, witty-peer, warm-mentor)"
   - "Who is the audience? (job titles, expertise level)"
   - "Three topics you want the brand to own."
   - "Two phrases to **avoid** (anti-patterns)."
   - "The one thing the brand never does."

5. **Write `~/.syndly/brand-voice.md`.** YAML frontmatter for the structured fields plus a freeform notes section beneath. Example:

   ```markdown
   ---
   tone: measured-expert
   audience: senior backend engineers running production services
   own_topics:
     - incident retrospectives
     - post-mortem culture
     - platform reliability
   avoid:
     - "synergy"
     - "disrupt"
     - emojis in technical posts
   never: shill for vendor products
   ---

   ## Notes
   ...
   ```

6. **Run the per-platform interview.** Ask the user, one platform at a time, for a 2-3 sentence tone description:

   - **X (Twitter)**: short, punchy, threads are okay. Voice here may differ from the brand voice default.
   - **LinkedIn**: longer-form, professional but human, leads with insight or story.
   - **Facebook**: conversational, image-forward, community-friendly.

   For each platform the user opts to skip, write a `~/.syndly/platforms/<platform>.md` containing just a one-line note that the platform is intentionally inactive, so future invocations don't ask again.

7. **(Optional) Add the first source.** Ask: "Add a first content source now? (URL or RSS feed)" If yes, look up the relevant MCP tool name (start with `list_sources` to confirm connectivity, then call the add-source tool the server exposes). Always show the user what's about to be added and wait for confirmation before invoking any mutating tool.

8. **Print the summary.** Confirm:
   - Path to `~/.syndly/`
   - Brand voice fields captured
   - Per-platform profiles written / skipped
   - First source added (or skipped)
   - Reminder: invoke `/syndly-captures` or `/syndly-draft` next time you want to draft a post.

## Things this skill must NOT do

- Write the MCP API key to any file under `~/.syndly/` or anywhere else.
- Invoke mutating MCP tools (anything that creates / updates / removes a row) without showing the user the planned change and waiting for explicit confirmation.
- Skip past the brand voice interview even if the user seems impatient. This document is the foundation the rest of the library reads from.
