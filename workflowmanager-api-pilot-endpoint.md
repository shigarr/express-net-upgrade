# WorkflowManager.API – ASP.NET Core Pilot Endpoint

## Purpose

This document records the first proposed low-risk WorkflowManager.API endpoint for a side-by-side ASP.NET Core migration pilot.

No implementation changes should be made from this document alone. The endpoint must first be manually verified for route shape, dependencies, configuration, authentication, runtime behaviour, and testability.

`anchor.md` remains the source of truth for current project state.

---

## Background

WorkflowManager.API is currently an intentional legacy .NET Framework 4.8 ASP.NET Web API project.

It remains legacy because it depends on classic ASP.NET/System.Web hosting concepts including:

* System.Web
* System.Web.Http / ASP.NET Web API
* OWIN startup
* Web.config
* Global.asax
* Controllers and routing
* Authentication / authorization setup
* WorkflowManager.Domain and related WorkflowManager libraries

The preferred future migration strategy is side-by-side / strangler-style ASP.NET Core migration, not a big-bang rewrite.

---

## Endpoint Inventory Summary

A static endpoint inventory was generated for WorkflowManager.API.

Summary:

* Controller files scanned: 28
* Public action-like methods found: 29
* Inventory CSV: `WorkflowManager.API.endpoint-inventory.csv`

Static analysis identified several possible read-only pilot candidates, including:

* ClassController.Get
* EventRateSchemeController.Get
* ExpressImportDetailsController.Get
* JobsController.HasDlmAnalysisTasks
* PriorityController.PriorityByJobSize
* PriorityController.PriorityByPriorityId
* RiskLinkServersController.Get
* RunningTasksController.Get
* TasksController.Get
* UserController.Get

Static analysis is not sufficient by itself. It may miss global filters, route conventions, inherited behaviour, dependency injection behaviour, authentication, authorization, and runtime configuration.

---

## Recommended Pilot Endpoint

### Endpoint

`GET /api/Jobs/{jobId}/HasDlmAnalysisTasks`

### Controller

`JobsController`

### Action

`HasDlmAnalysisTasks`

### Expected Return Type

`bool`

---

## Why This Endpoint Was Selected

This endpoint is recommended as the first provisional ASP.NET Core side-by-side pilot candidate based on static endpoint inventory.

It appears to be:

* Read-only
* Explicitly routed
* Based on a simple route parameter
* Returning a simple boolean response
* Easy to compare between legacy and pilot implementations
* Lower risk than endpoints returning large domain models
* Lower risk than endpoints involving file import/export
* Lower risk than endpoints involving Excel generation
* Lower risk than endpoints involving SignalR
* Lower risk than endpoints involving task mutation or workflow state changes

The likely route shape is:

`GET /api/Jobs/{jobId}/HasDlmAnalysisTasks`

This route and behaviour must be manually confirmed against the actual controller attributes, WebApiConfig route conventions, global filters, and runtime configuration before implementation.

---

## Candidate Alternatives

If manual inspection shows that `JobsController.HasDlmAnalysisTasks` is unsuitable, the next fallback candidates are:

1. `PriorityController.PriorityByJobSize`
2. `PriorityController.PriorityByPriorityId`
3. `EventRateSchemeController.Get`

These should be assessed using the same criteria:

* Read-only
* Simple input
* Simple response
* Minimal dependency chain
* Easy legacy/new response comparison
* No file/export/import behaviour
* No SignalR
* No complex authentication behaviour
* No direct System.Web context dependency

---

## Endpoints to Avoid as First Pilot

The following should not be first-pilot candidates without further review:

* Login/authentication endpoints
* File import endpoints
* File export endpoints
* Excel generation endpoints
* Long-running task orchestration endpoints
* Mutating POST/PUT/DELETE endpoints
* SignalR-related paths
* Endpoints requiring complex HttpContext/session behaviour
* Endpoints with broad WorkflowManager state dependencies
* Endpoints returning large or complex domain models

---

## Required Manual Verification Before Implementation

Before implementing the ASP.NET Core pilot endpoint, verify the following.

### Route Verification

Confirm:

* Exact controller route prefix
* Exact action route
* Whether route is attribute-based or convention-based
* Whether route casing matters
* Whether query string parameters are involved
* Whether route constraints exist

### Authentication and Authorization

Confirm:

* Whether the endpoint is anonymous or authenticated
* Whether global authorization filters apply
* Whether controller-level authorization applies
* Whether action-level authorization applies
* Whether user identity or claims are used indirectly

### Dependency Chain

Inspect the implementation and identify whether it depends on:

* WorkflowManager.Domain
* WorkflowManager.DAL
* WorkflowManager.DAL.EF
* Entity Framework 6
* Dapper
* ConfigurationManager / Web.config
* HttpContext / RequestContext
* Any other net48-only dependency

This is critical because WorkflowManager.Domain, WorkflowManager.DAL, and WorkflowManager.DAL.EF currently remain net48-only.

### Configuration

Confirm any required configuration values, including:

* appSettings keys
* connectionStrings
* Web.config sections
* environment-specific transform assumptions

### Runtime Behaviour

Capture legacy behaviour for representative cases:

1. A jobId that has DLM analysis tasks
2. A jobId that does not have DLM analysis tasks
3. A non-existent or invalid jobId
4. Any null/empty/malformed route value behaviour if applicable

For each case, record:

* Request URL
* Request headers if relevant
* Status code
* Response body
* Content type
* Error response shape, if any
* Logging behaviour

---

## Pilot Implementation Principles

The ASP.NET Core pilot should be side-by-side and non-disruptive.

Principles:

* Do not replace the existing WorkflowManager.API endpoint initially.
* Preserve the legacy endpoint while the pilot is developed.
* Preserve route shape where practical.
* Preserve status-code behaviour.
* Preserve response body and JSON shape.
* Preserve error behaviour where practical.
* Preserve logging behaviour where practical.
* Avoid changing existing WorkflowManager.API code during the first pilot.
* Avoid changing shared business logic unless required and explicitly approved.
* Prefer adapter/wrapper approaches over rewrites.
* Keep rollback simple.

---

## Possible Implementation Constraint

The selected endpoint may still depend on WorkflowManager.Domain or EF6-coupled projects.

If the endpoint depends on net48-only WorkflowManager libraries, then the first ASP.NET Core pilot may require one of these approaches:

1. Choose a different endpoint with a net10.0-compatible dependency chain.
2. Create a thin comparison/proxy endpoint only, not a full implementation.
3. Extract a small net10.0-compatible abstraction for the required logic.
4. Defer the pilot until the WorkflowManager EF strategy is decided.

No approach should be chosen until dependency inspection is complete.

---

## Validation Strategy

Minimum validation for the pilot:

* Build legacy WorkflowManager.API.
* Build new ASP.NET Core pilot host.
* Call the legacy endpoint and pilot endpoint with the same inputs.
* Compare status code.
* Compare response body.
* Compare response headers where relevant.
* Compare error behaviour.
* Compare logging output.
* Confirm configuration behaviour.
* Confirm rollback path.

A pilot should not be considered successful until legacy and new behaviour are equivalent for agreed test cases.

---

## Open Questions

* Does JobsController.HasDlmAnalysisTasks depend on WorkflowManager.Domain?
* Does it depend on WorkflowManager.DAL or WorkflowManager.DAL.EF?
* Does it transitively require EF6?
* Does it require Web.config connection strings?
* Does it require authenticated user context?
* Does it use HttpContext, RequestContext, or Request properties?
* What is the exact route after WebApiConfig conventions are applied?
* What are the representative jobIds for behaviour comparison?
* What should happen for an unknown jobId?
* Is there any logging or audit side effect?

---

## Current Decision

`JobsController.HasDlmAnalysisTasks` is the recommended first ASP.NET Core side-by-side pilot candidate, pending manual verification.

No implementation should begin until:

* The method implementation is inspected.
* The dependency chain is understood.
* Representative test cases are captured.
* Route/auth/config behaviour is confirmed.
* The team agrees this endpoint remains the best first pilot.
