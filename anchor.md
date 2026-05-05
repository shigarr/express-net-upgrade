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
- Converted ApiClientLayer to SDK-style targeting net48.
- Removed ApiClientLayer packages.config and app.config.
- Reduced ApiClientLayer dependencies to Newtonsoft.Json and Microsoft.AspNet.WebApi.Client only.
- Preserved ApiClientLayer public contract including ApiClientRequest.
- Built the full solution successfully after ApiClientLayer conversion.
- Multi-targeted ApiClientLayer to net48 and net10.0.
- Added CreateJsonContent to isolate request JSON serialization.
- Replaced direct PostAsync/PutAsync Formatter usage with CreateJsonContent.
- Kept Formatter, ReadAsAsync, MediaTypeFormatter, and JsonMediaTypeFormatter under the net48 conditional path.
- Added net10.0 Newtonsoft.Json-based request/response serialization path.
- Built ApiClientLayer successfully.
- Built the full solution successfully after ApiClientLayer multi-targeting.
- Multi-targeted ServiceInterfaces to net48 and net10.0.
- Built the full solution successfully after ApiClientLayer, DataTransferObjects, DataAccessLayerInterfaces, and ServiceInterfaces multi-targeting.
- Completed core dependency chain multi-targeting for ApiClientLayer, DataTransferObjects, DataAccessLayerInterfaces, and ServiceInterfaces.
- Converted DataAccessLayer to SDK-style targeting net48.
- Retained Dapper 1.50.2 as the only DataAccessLayer PackageReference.
- Removed unused DataAccessLayer references to EPPlus, ExcelLibrary, Newtonsoft.Json, packages.config, and app.config.
- Identified and excluded root-level orphan usp_GetDatabaseStatus.cs that was not included in the legacy project file but was picked up by SDK-style default compile globbing.
- Built the full solution successfully after DataAccessLayer SDK-style conversion.

## What’s Next
- Inspect DalConnection.cs before attempting DataAccessLayer net48/net10.0 multi-targeting.
- Plan target-specific SQL provider handling in DataAccessLayer to align with DataAccessLayerInterfaces.
- Continue watching for SDK-style default globbing pulling in files not included by legacy project files.

## Active Decisions
- Migration is framework-only (no UI/business changes)
- Independent branch strategy
- Dev effort ~10–12 weeks
- First technical migration step is SDK-style conversion of DataTransferObjects on net48, not net10.0 multi-targeting yet
- DataTransferObjects will not target net10.0 until ApiClientLayer dependency is resolved or migrated
- Unused project references may be removed when source search and successful build confirm they are not required.
- Continue SDK-style conversion on net48 before attempting net10.0 targeting.
- ApiClientLayer SDK-style conversion remains net48-only for now.
- Unused ApiClientLayer dependencies such as RestSharp, System.Text.Json, System.Web, and transitive package references are not carried forward unless build evidence requires them.
- ApiClientLayer net48 path preserves Microsoft.AspNet.WebApi.Client / JsonMediaTypeFormatter behaviour.
- ApiClientLayer net10.0 path avoids Microsoft.AspNet.WebApi.Client and uses Newtonsoft.Json directly.
- Newtonsoft.Json remains the serialization engine for ApiClientLayer to preserve behaviour.
- Do not migrate Factories until its implementation dependencies such as DataAccessLayer and Services have been assessed or migrated.
- Continue dependency-order migration from lower-level abstractions into implementation projects.
- Preserve legacy compile behaviour by excluding source files that were not included in the old project file and are only picked up by SDK-style default globbing.
- DataAccessLayer remains net48-only after SDK-style conversion; net10.0 targeting is deferred until SQL provider handling is reviewed.

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
- ApiClientLayer still depends on Microsoft.AspNet.WebApi.Client APIs, including ReadAsAsync and MediaTypeFormatter, which may block net10.0 targeting.
- Runtime smoke testing is still required for API client serialization, response handling, retry/logging handlers, and RiskModeler/Hydra request flows.
- ApiClientLayer net48 and net10.0 JSON serialization paths may differ subtly because net48 uses JsonMediaTypeFormatter while net10.0 uses direct JsonConvert serialization/deserialization.
- Runtime smoke testing is required for API client request serialization, response deserialization, retry/logging handlers, and Hydra/RiskModeler calls.
- Runtime behaviour remains unvalidated despite successful full solution builds.
- DataAccessLayer may expose higher-risk SQL, EF, configuration, or provider compatibility issues.
- Downstream implementations of IDalConnection may need target-specific SQL provider handling.
- SDK-style default compile globbing can include historical/orphaned .cs files that were not compiled by legacy project files.
- DataAccessLayer still uses System.Data.SqlClient and requires target-specific SQL provider handling before net10.0 targeting.
- Dapper/stored-procedure runtime behaviour still requires smoke testing.

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
- ApiClientLayer has no project references and now builds successfully as an SDK-style net48 library.
- ApiClientLayer should not be upgraded to net10.0 until dependency and System.Web usage are assessed.
- ApiClientRequest still uses Newtonsoft.Json.JsonIgnore and this serialization behaviour should be preserved.
- ApiClientLayer no longer blocks DataTransferObjects from attempting net48/net10.0 multi-targeting.
- The next dependency-chain target is DataTransferObjects.
- Core dependency chain now supports net48 and net10.0 at compile time.
- Next migration focus moves from interfaces/DTOs/API-client support into implementation-layer assessment.
- DbTools\usp_GetDatabaseStatus.cs remains the intended compiled file.
- Root-level usp_GetDatabaseStatus.cs is excluded because it was not referenced by the legacy project file.
