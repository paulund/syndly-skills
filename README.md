# syndly-skills

Pre-built agent skills that wrap [Syndly](https://github.com/PaulundOrg/socialqueue) MCP tools into reusable social media workflows. Install the whole library in one command, then invoke each skill as `/syndly-<role>` in Claude Code (or any skills.sh-compatible agent).

## Install

```bash
npx skills add paulund/syndly-skills
```

This installs every skill in the library into your agent's skills directory. After it runs, you can invoke any of them with `/syndly-<role>`.

## Skills

| Invocation            | What it does                                                                                                                                              |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/syndly-setup`       | First-run onboarding. Scaffolds `./.syndly/` at the project root, walks the brand-voice profile interview, writes per-platform tones.                   |
| `/syndly-sources`     | List, create, edit, and remove content sources (RSS feeds, sitemaps) that supply news captures.                                                           |
| `/syndly-captures`    | Show news captures that have arrived since your last check.                                                                                               |
| `/syndly-draft`       | Pick a capture → your angle → draft a post in your brand voice and the target platform voice → enqueue on approval. End-to-end.                            |
| `/syndly-queue`       | List the scheduled and queued posts across channels, with status, target channel, and scheduled time.                                                     |
| `/syndly-performance` | Summarise recent post performance — top posts, channel-level engagement, follower deltas — and surface recommendations.                                   |

Run `/syndly-setup` first — it creates the brand-voice profile that the daily-use skills (`draft`, `performance`) read from.

## Naming convention

All skills in this library are hyphen-namespaced under `syndly-` (e.g. `syndly-setup`, `syndly-draft`). The directory name and the frontmatter `name:` field must match exactly.

## Project-level state

All brand voice, platform tones, drafts, and skill state live in `./.syndly/` at the project root. Each project gets its own voice profile; brand voice and platform profiles are version-controlled, drafts and cursor state are ephemeral and should be in `.gitignore`.

Typical `.gitignore` entries:

```
.syndly/drafts/
.syndly/state/
```

## License

MIT
