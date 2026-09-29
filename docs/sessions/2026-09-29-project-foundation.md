# Session: Project foundation and first browser spike

**Date:** 2026-09-29
**Intended outcome:** Establish the public repository, decide how project context
should be documented, clarify the initial product direction, and identify one
small next experiment.
**Time available:** ~2h

## What I did

Mostly docs and planning today.
Set up the GH repo, document structure, AGENTS.md, brought in the Hello Interview framework so this serves as system design practice too

Reviewed the basics of authentication, cookies, web browsers

Challenged the asumption that we should use Browser Base (what Cape used) - first principle approach to the requirements

Identified clusters of uncertainty:
1. how does auth work? What needs to be persisted? How do we give access to an authenticated session to a program / AI agent?
2. what does navigation / data fetching look like once logged in? (mostly deferred looking into that for now)

## Decisions and why
Focus on auth for now - I want to understand deeply and not just build on a blackbox. I remember we were storing user cookies at Cape and I want to understand why, how they're created, reused, etc.

## Smallest sensible next step
Create a dedicated Chromium profile for this project, manually log in linkedin, see if that gives us a persistent logged in session

