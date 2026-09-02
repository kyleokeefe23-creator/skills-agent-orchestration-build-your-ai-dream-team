# Project Pulse Dashboard — Implementation Plan

## Summary and goal

Build **Mona's Project Pulse** as a polished, zero-dependency static dashboard that presents project health and recent activity from a local JSON data source. The result should be easy to launch locally, accessible across input methods, responsive on small and large screens, and resilient when its data cannot be displayed.

## Architecture contract

- The application is a static app with no package manager, build step, framework, or third-party runtime dependency.
- `app/project-data.json` uses a top-level `projects` array. Every project item contains `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `app/index.html` fetches and safely renders the JSON data. It must show a friendly empty state when `projects` is empty and a friendly error state for fetch, parsing, schema, or rendering failures.
- The markup exposes `.dashboard`, `.project-grid`, and `.project-card`. Each card also uses status and priority badge classes so their semantic state can be styled consistently.
- `app/styles.css` is the single CSS owner and contains all presentation behavior.

## File ownership

| Path | Owner | Responsibility |
| --- | --- | --- |
| `app/index.html` | Coder | Semantic dashboard markup, safe JSON fetch/render logic, state messaging, and the required markup contract. |
| `app/styles.css` | Designer | All visual styling, responsive layout, accessibility-focused interaction styling, and motion behavior. |
| `app/project-data.json` | Coder | Valid sample project data conforming to the agreed schema. |
| `.vscode/launch.json` | Coder | Strict JSON VS Code launch configuration for local static serving. |

## Responsibilities

### Designer

- Design and implement all CSS in `app/styles.css`; do not alter markup, JavaScript, data, or editor configuration.
- Create a clear visual hierarchy for heading, project cards, owners, recent activity, status, and priority.
- Make the interface polished, accessible, and responsive with appropriate contrast, readable typography, layout adaptation, `border-radius`, and `box-shadow`.
- Provide visible keyboard focus indicators and honor `prefers-reduced-motion` by reducing or removing nonessential animation and transition effects.
- Style the status and priority badge classes supplied by the markup contract, including understandable state distinctions that do not rely on color alone.

### Coder

- Implement semantic, accessible HTML and the dashboard's safe client-side data-loading/rendering behavior in `app/index.html`.
- Fetch `project-data.json`, validate that the payload includes an array at `projects`, and render text through safe DOM APIs rather than interpolating untrusted content as HTML.
- Normalize or safely handle unexpected field values, map status and priority values to predictable badge classes, and present helpful loading, empty, and error states.
- Supply representative, strict JSON sample data in `app/project-data.json` using every required field.
- Create `.vscode/launch.json` as strict JSON with a launch configuration named exactly `Run Project Pulse Dashboard`. It must run `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`, and configure `serverReadyAction` to open `http://localhost:%s/index.html`.

## Ordered implementation steps

1. Confirm this architecture contract, including the schema, class names, and file ownership boundaries.
2. **[Parallel work begins]** Coder creates the HTML dashboard skeleton with `.dashboard`, `.project-grid`, `.project-card`, and status/priority badge-class conventions; creates schema-compliant sample JSON; implements safe fetch/validation/rendering and states; and adds the required VS Code launch configuration.
3. **[Parallel work begins]** Designer creates `app/styles.css` against the agreed class contract, implementing responsive grids, cards, badges, focus treatment, and reduced-motion behavior.
4. **[Sequential integration]** Combine the Coder and Designer outputs without changing ownership boundaries; confirm every class expected by the stylesheet exists in the rendered markup.
5. **[Sequential manual validation]** Run the local server through the documented launch configuration or equivalent command and verify normal, empty, and failed-data experiences in a browser.
6. Resolve integration findings only in the file owned by the responsible role, then repeat the relevant validation.

## Parallel-work decision

After the architecture and markup contract in this plan are accepted, **Designer and Coder tracks may execute in parallel**. Designer depends only on the stable class contract, while Coder owns the DOM, data, and local-server configuration. This prevents overlapping edits while shortening delivery time.

## Sequential-work decision

**Integration and manual validation occur afterward, sequentially.** They require both parallel tracks to be complete because they verify that rendered DOM classes, visual behavior, data states, and launch behavior work together. Fixes should be assigned back to the relevant owner before final revalidation.

## Dependencies

| Work item | Depends on |
| --- | --- |
| Coder implementation | The agreed schema and markup/badge-class contract. |
| Designer implementation | The agreed `.dashboard`, `.project-grid`, `.project-card`, status badge, and priority badge class contract. |
| Integration | Completed Coder and Designer tracks. |
| Manual validation | Integrated app, valid JSON data, and the local HTTP server configuration. |

## Edge cases and risks

- A page opened directly from `file://` cannot reliably fetch JSON; validation must use the HTTP server.
- Missing, malformed, or non-array `projects` payloads must fail gracefully with a friendly error state rather than an uncaught exception.
- An empty `projects` array needs a distinct, friendly empty state.
- Unknown or missing status/priority values should receive safe fallback text/classes, preserving readability and avoiding broken CSS selectors.
- Project-provided text may contain markup-like characters; render it as text, not injected HTML.
- Long project names, owner names, or activity descriptions must wrap without overflowing narrow layouts.
- Badge color alone must not communicate meaning; text labels and sufficient contrast remain necessary.
- Port 5500 may already be occupied; document or handle this during local validation without changing the required launch command.

## Validation expectations

- Confirm `app/project-data.json` is strict, parseable JSON with a top-level `projects` array and each item containing `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Confirm `.vscode/launch.json` is strict JSON; its configuration name is exactly `Run Project Pulse Dashboard`; its command is `python3 -m http.server 5500`; its working directory is `${workspaceFolder}/app`; and `serverReadyAction` opens `http://localhost:%s/index.html`.
- Start the app via the launch configuration or `python3 -m http.server 5500` from `app`, then verify `http://localhost:5500/index.html` loads and renders project cards.
- Verify no-project data displays the planned empty state, and malformed/missing/unavailable data displays the planned error state without console-breaking exceptions.
- Check desktop and narrow viewport layouts, keyboard-only navigation and visible focus, readable badge labels/contrast, and reduced-motion preference behavior.
- Confirm the implementation introduces no external dependencies, build requirement, or network requirement beyond fetching the local JSON file.

## Open questions

1. What canonical status values should be supported (for example, `on-track`, `at-risk`, and `blocked`)?
2. What canonical priority values should be supported (for example, `high`, `medium`, and `low`)?
3. Should project cards be display-only in the first release, or should selecting a card open a project detail destination later?
4. What empty-state wording best matches Mona's product voice?
5. Is port 5500 reserved for this workspace, or should local troubleshooting guidance mention stopping the conflicting process before launch?
