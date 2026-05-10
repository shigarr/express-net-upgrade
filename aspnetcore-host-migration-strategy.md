# Express Migration – ASP.NET Core Host Migration Strategy

## Purpose

This document defines the strategic options for migrating the remaining classic ASP.NET/System.Web host projects:

- ExpressUI
- WorkflowManager.API

No implementation changes should be made from this document alone. It is a planning and decision artifact.

`anchor.md` remains the source of truth for current project state.

---

## Current State

### ExpressUI

Current status:

- Legacy ASP.NET Web Application
- Target framework: .NET Framework 4.8
- Uses System.Web
- Uses ASP.NET MVC and Web API controllers
- Uses OWIN startup
- Uses Castle Windsor
- Uses SignalR
- Uses Web.config, Web.config transforms, Global.asax, publish profiles
- Uses AngularJS-style client assets
- Uses Dapper / SqlClient repositories directly
- Uses EPPlus / ExcelLibrary for export paths
- Uses Amlin.Logging Web API integration
- Safe dependency cleanup completed and smoke-tested

Current decision:

- Keep legacy net48 for now
- Do not attempt SDK-style conversion in the current slice
- Do not attempt direct net10.0 migration

### WorkflowManager.API

Current status:

- Legacy ASP.NET Web API project
- Target framework: .NET Framework 4.8
- Uses System.Web
- Uses System.Web.Http / Web API controllers
- Uses OWIN startup
- Uses Web.config and Global.asax
- Uses EntityFramework / Identity-related scaffolding
- Uses WorkflowManager.Domain
- Safe dependency cleanup completed and smoke-tested

Current decision:

- Keep legacy net48 for now
- Do not attempt SDK-style conversion in the current slice
- Do not attempt direct net10.0 migration

---

## Main Migration Constraint

System.Web-based applications cannot be directly migrated to net10.0.

ExpressUI and WorkflowManager.API require a host migration strategy, not only a project-file conversion.

---

## Strategic Options

## Option 1 – Keep Legacy Hosts, Modernise Supporting Libraries

### Description

Keep ExpressUI and WorkflowManager.API on .NET Framework 4.8 while continuing to modernise supporting libraries where safe.

### What changes

- No host rewrite
- Keep existing Web.config, Global.asax, OWIN, MVC/Web API, SignalR, and DI behaviour
- Continue improving libraries behind the hosts

### Benefits

- Lowest risk
- No user-facing host change
- Preserves existing deployment model
- Allows regression hardening before larger host migration

### Risks

- ExpressUI and WorkflowManager.API remain on .NET Framework
- Full net10.0 migration remains incomplete
- System.Web dependency remains

### Recommendation

Use this as the current baseline.

---

## Option 2 – Strangler / Side-by-Side ASP.NET Core APIs

### Description

Introduce new ASP.NET Core services beside the existing System.Web hosts. Move selected endpoints or workflows gradually.

### What changes

- Existing ExpressUI and WorkflowManager.API remain operational
- New ASP.NET Core API host is created for selected endpoints
- Routing/proxying or client-side endpoint changes are introduced gradually

### Benefits

- Lower risk than big-bang rewrite
- Enables net10.0 for new API surface
- Allows endpoint-by-endpoint migration
- Easier rollback per endpoint

### Risks

- Requires dual-host deployment
- Requires routing/auth/config strategy
- May duplicate API behaviour temporarily
- Needs careful contract and endpoint parity testing

### Best candidates

- Low-risk read-only endpoints
- Internal workflow status endpoints
- Endpoints with strong smoke-test coverage
- Endpoints not tightly coupled to System.Web context

### Recommendation

Preferred long-term migration pattern.

---

## Option 3 – Full ASP.NET Core Rewrite of WorkflowManager.API

### Description

Rewrite WorkflowManager.API as an ASP.NET Core Web API.

### What changes

- Replace System.Web.Http controllers with ASP.NET Core controllers/minimal APIs
- Replace OWIN startup with ASP.NET Core hosting
- Replace Web.config runtime configuration with appsettings/IConfiguration
- Rebuild auth, DI, filters, routing, logging, and Swagger
- Keep existing WorkflowManager libraries where possible

### Benefits

- Produces a clean net10.0 API host
- Removes System.Web dependency from WorkflowManager.API
- Clarifies API hosting model

### Risks

- High implementation risk
- Auth/Identity behaviour may change
- Routing and model binding may differ
- Web.config/App.config assumptions must be replaced
- Requires extensive regression testing

### Recommendation

Do not start until endpoint inventory, auth strategy, and regression coverage are defined.

---

## Option 4 – Full ExpressUI Host Migration

### Description

Migrate ExpressUI from classic ASP.NET MVC/Web API/System.Web to an ASP.NET Core host.

### What changes

- Replace System.Web MVC/Web API hosting
- Rebuild OWIN startup as ASP.NET Core startup/hosting
- Replace Castle Windsor integration or bridge it carefully
- Replace SignalR with ASP.NET Core SignalR if required
- Rework Web.config, static assets, routing, filters, and logging integration
- Preserve AngularJS-style frontend behaviour

### Benefits

- Moves the main UI/API host toward net10.0
- Removes System.Web dependency from ExpressUI
- Enables modern hosting and deployment patterns

### Risks

- Highest risk option
- Large controller surface
- Client asset dependencies
- SignalR compatibility concerns
- Excel export paths
- Direct repository/database access paths
- Authentication/authorization differences
- High regression scope

### Recommendation

Do not start as the first ASP.NET Core migration. Consider only after WorkflowManager/API strategy is proven.

---

## Option 5 – Split UI Static Assets from API Hosting

### Description

Separate frontend/static UI assets from backend API hosting over time.

### What changes

- Keep AngularJS/static assets initially
- Serve UI assets from a separate static host or ASP.NET Core host
- Move backend API endpoints separately

### Benefits

- Separates concerns
- May reduce host migration complexity
- Allows API migration independent of frontend rewrite

### Risks

- Requires routing, CORS, auth/session, deployment, and environment config decisions
- May expose hidden coupling between UI and same-origin APIs
- Could become a larger architectural change if not controlled

### Recommendation

Potential long-term option only. Not a first step.

---

## Recommended Strategy

Recommended path:

1. Keep ExpressUI and WorkflowManager.API legacy net48 for now.
2. Start with a side-by-side ASP.NET Core API proof of concept.
3. Choose one low-risk WorkflowManager.API endpoint as a pilot.
4. Keep existing contracts and response shapes unchanged.
5. Reuse already migrated libraries where possible.
6. Compare old and new endpoint behaviour with automated or scripted checks.
7. Only expand after the pilot proves routing, config, auth, logging, and deployment approach.

---

## First Pilot Candidate Criteria

A good first endpoint should be:

- Read-only
- Low business risk
- No file upload/download
- No Excel generation
- No SignalR
- No complex authentication behaviour
- No direct System.Web context dependency
- Easy to compare old vs new response
- Covered by smoke or regression test

Avoid as first pilot:

- Login/auth endpoints
- File import/export
- Excel export
- Long-running task orchestration
- SignalR paths
- Endpoints requiring complex Web.config state
- Endpoints with direct HttpContext/session dependency

---

## Required Discovery Before Pilot

Before choosing the first endpoint, gather:

- WorkflowManager.API route list
- Controller/action inventory
- Authentication/authorization behaviour
- Web.config appSettings used by API
- Connection strings used by API/domain/DAL
- Filters/message handlers/model binders
- Dependency injection setup
- Logging setup
- Swagger/HelpPage usage
- Representative request/response samples
- Deployment model and IIS hosting assumptions

---

## Key Technical Decisions Needed

### Hosting

Decision needed:

- New ASP.NET Core service beside legacy app
- Same IIS site with reverse proxy
- Separate IIS site
- Container or Windows Service hosting later

### Routing

Decision needed:

- Preserve identical routes
- Use versioned routes
- Use proxy/routing layer

### Authentication

Decision needed:

- Preserve current auth externally
- Bridge existing auth
- Rebuild using ASP.NET Core auth

### Configuration

Decision needed:

- Map Web.config appSettings to appsettings.json / environment variables
- Preserve connection string names
- Define environment-specific config strategy

### Logging

Decision needed:

- Use Amlin.Logging core net10.0
- Add ASP.NET Core logging middleware separately
- Preserve correlation/error logging semantics

### Data Access

Decision needed:

- Use net10.0-capable core libraries only
- Avoid EF6-coupled WorkflowManager projects until EF strategy is decided
- Define temporary boundary if needed

---

## Validation Strategy

Minimum validation for any pilot endpoint:

- Build old and new hosts
- Compare status code
- Compare response body/schema
- Compare error handling
- Compare logging output
- Compare configuration behaviour
- Run smoke test against both endpoints
- Confirm rollback path

---

## Current Recommendation

Do not migrate ExpressUI or WorkflowManager.API directly.

Next practical step:

Create a WorkflowManager.API endpoint inventory and identify one low-risk read-only endpoint for a side-by-side ASP.NET Core pilot.
