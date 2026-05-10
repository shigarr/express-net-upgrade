# Express Migration – Status Report

## Snapshot Date
2026-05-10

## Current Migration State
The current migration slice is complete and committed. The active solution has been largely modernised at project-system level while preserving runtime behaviour. Core libraries, infrastructure logging, WorkflowManager libraries, worker libraries, Windows service/host projects, and test/support projects have been converted or cleaned up where appropriate.

The full solution builds successfully.

## Completed Migration Work

### Core Libraries
The following projects are SDK-style and multi-target `net48;net10.0`:

- ApiClientLayer
- DataTransferObjects
- DataAccessLayer
- DataAccessLayerInterfaces
- ServiceInterfaces
- Services
- Factories
- Amlin.Logging

### WorkflowManager Libraries
The following projects are SDK-style and remain `net48` due to EF6 / classic dependency constraints:

- WorkflowManager.DAL
- WorkflowManager.DAL.EF
- WorkflowManager.Domain

WorkflowManager.API was not converted to SDK-style. It remains legacy `net48` and had safe dependency cleanup only.

### Workers and Hosts
The visible worker/host projects were converted to SDK-style `net48` where appropriate. Integrated host/daemon projects were smoke-tested. Non-integrated projects were build-validated only.

### GUI / ExpressUI
GUI/ExpressUI remains legacy `net48`. Safe dependency cleanup was completed and smoke-tested. No SDK-style conversion or ASP.NET Core migration was attempted.

### Test / Support Projects
TestConsole and UnitTests were converted to SDK-style `net48`. Stress testing projects were already SDK-style and remain `net5.0` pending a separate support/test tooling decision.

### Solution Cleanup
Api.Express.Utility was removed from the active solution after confirming no active in-repository references. Stale unreferenced `packages.config` files were removed from SDK-style projects.

## Current Active Solution State

- Active C# projects: 28
- SDK-style projects: 26
- Legacy projects: 2
- Remaining legacy projects:
  - ExpressUI
  - WorkflowManager.API
- `packages.config` remains only where intentionally required by legacy projects.
- Full solution build passes.

## Intentional Exceptions

### ExpressUI
Remains legacy `net48` because it depends on classic ASP.NET/System.Web, MVC/Web API, OWIN, Castle Windsor, SignalR, Web.config, Global.asax, and client assets.

### WorkflowManager.API
Remains legacy `net48` because it depends on classic ASP.NET Web API, System.Web, OWIN, Identity/EF, Web.config, Global.asax, and Web API routing/controllers.

### Stress Testing Projects
Remain SDK-style `net5.0`. Retargeting is deferred because these are support/test tooling projects and may need separate validation.

## Key Decisions Made

- Use SDK-style conversion before framework upgrades.
- Multi-target only where dependencies and behaviour allow it.
- Keep EF6-coupled WorkflowManager projects on `net48`.
- Keep classic ASP.NET/System.Web hosts legacy `net48`.
- Remove only source-proven unused dependencies.
- Treat non-integrated daemon/host projects as build-validated only.
- Remove Api.Express.Utility from the active solution instead of migrating it.

## Known Risks

### High
- ExpressUI and WorkflowManager.API cannot move directly to `net10.0` without an ASP.NET Core/System.Web migration strategy.

### Medium
- EF6-coupled WorkflowManager projects remain `net48`.
- Some SDK-style `net48` projects are not yet assessed for `net10.0`.
- Non-integrated daemon/host projects were not runtime smoke-tested.
- Stress testing projects target out-of-support `net5.0`.

### Low
- Stale package references were cleaned from SDK-style projects and full solution build passes.

## Validation Completed

- Full solution build after each major slice.
- End-to-end smoke testing covering APIs, database access, and Windows Services.
- Amlin.Logging smoke testing.
- ExpressManager and HydraJobManager host/daemon smoke testing.
- WorkflowManager DAL/EF/Domain smoke testing where practical.
- WorkflowManager.API smoke testing after cleanup.
- GUI/ExpressUI smoke testing after cleanup.
- Build-only validation for non-integrated host/daemon projects.

## Deferred Work

- ASP.NET Core migration strategy for ExpressUI.
- ASP.NET Core migration strategy for WorkflowManager.API.
- EF6 strategy for WorkflowManager projects.
- `net10.0` feasibility review for SDK-style `net48` projects.
- Stress project retargeting from `net5.0`.
- External deployment/release verification for removed Api.Express.Utility.

## Recommended Next Phase Options

1. Regression-test the current committed slice.
2. Define an ASP.NET Core migration strategy for ExpressUI and WorkflowManager.API.
3. Review SDK-style `net48` projects for possible `net10.0` multi-targeting.
4. Decide whether to retarget stress projects from `net5.0`.
5. Review deployment/release pipelines for any assumptions about removed or cleaned projects.
