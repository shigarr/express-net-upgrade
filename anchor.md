# Express Migration – Anchor

## Current Phase
Setup / Planning

## What’s Done
- High-level migration plan defined
- Governance model defined
- Executive summary created
- Solution structure snapshot completed
- Confirmed DataTransferObjects has a narrow but real ApiClientLayer dependency through JobsRequestBase inheriting from ApiClientRequest
- Confirmed ApiClientLayer currently targets net48 only
- Decided DataTransferObjects should first be converted to SDK-style while remaining on net48
- Confirmed DataAccessLayerInterfaces does not use EPPlus, ExcelLibrary, or Newtonsoft.Json
- Converted DataAccessLayerInterfaces to SDK-style targeting net48 only, if build completed successfully
- Converted ServiceInterfaces to SDK-style targeting net48.
- Removed unused ServiceInterfaces references to EPPlus, ExcelLibrary, Newtonsoft.Json, packages.config, and app.config.
- Removed unused ServiceInterfaces ProjectReference to DataAccessLayerInterfaces.
- ServiceInterfaces builds successfully.
- Completed SDK-style-on-net48 conversion pattern across DataTransferObjects, DataAccessLayerInterfaces, and ServiceInterfaces.

## What’s Next
- Run full solution build after ServiceInterfaces conversion if not already completed.
- Inspect ApiClientLayer.csproj because it currently blocks DataTransferObjects from net10.0 multi-targeting.
- Assess whether ApiClientLayer can be converted to SDK-style targeting net48 as the next dependency-unblocking step.

## Active Decisions
- Migration is framework-only (no UI/business changes)
- Independent branch strategy
- Dev effort ~10–12 weeks
- First technical migration step is SDK-style conversion of DataTransferObjects on net48, not net10.0 multi-targeting yet
- DataTransferObjects will not target net10.0 until ApiClientLayer dependency is resolved or migrated
- Unused project references may be removed when source search and successful build confirm they are not required.
- Continue SDK-style conversion on net48 before attempting net10.0 targeting.

## Risks
- System.Web dependencies (unknown extent)
- Auth model compatibility
- Hidden coupling across services
- First slice dependencies not yet validated
- EF version and migration strategy not yet confirmed
- Windows Service hosting pattern not yet assessed
- Direct repository inspection is unavailable due to organisational security enforcement
- Hidden dependencies may be missed unless project files, config files, and relevant snippets are supplied systematically
- DataTransferObjects cannot multi-target net10.0 while it inherits from ApiClientRequest in net48-only ApiClientLayer
- Changing JobsRequestBase inheritance would be a behavioural/design change and is not part of the minimal-change migration
- Full solution build is still required after ServiceInterfaces conversion if not already completed.
- DataTransferObjects remains blocked from net10.0 targeting by its dependency on net48-only ApiClientLayer.

## Notes
- Code sharing limited → snippet-driven approach
- Codex/control-repository access is available only for migration control context, not for the secured source code repository
- Source code and project files will be provided manually by the user
- Migration assessment will follow a manual evidence-driven / snippet-driven approach
- JobsRequestBase in DataTransferObjects.HydraJob.RiskModeler inherits from ApiClientLayer.Types.ApiClientRequest
- Recommended first SDK-style DataTransferObjects project targets net48 only with GenerateAssemblyInfo=false
- DataAccessLayerInterfaces should preserve AssemblyInfo.cs using GenerateAssemblyInfo=false
- DataAccessLayerInterfaces should keep only its DataTransferObjects ProjectReference
- ServiceInterfaces no longer references DataAccessLayerInterfaces after validation showed it was unused.
- The first repeatable migration pattern is SDK-style conversion on net48 with behaviour-preserving dependency cleanup.
