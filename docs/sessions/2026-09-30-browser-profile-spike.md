# Session: Browser persistence and first working skill

**Date:** 2026-09-30
**Intended outcome:** Test whether a manually established LinkedIn login persists
and define the next browser-access spike.
**Time available:** ~90 minutes

## What I did

I started with the browser-profile spike from yesterday. I launched Chrome with
the repository's ignored `.browser-profile/`, logged into LinkedIn manually,
fully quit Chrome, and reopened the same profile. The LinkedIn session remained
authenticated.

I then tested whether Cursor's native browser could remove the need to build our
own authentication and browser-control layer. After one manual LinkedIn login,
Cursor reused the authenticated browser state across multiple chats. Open tabs
were scoped to an individual chat, but a new chat could open its own tab and
reuse the existing authentication.

From there, I experimented with natural-language browser navigation. Agents
could navigate from a LinkedIn company URL to its People page, identify people
with named mutual connections, and open LinkedIn's mutual-connections results
to resolve a hidden connector.

I turned the observed workflow into the project skill
`.cursor/skills/find-linkedin-intros/SKILL.md` and tested it four times using
Grok 4.7 and GPT-5.6 Sol.

## Decisions and why

- Use Cursor's native browser for the first personal MVP. It already provides
  persistent authentication and an agent-browser bridge, so building those
  layers now would solve a problem the current environment already solves.
- Keep the dedicated `.browser-profile/` as a completed experiment rather than
  the active implementation path.
- Implement the current product as an on-demand Cursor skill instead of a
  standalone application.
- Search LinkedIn's recommended People results by default rather than crawling
  every associated member.
- Return only actionable paths with named connectors. Ignore unnamed
  second-degree cards.
- Always resolve truncated summaries such as `and 1 other` before finishing.
- Prefer Grok when the skill can make its behavior equivalent because it is much
  cheaper for this workflow. GPT-5.6 Sol remains a useful quality baseline.

## What confused or surprised me

- I expected authentication and browser control to require significant custom
  implementation, but Cursor already handled most of the useful personal
  workflow.
- Cursor persists browser authentication state across chats, while individual
  tabs do not persist across chats.
- Agents could accomplish the core product behavior from a loosely defined goal
  with minimal guidance.
- Model behavior varied. GPT-5.6 Sol resolved the nested third mutual and
  finished more directly. Grok initially left that connector unresolved and
  explored unnecessary employee pages.
- Explicit completion criteria in the skill brought Grok's behavior in line with
  the desired result.

## What I learned

- A dedicated Chrome user-data directory can preserve a manual LinkedIn login
  across browser restarts.
- For the current MVP, Cursor's workspace-scoped browser state is a simpler
  authentication boundary than a repository-controlled browser profile.
- Native browser navigation can be slow when a tool returns a large page
  snapshot. Small DOM/CDP inspections were generally more reliable.
- A thin, well-specified agent skill can be a useful product implementation when
  the host environment already supplies the necessary capabilities.
- Small multi-model evaluations expose underspecified completion criteria. The
  right first response is to tighten the workflow, not immediately mandate the
  more expensive model.

## Smallest sensible next step

Use the skill against a few different LinkedIn company pages. Record where the
workflow breaks or produces unhelpful output, and only then decide whether role
filtering, ranking, persistence, or repository-controlled automation is the next
valuable increment.
