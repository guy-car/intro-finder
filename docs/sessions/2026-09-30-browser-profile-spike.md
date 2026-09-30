# Session: Browser persistence and first working skill

**Date:** 2026-09-30
**Intended outcome:** Test whether a manually established LinkedIn login persists
and define the next browser-access spike.
**Time available:** ~90 minutes

## What I did

Started with the browser profile spike from yesterday.

Created a Chrome profile inside the repo at `.browser-profile/`, logged into
LinkedIn manually, fully closed Chrome and reopened it with the same profile.
Still logged in - so that worked.

Then tried to have a Cursor agent use that profile. Turns out Cursor's native
browser tool can't be pointed at our own Chrome profile or attached to that
Chrome window. It opened a separate browser and got the LinkedIn login page.

So I tried logging into LinkedIn directly inside Cursor's native browser.

What I learned from that:
- Cursor seems to maintain its own browser storage / profile for the workspace
- the LinkedIn login persisted across different Cursor chats
- an open tab did not persist across chats, but a new chat could open a new tab
  and still be logged in

At that point I started testing navigation through normal prompts. Given a
LinkedIn company URL, agents were able to go to the People page, find people
with mutual connections, and even open LinkedIn's mutual connection results to
find a name hidden behind "and 1 other."

Turned that workflow into
`.cursor/skills/find-linkedin-intros/SKILL.md`.

Ran it four times across Grok 4.7 and GPT-5.6 Sol to see how stable the behavior
was and whether the models approached it differently.

## Decisions and why

For now, use Cursor's native browser for the personal MVP.

It already gives agents a browser they can control and persists the login across
chats. Building our own auth + browser control layer right now would mostly be
rebuilding things Cursor gives us.

The current "product" can just be an on-demand Cursor skill. Not the most
exciting engineering project lol, but already useful.

For the actual search behavior:
- use LinkedIn's recommended People results rather than crawling every employee
- only return useful paths where we know the connector's name
- ignore people who show as 2nd degree but don't show who the mutual is
- if LinkedIn says "and 1 other," go find that person's name before finishing

GPT-5.6 Sol did the most desirable workflow on the first try. Grok initially
left the hidden third mutual unresolved and spent time crawling extra employee
pages. Added more explicit completion rules to the skill and Grok then produced
the same useful result.

That matters because Grok is much cheaper for me in Cursor.

## What surprised me

The biggest surprise is how much Cursor already handles natively.

I thought we might need to build the authentication, persisted browser session,
and agent/browser bridge ourselves. For my current use case, Cursor already
solves most of that.

Also interesting to see how a small difference in the skill instructions changed
the cheaper model's behavior.

## Smallest sensible next step

Use the skill on a few different companies and see where it breaks or gives
unhelpful results.

From there, decide whether the next useful thing is role filtering, ranking the
paths, persistence, or going back toward browser automation that is not tied to
Cursor.
