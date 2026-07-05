---
name: syndly:draft
description: Draft a post from a capture using your brand voice profile and the target platform voice.
---

# Draft

End-to-end drafting flow: pick a capture, ask for the user's angle, draft the post using the brand voice and the target platform voice, get explicit approval, then enqueue or schedule on the chosen channel.

This is the only skill that requires both of the local voice files (`brand-voice.md` and the platform-specific profile). If either is missing, abort and tell the user to run `/syndly:setup` first.

## Pre-flight

1. Check that `~/.syndly/brand-voice.md` exists. If not: stop and say "Run /syndly:setup first to create your brand voice profile."
2. Check that `~/.syndly/platforms/<platform>.md` exists for the platform the user wants to post to. If not: tell the user `/syndly:setup` covers the platform but they may have skipped it; offer to ask the platform-tone interview questions now and write the file before continuing.
3. Confirm the user is targeting one channel at a time (X, LinkedIn, Facebook — only those platforms have voice profiles per Slice 6A).

## Workflow — two gates, no shortcuts

### Gate 1 — User's opinion on the capture

The capture itself (title, link, 200-char excerpt) is shown. Then ask exactly one question:

> "What's your angle on this? (one or two sentences — your take, the framing you want, anything to emphasise or skip)"

**Wait for the user's reply before continuing.** Do not draft a post on autopilot.

### Drafting

Combine:

- The capture's content
- The user's stated angle
- General voice from `~/.syndly/brand-voice.md`
- Platform voice from `~/.syndly/platforms/<platform>.md`

Apply platform-specific formatting conventions:

- **X**: ≤ 280 chars, plain text, optional 1-2 hashtags at the end
- **LinkedIn**: ≤ 3000 chars, line breaks every 1-3 sentences, opening line that earns the scroll
- **Facebook**: 1-5 short paragraphs, conversational, image-forward if relevant

Persist the draft to `~/.syndly/drafts/<ISO-timestamp>-<capture-slug>.md` with the raw draft body plus the YAML frontmatter recording the capture id, target platform, and angle that produced it. This makes the draft inspectable and reproducible.

### Gate 2 — Approval of the draft

Show the draft. Then offer four paths:

1. **Approve** — ask for target channel + scheduled time, then call the `schedule_post` MCP tool with `confirmed: true`. Set `user_specific_instructions` (the project's additional instructions field) to a short summary of the capture + angle so the post is traceable later.
2. **Reword first** — ask for the change, regenerate, return to the same approval gate. Do not call `schedule_post` until the user picks Approve.
3. **Save as draft only** — the file under `~/.syndly/drafts/` is enough; end the session with no MCP mutation. Good for "I want to come back to this later."
4. **Discard** — delete the file under `~/.syndly/drafts/` and end. No MCP mutation.

## Things this skill must NOT do

- Skip either of the two gates. Every drafting flow runs through opinion → approval.
- Call `schedule_post` without `confirmed: true` and the user's explicit choice of channel + time.
- Auto-pick a platform. Always confirm which platform and which channel before scheduling.
- Draft without reading `~/.syndly/brand-voice.md` — the brand voice is the whole point of having run `/syndly:setup`.
