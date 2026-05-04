# Solution Structure

## Components
- UI (AngularJS + .NET Framework hosting)
- API x3
- Class Libraries x11
- Windows Services x4 (console-based)
- SQL Projects x2

## Observations
- Heavy dependency on shared libraries
- Windows Services handle orchestration
- APIs act as integration layer

## Unknowns (to confirm)
- Data access approach (EF6 / ADO.NET?)
- Authentication model
- Use of System.Web
- External integrations (RiskModeler confirmed)
