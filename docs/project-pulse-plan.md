# Project Pulse Dashboard Implementation Plan

## Goal and scope

Build a small, polished static Project Pulse dashboard for Mona's contributors. The first view should make it easy to understand which projects are active, who owns them, their current status, recent activity, and priority or risk. The UI should use readable spacing, visible project cards, status badges, clear hierarchy, accessible markup, and a responsive layout.

The implementation is limited to:

- `app/index.html` — the dashboard document and rendering logic.
- `app/styles.css` — the visual system, layout, responsive behavior, and accessibility-related presentation.
- `app/project-data.json` — static project content with a top-level `projects` array.
- `.vscode/launch.json` — the deterministic local preview configuration.

This is a static app; no backend, build step, framework, or external data service is required. `index.html` must open the dashboard UI rather than a server directory listing.

## Ownership and file assignments

| Owner | Files | Responsibilities |
| --- | --- | --- |
| Planner | `docs/project-pulse-plan.md` | Define phases, ownership, dependencies, parallel work, edge cases, and validation gates before implementation. |
| Designer | Design guidance for `app/index.html` and `app/styles.css`; implementation edits only when explicitly delegated | Define information hierarchy, card layout, status and priority treatment, typography, spacing, color contrast, responsive behavior, keyboard/accessibility expectations, and the visual direction for a polished dashboard. Keep deterministic hooks such as `.dashboard` and `.project-card`. |
| Coder | `app/index.html`, `app/project-data.json`, `.vscode/launch.json`; `app/styles.css` when the Orchestrator assigns the implementation of the Designer's direction | Implement the static dashboard, load `project-data.json`, render visible cards, expose each required field, use accessible semantics, and create strict valid launch JSON. Keep implementation deterministic and within the assigned files. |
| Orchestrator | Coordination and review; no direct application implementation | Delegate with explicit scopes, sequence dependent work, resolve handoffs, review the integrated result, and report validation and blockers. |

No agent should modify unrelated repository files. In particular, this planning task persists only this document; application files are created in the later build phase.

## Ordered implementation phases

### 1. Confirm constraints and prepare the handoff

**Owner:** Orchestrator, with Planner output  
**Files:** `docs/project-pulse-plan.md` (already produced); no application edits.

The Orchestrator should use this plan and the repository brief as the source of truth. Confirm that the required files are exactly the three app files and `.vscode/launch.json`, that the app is static, and that the launch target must be `index.html` under `app/`. Assign every later task an explicit file scope.

### 2. Produce the UX and visual direction

**Owner:** Designer  
**Files:** Design guidance for `app/index.html` and `app/styles.css`; implementation edits only if assigned.

Define a contributor-first information hierarchy: a clear `Project Pulse` heading, a concise dashboard context, and a responsive collection of project cards. Specify how each card presents name, owner, status, recent activity, and priority/risk without relying on color alone. Define accessible labels, heading order, sufficient contrast, readable focus states, semantic structure, and behavior at narrow and wide viewport sizes.

The styling direction must be polished rather than a bare page. It should include a `.dashboard` layout and `.project-card` styling, with clear spacing, rounded corners, shadows, status badges, and responsive layout rules. The Designer should hand these decisions to the Coder before implementation of overlapping files begins.

### 3. Create deterministic data

**Owner:** Coder  
**File:** `app/project-data.json`

Create valid JSON with a top-level `projects` array and multiple representative projects. Every project object must include:

- `name`
- `owner`
- `status`
- `recentActivity`
- `priority`

Use contributor-friendly, non-empty values that exercise normal status and priority/risk presentation. Keep the shape consistent so the page can render every item without special-case assumptions.

### 4. Implement the dashboard document and styling

**Owner:** Coder, using the Designer handoff  
**Files:** `app/index.html`, `app/styles.css`

Create the dashboard UI with the exact title `Project Pulse`. Reference both `styles.css` and `project-data.json`. Render visible project cards using the `project-card` class, and show each project's name, owner, status, recent activity, and priority. Use semantic HTML, accessible text and labels, and a clear empty/error state strategy if data cannot be loaded or contains no projects.

Implement the Designer's layout in `styles.css`, including `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`. Ensure badges and priority treatments remain legible in different states, content wraps without clipping, and the layout remains usable on small screens. Prefer simple browser-native JavaScript if needed; do not introduce a framework or dependency.

This phase depends on the data contract from Phase 3 and the visual/accessibility handoff from Phase 2. Because both `index.html` and `styles.css` are part of the same visual integration, the Orchestrator should prevent separate overlapping edits to those files.

### 5. Configure the runnable preview

**Owner:** Coder  
**File:** `.vscode/launch.json`

Create strict JSON with no comments and a launch configuration named **Run Project Pulse Dashboard**. Configure it to serve from `${workspaceFolder}/app` using:

```text
python3 -m http.server 5500
```

Set the server-ready browser action to open `http://localhost:%s/index.html`, not the directory root. The launch configuration's `cwd` must be `${workspaceFolder}/app`, and the opened URL must explicitly target `index.html` so users see the dashboard instead of a directory listing.

### 6. Integrate and review

**Owner:** Orchestrator, with Designer and Coder review as needed  
**Files:** All four implementation files.

Review the integrated result against the brief and this plan. Confirm that the JSON keys match the rendering logic, every project card exposes all required information, the CSS hooks and visual affordances are present, and the launch configuration points to the same app directory and document. Resolve any issue by delegating back to the owning agent rather than making an unassigned cross-scope edit.

## Dependencies and sequencing

The data schema must be agreed before the page renderer is finalized: `app/index.html` depends on `app/project-data.json` having a top-level `projects` array and the five required fields. The page and stylesheet also depend on the Designer's hierarchy and accessibility decisions. `.vscode/launch.json` depends on the final app location and must be reviewed after the app entry point exists.

The required order is:

1. Planner establishes this plan and explicit ownership.
2. Designer defines the visual and accessibility contract.
3. Coder creates the data contract.
4. Coder implements `index.html` and `styles.css` against that contract.
5. Coder creates and validates `launch.json`.
6. Orchestrator integrates, runs checks, and performs the manual preview review.

## Parallel work decisions

After Phase 1, the Designer can work in parallel with the Coder creating `app/project-data.json`, because the Designer's guidance and the data file do not overlap. The Orchestrator should not start the page implementation until the Designer handoff and data shape are available.

Once the Coder begins `app/index.html` and `app/styles.css`, those files must be handled as a coordinated sequential task because markup hooks, data rendering, CSS selectors, and accessibility behavior are interdependent. `.vscode/launch.json` can be drafted in parallel with styling after the app path and server command are fixed, but its final review must happen after `index.html` is present. Integration and validation are sequential after all assigned files exist.

## Edge cases and risk controls

- **Directory listing instead of UI:** Always open `/index.html` in the launch URL and manually confirm the rendered dashboard, not a file index.
- **Missing or malformed data:** Keep the JSON valid; handle a failed fetch or an empty `projects` array with a visible, understandable message instead of a blank page.
- **Incomplete project objects:** Treat required fields as part of the data contract. Avoid rendering undefined labels; use explicit fallback text only where the UI design calls for it.
- **Long content:** Test long project names, owner names, activity text, and priority labels for wrapping, overflow, and card-height consistency.
- **Status and priority accessibility:** Do not communicate state through color alone; include text labels and maintain contrast and visible focus indicators.
- **Responsive layout:** Check narrow mobile-sized and wider desktop-sized viewports so cards remain readable and controls do not overlap.
- **Local file restrictions:** Fetching JSON may fail when opening `index.html` directly from `file://`; use the configured local HTTP server or VS Code launch configuration for the functional preview.
- **Port conflicts:** If port 5500 is occupied, report the conflict and use the repository's expected launch configuration rather than silently changing the documented URL.
- **Launch JSON validity:** Keep `.vscode/launch.json` strict JSON with no comments and ensure its `cwd`, command, configuration name, and server-ready URL agree.

## Validation expectations

Validation is complete only when automated structure checks and a manual functional preview both pass.

1. Run the existing repository validation script from the repository root:

   ```bash
   bash scripts/validate-exercise.sh
   ```

   This protects the exercise's existing repository and workflow contracts. A failure unrelated to the learner files should be reported explicitly rather than ignored.

2. Validate the project data and launch configuration as JSON:

   ```bash
   python3 -m json.tool app/project-data.json >/dev/null
   python3 -m json.tool .vscode/launch.json >/dev/null
   ```

   Also confirm that `app/project-data.json` has a top-level `projects` array and that every array item has `name`, `owner`, `status`, `recentActivity`, and `priority`.

3. Review the static references and deterministic hooks. Confirm that `app/index.html` contains the exact `Project Pulse` title, references `styles.css` and `project-data.json`, renders `project-card` elements, and exposes status, recent activity, and priority. Confirm that `app/styles.css` contains `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`.

4. Perform a manual browser or VS Code launch check. In VS Code Run and Debug, select **Run Project Pulse Dashboard** and start it. Confirm that the browser opens `http://localhost:5500/index.html` (or the configured `%s` server-ready equivalent), uses `${workspaceFolder}/app` as the working directory, and displays the Project Pulse dashboard rather than a directory listing. Inspect cards, status badges, priority treatment, readable spacing, keyboard focus, and a narrow viewport. Stop the preview server afterward.

5. The Orchestrator should record which checks passed, any environment limitation such as an occupied port, and any remaining risk in the final handoff. No staging, commit, or push is part of this implementation plan.
