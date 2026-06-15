# Express AngularJS to Modern Angular Rewrite Recommendation

## Purpose

This document summarises the recommended complete rewrite approach for the Express AngularJS UI into the latest Angular framework using PrimeNG.

The recommendation is based on inspected AngularJS UI code, MVC/API controllers, DTOs, and workflow evidence. It treats the existing AngularJS implementation as behavioural evidence, not as the target architecture.

## Executive Summary

Express should be rewritten as a workflow-driven Angular application inside a future unified EUC portal shell, while keeping the Express backend separate.

The first rewrite vertical slice should be:

**Home / Imports filtering plus Details task monitoring.**

This slice starts where users start, proves the Express app shell, establishes PrimeNG table/filter/dialog patterns, introduces typed API clients and lookup services, and validates Express-specific asynchronous task monitoring without taking on the higher-risk Import creation, Results review, or DLM task-builder workflows immediately.

The first slice should include:

- Home / Imports list
- Import filtering
- Lookup loading and caching strategy
- Row selection
- Details dialog
- Task progress polling
- Navigation/action enablement based on workflow status

The first slice should not include:

- Database attach import
- Acc/Loc import
- Full Results review
- DLM profile/task-builder submission
- Portal-wide task monitoring

Database attach and Acc/Loc import have been stalled for years and are not required in the rewrite. They should only be introduced later if there is a fresh product requirement.

## Target Direction

The target UI should use:

- Latest Angular
- PrimeNG as the UI framework
- Typed Angular services for Express API clients
- Feature-based routing and structure
- Express-specific task monitoring for now
- A local Express lookup service/store for repeated lookup/static data
- A future-ready EUC portal shell, with Express as one application inside it

The Express backend remains separate. The Express task queue and task monitoring remain Express-specific and should not be promoted to a portal-wide shared capability in this rewrite.

## Workflow Understanding

| Workflow Step | Evidence | Confidence | Rewrite Implication |
|---|---|---|---|
| Home / Imports filtering | Home controller filters by class, EDM, portfolio partial name, user, date range and posts to `/api/Imports`. | High | Build this as the first page in the Express Angular app. |
| Import creation | Current active paths are Portfolio and Multiple Portfolio import. Database attach and Acc/Loc are present in legacy code but not required. | High | Rewrite only active Portfolio and Multiple Portfolio creation when the Import slice is reached. |
| Import record creation | Import ID is created when the database record is created, before tasks are created and queued. | Provided context | Import submit response should return the new `ImportId`. |
| Asynchronous Import tasks | Import submission sends a WorkflowManager message and returns after queue submission. | High | UI should treat record creation as synchronous and task execution as asynchronous. |
| Details task monitoring | Details dialog is available for all imports, regardless of state, and polls task progress by job/import context. | High | Details should be available for in-flight and terminal imports. |
| Results review | Results page reviews Data Quality, Audit, Updates, and Notes. | High | Results should be a later, separate vertical slice because of breadth and risk. |
| DLM Submission | DLM page builds submission tasks from portfolios, DLM profiles, treaties, output profiles, database selection, and grouping flags. | High | DLM submission should be a later high-risk slice with extra discovery. |
| Asynchronous DLM submission tasks | DLM submission posts a command that sends a WorkflowManager message. | High | DLM task monitoring should continue through Express-specific task monitoring. |
| Final completion/failure loop | Authoritative terminal states are `Complete` and `Errored`; other states are intermediary. | Provided context | Workflow progression should be based on terminal status. |

## Important Scope Decisions

### In Scope For Rewrite

- Home / Imports
- Portfolio import
- Multiple Portfolio import
- Details task monitoring
- Results review
- DLM profile selection and submission
- Express-specific lookup/static data strategy
- Express-specific task monitoring
- Express app integration inside a future EUC portal shell

### Out Of Scope For Rewrite

- Database attach import
- Acc/Loc import
- Portal-wide generic task monitoring
- Generic EUC queue/task framework
- Express backend consolidation with other EUC apps
- API implementation changes unless separately planned

## Recommended First Vertical Slice

### First Slice

**Home / Imports filtering plus Details task monitoring**

### Why This Slice First

This slice is the best foundation because it:

- Starts at the workflow entry point.
- Exercises real user filtering and import discovery.
- Establishes the Angular route and Express app shell.
- Establishes PrimeNG table, filter, toolbar, dialog, toast, and status-tag patterns.
- Establishes typed API clients.
- Establishes the local lookup/static data strategy.
- Validates asynchronous task monitoring without needing to submit new work.
- Avoids the complexity of Results review and DLM task-builder rules in the first delivery.

### First Slice Acceptance Criteria

- User can open the Express Home / Imports page inside the portal shell.
- User can filter imports by class, EDM, portfolio name, user, and dates.
- User can see imports in a PrimeNG table.
- User can select an import row.
- Details button is enabled for all imports, including in-flight and terminal imports.
- Results navigation is enabled according to authoritative workflow progression rules.
- Task Details dialog shows task progress for the selected import/job.
- Task progress polling is scoped to the dialog lifecycle and stops when the dialog closes.
- Lookup data is loaded through a dedicated Express lookup service.
- `Complete` and `Errored` are treated as terminal states; other states are intermediary.

## Candidate Vertical Slice Comparison

| Candidate Slice | Workflow Coverage | Benefits | Risks | Suitability |
|---|---|---|---|---|
| Home / Imports filtering + Details monitoring | Entry point, workflow visibility, async monitoring | Strong foundation, low command risk, validates core shell and task loop | Requires clear status/action rules | Good first slice |
| Import creation | Starts async Import workflow | High user value, exercises command APIs | Multiple forms, defaults, EDM/DataBridge checks, currency vintage handling | Possible second slice |
| Results review | Import output review and DLM readiness | Core workflow checkpoint | Broad surface: DQ, audit, updates, overrides, exports | Later slice |
| DLM profile selection/submission | End-to-end DLM task creation | Critical workflow value | Dense rules around profiles, treaties, output profiles, permissions | Later high-risk slice |
| Full happy path | Complete workflow | Business-complete validation | Too broad for first delivery | Too risky first |

## Recommended Rewrite Slices

The rewrite should be planned as a sequence of workflow slices. The first slice establishes the Angular and PrimeNG foundation, but it is not the whole rewrite. The later slices should be planned explicitly so the team understands the expected progression and risk profile.

### Slice 1: Home / Imports And Details Task Monitoring

**Goal:** Rebuild the workflow entry point and Express-specific task visibility.

**Includes:**

- Express route inside the future portal shell.
- Import filter panel.
- Imports table.
- Selected import actions.
- Details dialog.
- Task progress polling.
- Job database / portfolio / DLM analysis detail tables.
- Status display foundation.
- Lookup service foundation.

**Excludes:**

- New Import dialog.
- Results rewrite.
- DLM submission rewrite.

**Why first:** This proves the application shell, data access pattern, lookup strategy, status model, PrimeNG composition, and async monitoring loop before introducing command workflows.

**Primary risks:**

- Status/action enablement must match workflow rules.
- Lookup refresh should avoid repeatedly loading near-static data.
- Polling must be scoped so it does not continue after the Details dialog closes.

### Slice 2: Active Import Creation

**Goal:** Rebuild only the active import creation paths that are required for the rewrite.

**Includes:**

- Portfolio import.
- Multiple Portfolio import.
- Server, SBU, class, EDM, portfolio, priority, geocode, hazard lookup, currency scheme, currency vintage, and effective date fields.
- Client-side validation currently evidenced in the AngularJS controller.
- Submission command handling.
- Import submission response handling using returned `ImportId`.
- Refresh/select behaviour after successful submission.

**Excludes:**

- Database attach import.
- Acc/Loc import.

These excluded modes should not be ported for parity. They should be treated as future product work if the business decides to reintroduce them.

**PrimeNG composition:**

- `p-dialog` or routed creation page, depending on UX decision.
- `p-tabs` only if Portfolio and Multiple Portfolio remain in one creation surface.
- `p-select`, `p-autocomplete`, `p-checkbox`, `p-table`, `p-toast`.

**Primary risks:**

- EDMs are slightly volatile and can be added in Risk Modeler at any time.
- Currencies are updated quarterly.
- Import command response should distinguish synchronous import record creation from asynchronous task creation.
- Multiple Portfolio import has selection and job-name generation behaviour that should be preserved deliberately.

### Slice 3: Results Review

**Goal:** Rebuild the post-import review area where users inspect Data Quality, Audit, Updates, and Notes before DLM submission.

**Includes:**

- Results page shell.
- Data Quality tab.
- Audit tab.
- Failed data grid.
- Audit override and cancel override flows.
- Audit rerun flow.
- Updates tab.
- Standard updates.
- Policy currency updates.
- Location currency updates.
- Construction update.
- Occupancy update.
- Notes tab.
- Export actions.
- DLM submission navigation gating.

**PrimeNG composition:**

- `p-tabs` for Results sections.
- `p-table` for Data Quality, Audit, failed data, updates, and notes.
- `p-select` for import, data quality type, update choices, currencies, schemes, and types.
- `p-dialog` for override/update confirmations where appropriate.
- `p-confirmdialog` for destructive or workflow-changing actions.
- `p-toast` for command feedback.

**Primary risks:**

- Results is broad and should not be treated as one large component.
- Dynamic failed-data columns are generated from result shape in AngularJS and need an explicit Angular table strategy.
- Results page currently mixes result data and lookup data.
- DLM submission enablement must follow confirmed terminal/workflow rules.
- Some update flows return row counts; UX should make command result feedback clear.

**Recommended decomposition:**

- Results shell page.
- Data Quality review component.
- Audit results component.
- Failed audit data component.
- Audit override dialog.
- Standard updates component.
- Currency updates components.
- Construction/occupancy update components.
- Notes component.

### Slice 4: DLM Submission Selection And Task Builder

**Goal:** Rebuild the DLM submission workflow where users choose analyses and submit asynchronous DLM tasks.

**Includes:**

- Portfolio selection.
- DLM profile selection.
- Default selected system profiles.
- Exposed-only filtering.
- Output profile display/editing.
- Treaty selection.
- Treaty applicability visibility.
- DLM database selection/display.
- DLM task builder.
- Duplicate prevention.
- Remove all tasks.
- Remove unexposed tasks.
- Grouping flags.
- Submission modal.
- Priority, currency, and PPM import type selection.
- Standard-user restrictions.
- SuperUser capabilities.
- DLM submission command.

**Standard user behaviour:**

- Standard users can navigate to the DLM submission page.
- Standard users can submit analyses.
- Standard users can only submit profiles already selected by the system by default.
- Standard users cannot change the selected DLM profiles.
- Standard users cannot change output profiles.

**SuperUser behaviour:**

- SuperUsers can alter allowed DLM selections.
- SuperUsers can change output profiles.
- SuperUsers can perform privileged task-builder actions.

**PrimeNG composition:**

- `p-table` for portfolios, profiles, treaties, and task builder.
- `p-checkbox` for exposed-only and grouping flags.
- `p-select` for DLM database, output profile, priority, currency, and PPM import type.
- `p-dialog` for treaty editing and final submission.
- `p-confirmdialog` for removing tasks or submitting unexposed profiles.
- `p-tag` for exposed/default/status indicators.

**Primary risks:**

- This is the densest UI state in the current AngularJS implementation.
- It mixes job-specific data, near-static lookup data, permissions, and user-built task rows.
- Standard-user restrictions must align with server-side validation.
- The command payload should represent business selections, not PrimeNG table rows.
- Treaty applicability and exposed-profile rules need dedicated tests.

**Recommended pre-work:**

Before building the full UI, create and review a typed DLM task-builder state model with examples for:

- Standard user default submission.
- SuperUser changed output profile.
- Multiple portfolios.
- Treaties selected.
- Inapplicable treaties removed.
- Unexposed profiles included and removed.
- Duplicate task prevention.

### Slice 5: End-To-End Workflow Hardening

**Goal:** Validate the rewritten Angular app as one coherent Express workflow.

**Includes:**

- Home filtering.
- Import creation.
- Import task monitoring.
- Results review.
- DLM submission.
- DLM task monitoring.
- Terminal success/failure loop.
- Error handling and correlation ID display.
- Regression tests for preserved workflow behaviour.

**Primary risks:**

- Individual slices may work independently but expose gaps when chained together.
- Status refresh and navigation rules must remain consistent across Home, Results, DLM submission, and Details.
- Users need clear feedback when commands are accepted but asynchronous tasks have not yet appeared.

**Expected outcome:**

The team should be able to demonstrate the full Import to Results to DLM Submission to final task completion/failure loop in the new Angular UI, with Express still backed by its separate backend and Express-specific task monitoring.

## Workflow Status Policy

Authoritative terminal states:

- `Complete`
- `Errored`

All other states should be treated as intermediary/in-flight.

Details monitoring should not depend on terminal state. Users should be able to open Details for any import whether it is complete, errored, or still running.

Progression to later workflow stages should be controlled by the terminal status rules and any existing business readiness flags.

## Import Submission Contract Recommendation

The Import ID is created as soon as the import database record is created. This happens before tasks are created and queued.

Therefore, Import submission APIs should return the newly created `ImportId`, even if the UI initially only uses it to refresh or select the Home row.

Recommended command response shape:

```json
{
  "accepted": true,
  "importId": 12345,
  "message": "Import submitted"
}
```

If both `JobId` and `ImportId` are meaningful in the API/UI contract, both should be returned explicitly rather than overloading either term:

```json
{
  "accepted": true,
  "jobId": 456,
  "importId": 12345,
  "message": "Import submitted"
}
```

The important distinction is:

- Import record creation is synchronous.
- Task creation and execution are asynchronous.

The Angular UI can safely use the returned ID as a workflow anchor, while task monitoring still begins only once task rows exist and appear through the task progress flow.

## DLM Submission Permissions

Standard users can navigate to the DLM submission page and submit analyses, but with limited options:

- They can submit profiles already selected by the system by default.
- They cannot change the selected DLM profiles.
- They cannot change output profiles.

SuperUsers have broader DLM options:

- They can alter profile selections where allowed.
- They can change output profiles.
- They can perform privileged DLM task-builder actions currently guarded by `IsSuperUser`.

The Angular UI should enforce these restrictions in the interface, but server-side validation remains authoritative.

## Lookup And Static Data Strategy

The current AngularJS UI repeatedly loads lookup/static data as part of page bundles. The rewrite should separate live workflow data from lookup/static data.

| Data | Volatility | Current Evidence | Recommended Angular Strategy |
|---|---|---|---|
| DLM profiles | Nearly static | Loaded for DLM selection | Cache as job/server-aware lookup where appropriate; refresh on demand. |
| Output profiles | Nearly static | Loaded for DLM selection | Cache in Express lookup service; refresh on demand. |
| Update scripts | Nearly static | Loaded in Results lookup data | Cache per server; refresh on demand. |
| EDMs | Slightly volatile | Loaded for Home and Import typeaheads | Cache per Express session or feature open; provide manual refresh because RM can receive new EDMs anytime. |
| Currencies | Slightly volatile, quarterly | Used in Import and Results | Cache on feature entry; allow manual refresh. |
| Classes | Slowly changing | Home and Import filters | Cache when Express app opens. |
| Servers | Slowly changing | Home, Import, Results, DLM | Cache when Express app opens. |
| Users | Session/user dependent | Home user filter/defaults | Load for Home; cache per session with refresh. |
| Task progress | Live | Details dialog | Do not cache as static data; poll while active. |
| Import statuses | Live | Home imports table | Refresh with import list. |

## API Boundary Observations

The current UI/API boundary contains several page-bundle responses:

- `/api/HomePage`
- `/api/ImportPage`
- `/api/ResultsPage`
- `/api/ResultsLookupData`
- `/api/LoadImportSelectionData`

These responses often mix:

- Lookup data
- User defaults
- Screen-specific state
- Permissions
- Workflow-specific live data
- UI convenience values

The rewrite should not require immediate backend implementation changes, but it should define cleaner UI expectations.

### UI-Friendly Contract Direction

Prefer separate response responsibilities:

- Query responses for page/table data.
- Command responses for submitted actions.
- Lookup responses for static or slowly changing data.
- Task/status responses for live asynchronous progress.
- Capability responses or fields for permissions and allowed actions.

## API Recommendations By Area

### Query Responses

Import list query should return table-ready import rows with stable status and action metadata.

Results page queries should separate result data from lookup data where possible.

DLM selection query should distinguish:

- Job portfolios
- Available/default DLM profiles
- Exposed profile state
- Treaties and dependencies
- Output profiles
- User capabilities

### Command Responses

Import submission should return the created `ImportId`.

DLM submission should return an accepted/submitted result. If a stable workflow ID is available, return it; otherwise the UI should continue relying on refresh and task monitoring.

### Lookup Responses

Lookup responses should be small, typed, and cacheable by the Angular Express lookup service.

Examples:

- Classes
- SBUs
- Servers
- Users
- Priorities
- EDMs
- Currency vintages
- Currencies
- DLM profiles
- Output profiles
- Update scripts
- Audit override reasons
- Data quality types
- Construction and occupancy schemes

### Task/Status Responses

Task progress responses should support:

- PrimeNG table rendering
- Progress display
- Terminal state display
- Error/failure visibility
- Polling while Details is open

Task monitoring should remain within the Express app for now.

## Recommended Angular Structure

```text
src/app/
  core/
    auth/
    http/
      correlation-id.interceptor.ts
      error-toast.interceptor.ts
    configuration/
  layout/
    portal-shell/
    navigation/
  apps/
    express/
      express.routes.ts
      shell/
        express-page-shell.component.ts
      imports/
        pages/
          imports-home-page.component.ts
        components/
          import-filter-panel.component.ts
          imports-table.component.ts
          import-actions-toolbar.component.ts
          create-import-dialog.component.ts
        services/
          imports-api.service.ts
          imports-state.service.ts
        models/
      task-monitoring/
        components/
          task-details-dialog.component.ts
          task-progress-table.component.ts
          job-database-details.component.ts
        services/
          task-monitoring-api.service.ts
        models/
      results/
        pages/
        components/
        services/
        models/
      dlm-submission/
        pages/
        components/
        services/
        models/
      lookups/
        express-lookups.service.ts
        status-metadata.service.ts
        models/
  api/
    express/
      clients/
      models/
```

This structure is a recommendation, not a mandatory file map. The important design point is to organise by Express workflow features, not by old AngularJS controller files.

## PrimeNG Composition Recommendations

| Area | Suggested PrimeNG Composition |
|---|---|
| Home / Imports | `p-toolbar`, `p-table`, `p-button`, `p-tag`, `p-confirmdialog`, `p-toast` |
| Filters | `p-select`, `p-autocomplete`, `p-datepicker`, `p-inputtext`, reactive forms |
| Details dialog | `p-dialog`, `p-tabs`, `p-table`, `p-progressbar`, `p-tag` |
| Import creation | `p-dialog`, `p-tabs`, `p-select`, `p-autocomplete`, `p-table`, `p-checkbox` |
| Results review | `p-tabs`, `p-table`, `p-select`, `p-confirmdialog`, `p-toast` |
| DLM submission | `p-table`, `p-select`, `p-checkbox`, `p-dialog`, `p-toolbar`, `p-confirmdialog` |
| Status display | Shared status component using `p-tag` |
| Errors and notifications | `p-toast` plus Angular HTTP interceptors |

## Legacy Patterns To Avoid

| Legacy Pattern | Evidence | Rewrite Alternative |
|---|---|---|
| `$scope`-heavy controllers | AngularJS controllers hold broad mutable state. | Standalone Angular components with typed services and local state. |
| jQuery DOM manipulation | Datepicker setup, tab text inspection, direct checkbox disabling. | PrimeNG components and Angular template bindings. |
| UI-grid-specific configuration | Many grid definitions embedded in controllers. | PrimeNG table components with typed column definitions. |
| Repeated broad page refreshes | Home refresh reloads imports and lookup data. | Separate live polling from lookup caching. |
| Bare `Ok()` command responses | Import and DLM commands return success without stable IDs. | Return accepted result and known IDs where available. |
| Hardcoded glyph/status filters | `statusFilter`, `statusTitle`, Bootstrap glyphicons. | Shared status metadata service and PrimeNG tags. |
| Page-bundle API models | Import, Results, and DLM initializers mix responsibilities. | Separate query, lookup, command, and status models. |

## Rewrite Risks

| Risk | Impact | Mitigation |
|---|---|---|
| Results page is broad and behaviour-heavy | High regression risk | Decompose into Data Quality, Audit, Updates, and Notes subfeatures. |
| DLM task builder has dense business rules | High regression risk | Prototype and test DLM state model before full UI build. |
| Lookup and live data are mixed | Performance and stale-data risk | Introduce explicit lookup cache and refresh policy. |
| Status/action rules are implicit | Incorrect workflow progression | Centralise terminal/intermediary status handling. |
| Legacy inactive import modes may distract scope | Unnecessary rewrite effort | Exclude database attach and Acc/Loc until reintroduced. |
| Server validation and UI permissions must align | Security and UX risk | Keep server authoritative; mirror permissions in Angular capabilities. |

## Proposed Rewrite Backlog

| ID | Title | Type | Risk | Acceptance Criteria |
|---|---|---|---|---|
| RW-001 | Define Express app shell inside EUC portal route | Architecture decision | Medium | Express routes mount under portal shell; backend boundary remains Express-specific. |
| RW-002 | Model Home import list/filter contract | API contract | Medium | Typed Angular models exist for filters, rows, statuses, and allowed actions. |
| RW-003 | Build Home / Imports + Details first slice | Rewrite | Medium | User can filter imports, select a row, open Details, and monitor tasks. |
| RW-004 | Create Express lookup service | Lookup/static data strategy | Medium | Lookups have explicit load/cache/refresh policies. |
| RW-005 | Replace glyph status display with PrimeNG tags | Component extraction | Low | Shared status component maps known statuses to labels and severity. |
| RW-006 | Define terminal workflow status policy | API/UI contract | Medium | `Complete` and `Errored` are terminal; other states are intermediary. |
| RW-007 | Exclude stalled import modes from rewrite | Scope decision | Low | Database attach and Acc/Loc are not implemented unless separately reintroduced. |
| RW-008 | Return ImportId from Import submission | API contract | Medium | Import submit response returns created `ImportId`. |
| RW-009 | Prototype active Import creation forms | Rewrite | Medium | Portfolio and Multiple Portfolio import preserve validation and payload behaviour. |
| RW-010 | Decompose Results review | Discovery | High | Results tabs have documented behaviour and test cases. |
| RW-011 | Prototype DLM task-builder state model | Discovery | High | DLM selection, default/exposed rules, treaties, and permissions are testable. |
| RW-012 | Define DLM standard-user capability model | API/UI contract | High | Standard users can submit only system-default selections and cannot alter output profiles. |
| RW-013 | Define task-monitoring polling model | Rewrite | Medium | Polling is lifecycle-scoped and stops when dialog closes. |
| RW-014 | Establish PrimeNG composition baseline | PrimeNG UI composition | Low | Tables, dialogs, tabs, toast, confirm dialog, and date picker patterns are agreed. |

## Recommended Delivery Order

1. Express portal shell and route foundation.
2. Home / Imports filtering and table.
3. Details task-monitoring dialog.
4. Lookup service and status metadata foundation.
5. Active Import creation: Portfolio and Multiple Portfolio only.
6. Results review decomposition and rewrite.
7. DLM submission state-model prototype.
8. DLM submission rewrite.
9. End-to-end workflow verification.

## Testing Strategy

The rewrite should focus tests on behavioural preservation:

- Import filtering request construction.
- Lookup cache and refresh behaviour.
- Home row selection and action enablement.
- Details polling lifecycle.
- Terminal state handling for `Complete` and `Errored`.
- Import submit response handling with returned `ImportId`.
- Portfolio and Multiple Portfolio validation rules.
- Results tab filtering and command behaviours.
- DLM standard-user vs SuperUser capability differences.
- DLM task-builder duplicate prevention and treaty/profile selection rules.

## Final Recommendation

Proceed with a workflow vertical-slice rewrite, not a screen-by-screen mechanical port.

Start with **Home / Imports filtering plus Details task monitoring** because it creates the foundation for the whole Express workflow while keeping the first delivery contained.

Then add active Import creation, Results review, and DLM submission as separate workflow-aware slices.

Keep Express task monitoring inside Express for now. Keep the Express backend separate. Build the Angular UI so it can live cleanly inside a future EUC portal without expanding the scope into a generic multi-application queue or backend platform.
