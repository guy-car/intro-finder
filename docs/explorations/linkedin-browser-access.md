# LinkedIn Browser Access Exploration

**Status:** Authentication and navigation spikes completed; Cursor-native MVP selected.

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

## Results

### Dedicated browser profile

The first spike succeeded. Chrome stored the manually authenticated LinkedIn
session in `.browser-profile/`, and the session survived fully quitting and
reopening Chrome with the same `--user-data-dir`.

This proved that a repository-local browser profile can provide persistent
authentication. It did not provide an agent-browser control bridge.

We also gave a fresh Cursor browser agent the path to `.browser-profile/` and
asked it to use that authenticated session. Cursor's native browser tools could
not select a custom user-data directory or attach to the external Chrome
instance. They opened a separate managed browser and reached LinkedIn's login
page instead.

### Cursor native browser

Cursor's native browser solved both persistence and agent access for the current
personal use case:

- Cursor appears to maintain its own workspace-scoped browser storage or
  profile. The exact internal implementation is not part of the interface we
  rely on.
- A manual LinkedIn login produced workspace-scoped browser authentication state.
- That authentication state persisted across new Cursor chats.
- An open browser tab did not carry into a new chat; the new agent opened its own
  tab and reused the existing authentication.
- Agents could navigate LinkedIn read-only, inspect company People pages, and
  follow LinkedIn's mutual-connections results.

Browser navigation sometimes waited after the page appeared visually loaded.
Small DOM/CDP inspections were faster and more reliable than repeatedly
requesting large accessibility snapshots.

### Skill prototype

The project skill at `.cursor/skills/find-linkedin-intros/SKILL.md` now accepts a
LinkedIn company URL and uses Cursor's authenticated native browser to return
named introduction paths.

Four test runs across GPT-5.6 Sol and Grok 4.7 showed that:

- Both models found the same eight recommended people with named mutuals.
- GPT-5.6 Sol resolved a truncated `and 1 other` mutual summary without extra
  guidance.
- Grok initially stopped with the third connector unresolved and spent time
  crawling additional employee pages.
- Explicit completion criteria brought Grok's behavior in line with the desired
  workflow: inspect recommended results, resolve every truncated mutual summary,
  and ignore second-degree cards without named connectors.

## Current direction

Use Cursor's native browser and the project skill for the first useful personal
version. The dedicated `.browser-profile/` remains a successful experiment but
is not needed for the Cursor-first MVP.

Repository-controlled browser automation may become worthwhile later if a real
need emerges for portability beyond Cursor, deterministic extraction, or deeper
browser-automation learning.
