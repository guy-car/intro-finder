# LinkedIn Browser Access Exploration

**Status:** First spike defined but not implemented.

## Product context

The intended experience is local and IDE-first. A user gives an agent a LinkedIn
company URL and receives relevant employees at that company, their roles, and
the people in the user's network who could provide an introduction.

An early result might look like:

- Roger — Senior Engineer at XYZ — 8 people in your network know him
- Maria — CTO at XYZ — 3 people in your network know her

There is no standalone frontend in the current product direction.

Three broad components have emerged:

1. Resolve a company name or domain to its LinkedIn company URL. This is useful
   for future job-lead workflows but is currently out of scope.
2. Establish and reuse an authenticated LinkedIn session.
3. Navigate LinkedIn and extract the data needed to identify introduction paths.

## Two areas of uncertainty

### 1. Authentication and session reuse

How can a user log in manually and allow a later browser process to remain
authenticated?

The current mental model is:

- LinkedIn returns one or more session credentials after a successful login.
- The browser stores relevant cookies and other site state in its profile.
- The browser automatically includes matching cookies in later requests.
- The session works while both the local state remains available and LinkedIn
  continues to accept it.

A browser profile is a directory containing cookies, site storage, settings, and
other browser state. For this personal prototype, the working preference is to
keep a dedicated profile at `.browser-profile/` inside the repository and exclude
it from Git.

No browser automation library or managed browser provider has been selected.
Playwright and Browserbase are candidates to evaluate later, not current
decisions.

### 2. Navigation and extraction

Given an authenticated session, how should an agent navigate LinkedIn and
retrieve employee and mutual-connection data?

Potential mechanisms include deterministic browser automation, DOM or
accessibility-tree interaction, network-response inspection, vision-based
computer use, or a hybrid. This area is intentionally deferred until session
reuse is understood.

## First spike: dedicated profile session persistence

### Question

Can a manually established LinkedIn login survive closing and reopening a
browser that uses a dedicated profile directory?

### Steps

1. Start Chrome with an empty `.browser-profile/` directory.
2. Navigate to LinkedIn and log in manually.
3. Confirm that a protected page is accessible.
4. Fully quit that dedicated browser process.
5. Start Chrome again using the same profile directory.
6. Navigate to LinkedIn and check whether the session remains authenticated.

### Success criteria

LinkedIn opens as authenticated after the browser is restarted, without asking
the user to enter credentials again.

### Non-goals

This spike does not need to:

- Identify the exact cookie or storage record responsible for authentication.
- Introduce Playwright or another automation library.
- Give an IDE agent control of the browser.
- Navigate employee or mutual-connection pages.
- Extract or persist LinkedIn data.
- Prove that the session will remain valid indefinitely.

## Likely follow-up

If the first spike succeeds, define a second spike that launches or controls the
same dedicated profile through a program. Agent navigation and data extraction
should remain a separate concern so each experiment teaches one thing clearly.
