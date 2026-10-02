# Project Pulse Implementation Plan

## Summary

Build Mona's Project Pulse as a dependency-free static dashboard that helps contributors scan active projects, ownership, status, recent activity, priority or risk, and short summaries.

The implementation will use semantic HTML, responsive CSS, and vanilla JavaScript that fetches `project-data.json`. No build system or additional dependencies are required. The launch configuration will serve `app/` over HTTP so data loading works and the browser opens `index.html` rather than a directory listing.

The brief requests contributor-friendly summaries but does not list `summary` in the minimum JSON schema. Include `summary` as an additive field alongside all required fields.

## Ownership

| Owner | Assigned files | Responsibilities |
| --- | --- | --- |
| Designer | `app/styles.css` | Define visual hierarchy, responsive layout, status and priority treatments, typography, spacing, focus states, and accessible color contrast. |
| Coder | `app/project-data.json` | Create representative project records using the agreed schema. |
| Coder | `.vscode/launch.json` | Create the deterministic VS Code preview configuration. |
| Coder | `app/index.html` | Implement semantic markup, data loading, card rendering, summary counts, and loading, empty, and error states. |
| Orchestrator | No implementation files | Enforce file scopes, pass the design contract between agents, coordinate dependencies, and validate the integrated result. |

Only the assigned owner may modify each file. Reviews by another agent are read-only; requested corrections return to the file owner.

## Data And UI Contract

Each object in the top-level `projects` array must contain non-empty string values for:

- `name`
- `owner`
- `status`
- `recentActivity`
- `priority`
- `summary`

Use a small set of predictable values, such as `Active`, `Planning`, `At risk`, and `Complete` for status, and `High`, `Medium`, and `Low` for priority. Unknown values must receive neutral styling.

The HTML and CSS contract must include:

- Exact page title and visible heading: `Project Pulse`
- Stylesheet reference: `styles.css`
- Data request/reference: `project-data.json`
- Dashboard root: `.dashboard`
- Project collection with an accessible label
- One `.project-card` article per project
- Visible name, owner, status, `recentActivity`, priority, and summary
- Status badge and distinct priority treatment
- Loading, empty, fetch-error, and JavaScript-disabled states

## Implementation Phases

### Phase 1: Establish The Shared Contract

**Owner:** Designer, coordinated by Orchestrator  
**Files modified:** None

1. Define the information hierarchy: dashboard heading and overview, project count or status summary, then project cards.
2. Confirm semantic elements and stable CSS hooks, including `.dashboard` and `.project-card`.
3. Define responsive behavior, badge variants, neutral fallback styling, focus visibility, contrast expectations, and long-content wrapping.
4. Orchestrator gives the finalized contract to Coder before implementation begins.

This phase must complete first because both HTML and CSS depend on the same class and content contract.

### Phase 2: Build Independent Foundations

These tasks can run in parallel because their file ownership does not overlap and neither output depends on the other.

**Designer task: `app/styles.css`**

1. Implement the agreed dashboard and card hooks.
2. Create a responsive card grid that collapses cleanly on narrow screens.
3. Use restrained `border-radius`, `box-shadow`, spacing, and clear typography to produce a polished dashboard.
4. Style status badges, priority indicators, metadata, loading, empty, and error states.
5. Include visible keyboard focus, sufficient contrast, overflow protection, and reduced-motion handling if motion is used.

**Coder task: `app/project-data.json` and `.vscode/launch.json`**

1. Add multiple representative project records under a top-level `projects` array.
2. Include every required field plus `summary` in every record.
3. Create `.vscode/launch.json` as strict JSON without comments.
4. Add a configuration named exactly `Run Project Pulse Dashboard`.
5. Use a `node-terminal` launch with command `python3 -m http.server 5500`.
6. Set `cwd` to `${workspaceFolder}/app`.
7. Configure `serverReadyAction` to capture the reported port and open `http://localhost:%s/index.html` externally.

### Phase 3: Implement And Integrate The Dashboard

**Owner:** Coder  
**File modified:** `app/index.html`  
**Dependencies:** Phase 1 contract, `app/styles.css`, and `app/project-data.json`

1. Add semantic document structure, responsive viewport metadata, the exact `Project Pulse` title, and the stylesheet link.
2. Add a stable dashboard shell with a loading region so the first paint is meaningful.
3. Fetch `project-data.json`, validate that `projects` is an array, and render one `.project-card` per valid record.
4. Render all data with DOM APIs and `textContent`; do not interpolate data into `innerHTML`.
5. Derive safe status and priority classes through an allowlist, with neutral fallbacks for unknown values.
6. Show useful loading, empty, malformed-data, and network-error messages in an `aria-live` region.
7. Add a `noscript` message explaining that JavaScript is required.
8. Keep the app dependency-free and avoid adding unassigned files such as `app.js`.

This work must follow Phase 2 because the HTML binds the finalized schema and CSS hooks together.

### Phase 4: Review, Correct, And Validate

Designer and Coder may perform read-only reviews in parallel:

- Designer reviews hierarchy, responsive behavior, contrast, focus states, badge clarity, and long-content handling.
- Coder reviews schema handling, safe rendering, launch behavior, console errors, and JSON validity.

Corrections may run in parallel only when each owner changes a different assigned file. Coder must not edit `app/styles.css`, and Designer must not edit the Coder-owned files. After corrections, the Orchestrator performs one sequential end-to-end validation.

## Dependencies And Ordering

- The design contract precedes all file implementation.
- `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` can be created in parallel after that contract.
- `app/index.html` depends on both the CSS hooks and JSON schema, so it is implemented afterward.
- Runtime validation depends on all four files.
- Final integration validation must run after every correction is complete.
- No agent may concurrently edit the same file.

## Edge Cases And Risks

- Opening `index.html` with a `file://` URL can block JSON fetching; use the launch configuration and HTTP server.
- Port `5500` may already be occupied. Stop the conflicting process before launch because the exercise requires the deterministic command.
- Empty or malformed `projects` data must produce a clear message instead of an empty page or uncaught exception.
- Missing fields should display a neutral fallback and be reported in the console without breaking other cards.
- Unknown status or priority values must not create unsafe or uncontrolled CSS classes.
- Long names, owner names, and activity text must wrap without overflowing cards.
- Render fetched strings with `textContent` to prevent markup injection.
- CSS-only status communication is insufficient; badges must retain visible text.
- `scripts/validate-exercise.sh` validates the exercise template itself, including the absence of tracked learner outputs, so it is not the acceptance test for the completed dashboard.

## Validation Expectations

1. Run `python3 -m json.tool app/project-data.json`.
2. Run `python3 -m json.tool .vscode/launch.json`.
3. Verify every project has `name`, `owner`, `status`, `recentActivity`, `priority`, and `summary`.
4. Confirm `app/index.html` references both `styles.css` and `project-data.json`.
5. Confirm the required `.dashboard` and `.project-card` selectors and the `border-radius` and `box-shadow` properties exist.
6. Launch `Run Project Pulse Dashboard` from VS Code.
7. Confirm the browser opens `/index.html`, not the server directory listing.
8. Confirm multiple project cards render and expose every project field visibly.
9. Check browser console and network panels for failed requests or JavaScript errors.
10. Test loading, empty, malformed-data, and fetch-error behavior.
11. Inspect desktop and narrow mobile widths for readable spacing, wrapping, and stable card layout.
12. Keyboard-check focus visibility and verify headings and live status messages with accessibility tooling.
13. Confirm the Step 3 workflow phrase and JSON checks will pass.

## Open Questions

- No production project content is supplied. Unless Mona provides real data, use clearly representative sample projects.
- Confirm whether the proposed status and priority vocabularies match Mona's reporting terminology; implementation can proceed with the defaults above.
- Confirm whether summaries should remain required long-term. This plan includes them because the product brief explicitly asks for contributor-friendly summaries.