---
name: syndly-queue
description: List the current post queue and its status.
---

# Queue

Read-only view of what's currently scheduled or queued in Syndly. Use this to check what will go live and when, or to decide whether to back off before running `/syndly-draft` for a new capture.

## Workflow

1. **Pull the queue.** Call the read-only `list_posts` MCP tool. If you have a `post_status` filter, use it; otherwise pull everything and filter locally.

2. **Filter to queue-worthy statuses.** Keep entries with status ∈ `{queued, scheduled}`. Verify the actual status strings against `src/mcp/schemas/` if there's any doubt — the strings have shifted historically.

3. **Group by channel.** Show one section per channel. Within each section, sort by `scheduled_for` ascending (soonest first). Skip channels with no queued posts.

   ```
   ## X (@acme)
   - 09:00 — first-line-of-the-pinned-thread (scheduled)
   - 17:00 — capture 2026-07-04 (queued)
   ## LinkedIn (Acme Engineering)
   - 2026-07-08 09:00 — draft v3 (scheduled)
   ```

4. **Surface warnings.** If the user appears to have many posts queued at the same time on the same channel (≥ 3 posts within a 24h window for X, or any back-to-back LinkedIn posts within 4h), call it out as a question, not a directive — sometimes intentional, sometimes an accident.

5. **Offer follow-ups.** After the queue view:

   - "See only one channel" — filter the table down
   - "Triage — flag posts to revisit" — pull the related MCP tool the server exposes for triage if you can find it
   - "Pause everything scheduled for tomorrow" — surface the relevant tool name (`cancel_post` or similar); do not invoke it without explicit confirmation

## Things this skill must NOT do

- Call any mutating MCP tool. Queue is read-only by name.
- Reorder or modify queued posts. That's `/syndly-draft` or future ops skills.
- Show drafts that are not yet queued. If the user wants drafts, point them at `.syndly/drafts/` directly.
