# Express Migration – Anchor

## Current Phase
Setup / Planning

## What’s Done
- High-level migration plan defined
- Governance model defined
- Executive summary created
- Solution structure snapshot completed

## What’s Next
- Identify first low-risk core library migration slice
- Inspect dependencies for DataTransferObjects, ServiceInterfaces, DataAccessLayerInterfaces, Amlin.Logging, and Factories
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

## Notes
- Code sharing limited → snippet-driven approach
