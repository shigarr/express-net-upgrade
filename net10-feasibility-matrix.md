# Express Migration – net10.0 Feasibility Matrix

## Snapshot Date
2026-05-10

## Purpose
This document records the current net10.0 feasibility status for active solution projects after the completed SDK-style migration and cleanup slice.

This is an assessment artifact. `anchor.md` remains the source of truth for current project state.

---

## Already Multi-targeted to net48 / net10.0

| Project | Status | Notes |
|---|---|---|
| ApiClientLayer | Complete | net10 path avoids Web API formatter and uses Newtonsoft.Json. |
| DataTransferObjects | Complete | Depends on ApiClientLayer, now multi-targeted. |
| DataAccessLayerInterfaces | Complete | Uses target-specific SQL client handling. |
| DataAccessLayer | Complete | Uses conditional Dapper / SQL client handling. |
| ServiceInterfaces | Complete | Clean interface layer. |
| Services | Complete | Newtonsoft.Json and Microsoft.CSharp handled. |
| Factories | Complete | Conditional System.Configuration handling. |
| Amlin.Logging | Complete | Core logging multi-targeted; Web API helpers net48-only. |
| Workers | Complete | Replaced JavaScriptSerializer with Newtonsoft.Json and removed System.Web.Extensions. |

---

## SDK-style Projects Remaining net48 Only

| Project | Feasibility | Current Decision | Reason / Blocker |
|---|---:|---|---|
| ExpressManagerHost | Medium | Keep net48 for now | Windows service/host deployment pattern; requires host/runtime validation strategy. |
| ExpressManagerDaemon | Medium | Keep net48 for now | ServiceBase, ProjectInstaller, settings, service install/start behaviour. |
| HydraJobManagerHost | Medium | Keep net48 for now | Windows host/config behaviour; runtime validation required. |
| HydraJobManagerDaemon | Medium | Keep net48 for now | ServiceBase, ProjectInstaller, logging, settings, service lifecycle. |
| FileCopierHost | Low/Medium | Keep net48 for now | Non-integrated; build-validated only, runtime unavailable. |
| FileCopierDaemon | Low/Medium | Keep net48 for now | Non-integrated Windows Service; build-validated only. |
| FileDeleterHost | Low/Medium | Keep net48 for now | Non-integrated host; build-validated only. |
| RatCatManagerHost | Low/Medium | Keep net48 for now | Non-integrated; build-validated only. |
| RatCatManagerDaemon | Low/Medium | Keep net48 for now | Non-integrated Windows Service; build-validated only. |
| WorkflowManager.DAL | Low | Keep net48 | EF6 model/type coupling through WorkflowManager.DAL.EF; Dapper/SQL runtime risk. |
| WorkflowManager.DAL.EF | Blocked | Keep net48 | EF6 model project. Requires EF strategy before net10.0. |
| WorkflowManager.Domain | Low | Keep net48 | Depends on WorkflowManager.DAL and WorkflowManager.DAL.EF; EF6-coupled chain. |
| TestConsole | Low | Keep net48 | References WorkflowManager.DAL / Domain net48-only projects and App.config runtime settings. |
| Tests | Low | Keep net48 | References WorkflowManager.API and EF/WorkflowManager net48-only projects; NUnit 2.6.4. |

---

## Legacy net48 Projects

| Project | Feasibility | Current Decision | Reason / Blocker |
|---|---:|---|---|
| ExpressUI | Blocked | Keep legacy net48 | Classic ASP.NET/System.Web, MVC/Web API, OWIN, Castle Windsor, SignalR, Web.config, Global.asax, AngularJS-style assets. Requires ASP.NET Core strategy. |
| WorkflowManager.API | Blocked | Keep legacy net48 | Classic ASP.NET Web API/System.Web, OWIN, Identity/EF, Web.config, Global.asax, controllers. Requires ASP.NET Core strategy. |

---

## Deferred Support / Stress Projects

| Project | Current Target | Current Decision | Notes |
|---|---|---|---|
| Express.ST.DataLayer | net5.0 | Deferred | Already SDK-style; retargeting requires support/test tooling decision. |
| Express.ST.DLM | net5.0 | Deferred | Already SDK-style; net5.0 is out of support. |
| Express.ST.Geocode | net5.0 | Deferred | Already SDK-style; net5.0 is out of support. |

---

## Key Decisions

- Do not move Windows Service host/daemon projects to net10.0 until a service hosting and deployment strategy is defined.
- Do not move WorkflowManager EF6-coupled projects to net10.0 until an EF6/EF Core strategy is defined.
- Do not move ExpressUI or WorkflowManager.API to net10.0 without a separate ASP.NET Core migration strategy.
- Keep test/support projects on net48 if they depend on net48-only runtime projects.
- Retarget stress projects separately from production migration work.

---

## Recommended Next Strategic Tracks

1. ASP.NET Core strategy for ExpressUI and WorkflowManager.API.
2. Windows Service hosting strategy for daemon/host projects.
3. EF strategy for WorkflowManager DAL/DAL.EF/Domain.
4. Support/test tooling strategy for stress projects currently on net5.0.
