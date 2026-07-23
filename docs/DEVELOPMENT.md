# Development

Recommended workflow:

```powershell
dotnet restore HOTASBridge.sln
dotnet build HOTASBridge.sln -c Debug
dotnet test HOTASBridge.sln -c Debug
dotnet run --project src/HOTASBridge.App/HOTASBridge.App.csproj
```

Simulation mode is available by default and should be used for UI and mapping work without hardware. Keep physical input parsing behind the common `InputEvent` model so tests and simulation stay valuable.

Before claiming a feature works, add focused unit or integration tests and update the README milestone status.
