---
name: syndly:performance
description: Summarise recent post performance with recommendations.
---

# Performance

Read-only weekly performance report covering recent Syndly posts. Composes a one-page summary from the analytics resource surfaces, with concrete recommendations the user can act on by hand using `/syndly:draft`.

## Workflow

1. **Pull the analytics surfaces** that Syndly exposes. Sources to consider (use whichever are present in the registry):

   - `syndly://<slug>/analytics/summary` — overall activity summary
   - `syndly://<slug>/analytics/channels/<channel_id>/followers` — follower deltas per channel
   - `syndly://<slug>/analytics/top-posts?limit=10` — top N posts by engagement
   - `get_summary` MCP tool (some versions) — same data, different shape

2. **Window.** Default to the last 7 days. Honour the user's "last N days" if they specify one.

3. **Compose the report.** Render in this order:

   - **Top 3 posts (last 7d)** — title fragment, channel, engagement, link. One line per post.
   - **Per-channel engagement trend** — engagement (likes + reposts + comments) over the window, compared to the prior period. One line per channel with a small bar comparison.
   - **Follower deltas** — net follower change per channel over the window. Sort descending.
   - **3 recommendations** — short, actionable, and tied to a specific data point. Never generic ("post more"). Examples of good recs:
     - "Your two highest-engagement LinkedIn posts both opened with a question. Try leading the next 3 posts with a one-line question."
     - "X engagement dropped 30% mid-week — check if scheduling shifted. Consider resuming the morning slot."
     - "Follower growth on LinkedIn outpaced Facebook 4x. If growth is the goal this quarter, tilt next week's `/syndly:draft` runs toward LinkedIn."

4. **Honour no-mutation policy.** The report is text only. No mutating tool calls in this skill — the recommendations are advisory, and the user executes them through `/syndly:draft` (which they invoke themselves).

5. **Output.** Render the report inline. If the user asks "give me the raw numbers" or "show me the underlying data", offer to dump the raw JSON of the analytics resources, but never do so unprompted.

## Things this skill must NOT do

- Call mutating MCP tools.
- Recommend changes the user did not ask for ("while you're at it, you should also..."). One report, three recommendations, stop.
- Sample size: if the window has fewer than 3 posts, surface that fact ("only N posts in the window — recommendations are weak; consider widening to 14d or 30d") and let the user decide.
