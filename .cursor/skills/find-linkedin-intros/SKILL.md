---
name: find-linkedin-intros
description: Finds read-only warm-introduction paths at a company using LinkedIn's authenticated website in Cursor's native browser. Use when the user provides a LinkedIn company URL or asks who in their network can introduce them to people at a target company.
---

# Find LinkedIn Intros

Use Cursor's native browser to identify people at a target company who share
mutual connections with the user.

## Inputs

Require a LinkedIn company URL. If the user provides only a company name or
domain, ask for the LinkedIn company URL; resolving it is not part of this
workflow yet.

## Safety boundary

This is a read-only workflow.

- Never follow, connect, message, react, post, apply, save, or change settings.
- Never read or return cookies, credentials, request headers, browser storage, or
  profile files.
- Do not use unrelated tabs or visit unrelated sites.
- Keep the browser locked only while actively operating it, and always unlock it
  before finishing or reporting an error.

## Workflow

1. List the native browser tabs.
2. Navigate a tab to `https://www.linkedin.com/feed/`.
3. Verify authentication from the final URL and page title.
   - If LinkedIn redirects to login, stop and ask the user to log in manually.
   - Never request credentials in chat.
4. Convert the supplied company URL to its `/people/` page and navigate there.
5. Confirm the company name and that the People page loaded.
6. Inspect visible person cards containing `mutual connection` or
   `mutual connections`.
   - Use LinkedIn's recommended People results by default.
   - Do not paginate through every associated member unless the user explicitly
     requests exhaustive coverage.
   - Ignore second-degree cards that do not name any connector.
7. For each matching card, extract:
   - Person name
   - Current title or headline, when visible
   - LinkedIn profile URL, when available
   - Visible mutual-connection summary
8. If a mutual summary is truncated, such as `and 1 other`, open the person's
   profile read-only and follow LinkedIn's built-in mutual-connections link to
   resolve every connector name before finishing. Prefer an existing link from
   the page over constructing an internal LinkedIn URL.
9. Return the findings and unlock the browser.

## Browser-operation guidance

- Prefer small DOM or accessibility inspections over repeated full-page
  snapshots.
- On large LinkedIn pages, use a scoped snapshot or DOM evaluation to find list
  items whose visible text includes `mutual connection`.
- Treat a navigation as complete once the expected URL, title, and relevant DOM
  content are available; do not wait for LinkedIn's background network activity
  to become idle.
- If navigation appears stuck but the page loaded visually, inspect the current
  tab once instead of navigating again.
- Do not repeat a failing browser action without new evidence. After one retry,
  stop and report the observed state.

## Completion checklist

Before writing the final answer, verify all of the following:

- The recommended People results were inspected for named mutual connections.
- Every summary containing `and N other` was added to a resolution queue.
- Every queued person was opened and their built-in mutual-connections result
  was inspected.
- The number of verified connector names matches the mutual count shown by
  LinkedIn.
- Unnamed second-degree cards were excluded.
- The browser was unlocked.

Do not finish early or ask whether the user wants you to resolve a truncated
mutual summary. Resolving it is part of this workflow. If LinkedIn prevents
resolution after one retry, include the verified names, mark the path
incomplete, and state the exact blocker.

## Output

Use this structure:

```markdown
## [Company]

- **[Person]** — [title/headline]
  - Mutual connections: [count when known]
  - Connectors: [names that were explicitly verified]
  - Profile: [LinkedIn URL when available]

### Coverage

[State which People results were inspected and whether the result may be
incomplete.]
```

Distinguish names explicitly verified through LinkedIn's mutual-connections
results from names shown directly on a People card. Exclude paths for which no
connector name could be verified. State that the default result covers
LinkedIn's recommended People results, not every associated member.
