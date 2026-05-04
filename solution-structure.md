# Express Solution Structure

## Snapshot Date
2026-05-04

## Root Path
C:\gitrepo\express\WARP_Prototype\

## Target
- Current: .NET Framework 4.8
- Target: .NET 10

---

## Solutions

| Solution | Purpose (Inferred) |
|--------|------------------|
| WARP_Prototype.Master.sln | Master aggregation solution |
| WARP_Prototype.Express.sln | Core Express components |
| WARP_Prototype.GUI.sln | UI solution |
| WARP_Prototype.Manager.sln | Manager / orchestration |
| WARP_Prototype.Workers.sln | Worker services |

---

## Project Breakdown

### 🖥️ UI

| Project | Path | Notes |
|--------|------|------|
| UI | GUI/ExpressUI/UI.csproj | AngularJS + .NET Framework hosting |

---

### 🌐 APIs

| Project | Path | Notes |
|--------|------|------|
| WorkflowManager.API | WorkflowManager/WorkflowManager.API/WorkflowManager.API.csproj | Core API layer |
| Api.Express.Utility | WebUtility_IntegrationAdapter/Api.Express.Utility.csproj | Utility / integration API |

---

### 🧠 Core Libraries

| Project | Path | Notes |
|--------|------|------|
| Amlin.Logging | Amlin.Logging/Amlin.Logging.csproj | Logging abstraction |
| DataAccessLayer | DataAccessLayer/DataAccessLayer/DataAccessLayer.csproj | Data access |
| DataAccessLayerInterfaces | DataAccessLayer/DataAccessLayerInterfaces/DataAccessLayerInterfaces.csproj | DAL contracts |
| DataTransferObjects | DataTransferObjects/DataTransferObjects/DataTransferObjects.csproj | DTOs |
| Factories | Factories/Factories.csproj | Object creation |
| ServiceInterfaces | Services/ServiceInterfaces/ServiceInterfaces.csproj | Service contracts |
| Services | Services/Services/Services.csproj | Business services |
| ApiClientLayer | Solutions/ApiClientLayer/ApiClientLayer.csproj | External API client (likely RiskModeler) |
| Workers | Workers/Workers.csproj | Shared worker logic |

---

### ⚙️ Workflow Manager Domain

| Project | Path | Notes |
|--------|------|------|
| WorkflowManager.DAL | WorkflowManager/WorkflowManager.DAL/WorkflowManager.DAL.csproj | Data access |
| WorkflowManager.DAL.EF | WorkflowManager/WorkflowManager.DAL.EF/WorkflowManager.DAL.EF.csproj | EF implementation |
| WorkflowManager.Domain | WorkflowManager/WorkflowManager.Domain/WorkflowManager.Domain.csproj | Domain layer |

---

### 🔄 Windows Services (Worker Hosts)

| Project | Type | Notes |
|--------|------|------|
| ExpressManagerDaemon | Daemon | Core job orchestration |
| ExpressManagerHost | Host | Service host wrapper |
| HydraJobManagerDaemon | Daemon | RiskModeler job orchestration |
| HydraJobManagerHost | Host | Host wrapper |
| RatCatManagerDaemon | Daemon | Unknown (needs clarification) |
| RatCatManagerHost | Host | Host wrapper |
| SrisWorkerDaemon | Daemon | Unknown (needs clarification) |
| FileCopierDaemon | Daemon | File handling |
| FileCopierHost | Host | Host wrapper |
| FileDeleterHost | Host | File cleanup |

---

### 🧪 Supporting / Non-Core

| Project | Notes |
|--------|------|
| Express.ST.* | Stress testing projects |
| TestConsole | Manual testing |
| UnitTests | Unit tests (need coverage check) |

---

### 🗄️ SQL Projects

| Project | Path | Notes |
|--------|------|------|
| Express.sqlproj | SQL/ExposureProcessing | Express DB |
| WARP.sqlproj | SQL/WARP | WARP DB |

---

## Architecture Observations (Important)

- Clear layered architecture:
  - UI → API → Services → DAL → DB
- Strong use of:
  - Interfaces (good for migration)
  - DTO separation
- Windows Services handle:
  - Background orchestration
  - External integrations
- Presence of:
  - EF-based DAL (`WorkflowManager.DAL.EF`)
  - Custom API client layer (likely external API heavy)

---

## Potential Migration Hotspots (Early Flags)

| Area | Risk |
|------|------|
| System.Web usage in UI/API | 🔴 High |
| EF (DAL.EF) | 🟠 Medium |
| Windows Services (Daemon + Host pattern) | 🟠 Medium |
| ApiClientLayer (external API calls) | 🟠 Medium |
| Logging abstraction | 🟡 Low |

---

## Unknowns To Confirm

- Extent of `System.Web` usage
- Authentication model (Windows Auth?)
- HttpClient usage pattern
- Config management approach
- Actual usage of stress testing projects
- Role of RatCat / Sris workers

---
