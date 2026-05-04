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

## What’s Next
- Convert DataTransferObjects.csproj to SDK-style targeting net48 only
- Preserve AssemblyInfo.cs using GenerateAssemblyInfo=false
- Keep Newtonsoft.Json 9.0.1 as PackageReference
- Keep ApiClientLayer ProjectReference
- Remove unused EPPlus and ExcelLibrary references from DataTransferObjects
- Build DataTransferObjects and then direct dependents DataAccessLayerInterfaces and ServiceInterfaces

## Active Decisions
- Migration is framework-only (no UI/business changes)
- Independent branch strategy
- Dev effort ~10–12 weeks
- First technical migration step is SDK-style conversion of DataTransferObjects on net48, not net10.0 multi-targeting yet
- DataTransferObjects will not target net10.0 until ApiClientLayer dependency is resolved or migrated

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

## Notes
- Code sharing limited → snippet-driven approach
- Codex/control-repository access is available only for migration control context, not for the secured source code repository
- Source code and project files will be provided manually by the user
- Migration assessment will follow a manual evidence-driven / snippet-driven approach
- JobsRequestBase in DataTransferObjects.HydraJob.RiskModeler inherits from ApiClientLayer.Types.ApiClientRequest
- Recommended first SDK-style DataTransferObjects project targets net48 only with GenerateAssemblyInfo=false
