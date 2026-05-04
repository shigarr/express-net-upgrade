# Express Migration – Anchor

## Current Phase
Setup / Planning

## What’s Done
- High-level migration plan defined
- Governance model defined
- Executive summary created
- Solution structure snapshot completed

## What’s Next
- Manually collect first-slice project files for low-risk core libraries
- Assess DataTransferObjects, ServiceInterfaces, DataAccessLayerInterfaces, Amlin.Logging, and Factories for SDK-style / multi-targeting suitability
- Define repeatable SDK-style / multi-targeting migration pattern
- Identify first migration slice
- Start first technical migration pattern

## Active Decisions
- Migration is framework-only (no UI/business changes)
- Independent branch strategy
- Dev effort ~10–12 weeks

## Risks
- System.Web dependencies (unknown extent)
- Auth model compatibility
- Hidden coupling across services
- First slice dependencies not yet validated
- EF version and migration strategy not yet confirmed
- Windows Service hosting pattern not yet assessed
- Direct repository inspection is unavailable due to organisational security enforcement
- Hidden dependencies may be missed unless project files, config files, and relevant snippets are supplied systematically

## Notes
- Code sharing limited → snippet-driven approach
- Codex/control-repository access is available only for migration control context, not for the secured source code repository
- Source code and project files will be provided manually by the user
- Migration assessment will follow a manual evidence-driven / snippet-driven approach
