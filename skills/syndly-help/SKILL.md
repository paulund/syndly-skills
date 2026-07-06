---
name: syndly-help
description: Read-only reference for the Syndly MCP API. Use when an agent hits an MCP error or wants to know what tools are available and how to call them.
---

# Help

Read-only discovery and error-interpretation helper for the Syndly MCP API. Surfaces the live tool surface, lists the user's projects/sources/posts on demand, and interprets common MCP errors.

## What it does

1. Confirms MCP connectivity by calling `tools/list` once per session.
2. Surfaces the project's brand voice and platform profiles from `./.syndly/`.
3. Lists the user's accessible projects (subject to the bug noted below).
4. Interprets common MCP errors and points at recovery actions.
5. Walks through tool schemas when asked "how do I…".

## Pre-flight

Read `./.syndly/brand-voice.md` and `./.syndly/platforms/*.md` so the skill knows the project's voice setup before answering. If those files are missing, the skill still works but its answers won't be tailored to the project.

## Workflow

1. **Confirm connectivity.** Call `tools/list` (live MCP). If it fails, surface the error verbatim and stop — the user needs to fix auth first.
2. **Cache the surface.** Save the list of tools and their input schemas for the duration of the session.
3. **Discover the project.** Call `list_projects` to confirm which project slug(s) the API key has access to. Do not assume — the user may not know which project their key is bound to.
4. **Answer the query.** Combine:
   - The cached tool surface (from `tools/list`)
   - The brand voice profile (from `./.syndly/brand-voice.md`)
   - The platform profiles (from `./.syndly/platforms/*.md`)
   - The known-issues table below
5. **Surface errors verbatim.** If the user pastes an error message and it isn't in the known-issues table, return it unchanged. Don't paraphrase MCP errors into something friendlier.

## Query patterns

- *"What tools are available?"* — list from the cached surface, grouped by domain (projects / channels / posts / sources / analytics).
- *"I'm getting error X"* — look up X in the known-issues table. If found, return the meaning + recovery. If not found, return X verbatim with a note that it's an unknown error.
- *"How do I create a draft?"* — walk through `create_post`: required fields (`project_slug`, `account_id`, `content`, `type`), the `confirmed: true` gate on mutating tools.
- *"List my projects / sources / posts"* — call `list_projects` / `list_sources` / `list_posts` as appropriate. Use the project slug from step 3.
- *"Verify my MCP connection"* — run `tools/list` again and report.
- *"What does my brand voice say?"* — print `./.syndly/brand-voice.md`.

## Known issues

The MCP server has known limitations that affect what this skill can answer:

| Error message | What it means | Recovery |
|---|---|---|
| `Missing Authorization header` | `SYNDLY_API_KEY` (or `SYN_MCP_KEY`) is not in the agent's env | Set the env var; hint file at `./.syndly/README.md` documents where it lives |
| `Invalid API key` | Token is wrong, expired, or revoked | Regenerate the key in the Syndly web UI |
| `Project not found` | Slug typo or project was deleted | Call `list_projects` to discover valid slugs |
| `Project does not belong to this API key` | The API key is bound to a different project than the one in `project_slug` | The user can only see the project their key is bound to. To work with a different project they own via the web UI, they need to generate a new API key scoped to that project. (Tracked as Syndly issue #1497.) |

### Other known limitations

- `list_projects` returns only projects the API key is bound to. Web-UI ownership is invisible from MCP. (Tracked as Syndly issue #1497.)
- API keys are project-scoped, not user-scoped, despite the AGENTS.md description. (Same issue.)
- `add_source`, `create_post`, `schedule_post`, `publish_now`, `cancel_post`, `remove_from_queue`, `disconnect_channel`, `update_project_settings` all require `confirmed: true` in their input schema. This is enforced by Zod (`z.literal(true)`), so the call fails server-side if missing.

## Things this skill must NOT do

- Call any mutating MCP tool — this skill is read-only by name. Mutating tools (`add_source`, `create_post`, `schedule_post`, `publish_now`, `cancel_post`, `remove_from_queue`, `disconnect_channel`, `update_project_settings`, `remove_source`) belong in their dedicated skills (`/syndly-sources`, `/syndly-draft`, `/syndly-queue`).
- Persist the API key to any file.
- Log raw API responses containing project data, post bodies, or analytics — surface summaries instead.
- Paraphrase MCP error messages into something friendlier. The user (and any downstream agent) needs the actual reason.
- Skip the `tools/list` connectivity check. If it fails, every subsequent call will fail for the same reason.