# Repository Guidelines

## Project Structure & Module Organization
- Core engine lives under `src/Veldrid/`. Backend bindings (Metal/OpenGL) sit in sibling folders such as `src/Veldrid.MetalBindings/`.
- Supporting libraries: `src/Veldrid.ImageSharp/`, `src/Veldrid.ImGui/`, `src/Veldrid.SDL2/`, `src/Veldrid.StartupUtilities/`, `src/Veldrid.Utilities/`.
- Samples and tests: `src/NeoDemo/` (graphics demo), `src/Veldrid.Tests/` (xUnit-based regression suite), VR samples under `src/Veldrid.VirtualReality/`.
- Documentation and planning artifacts live in `docs/` (e.g., `docs/roadmap.md`).

## Build, Test, and Development Commands
- `dotnet build src/Veldrid.sln` — compile all libraries, demos, and tests against .NET 8.
- `dotnet test src/Veldrid.Tests/Veldrid.Tests.csproj` — run automated test suite (requires graphics backends available on the host).
- `dotnet run --project src/NeoDemo/NeoDemo.csproj` — launch the NeoDemo sample to validate runtime rendering changes.

## Coding Style & Naming Conventions
- C# projects require UTF-8 BOM with CRLF line endings (enforced via `.editorconfig`).
- Use four-space indentation for C#; two-space indentation for XML project files.
- Follow existing namespace layout (`Veldrid.*`) and camelCase locals, PascalCase types/methods. Avoid implicit `var` per style rules.

## Testing Guidelines
- Tests use xUnit (`src/Veldrid.Tests`). Name test classes with `*Tests` and methods with descriptive phrases (e.g., `BufferCreation_CreatesDeviceBuffer`).
- When adding backend-specific tests, guard them with the appropriate `TEST_*` conditional constants defined in `Veldrid.Tests.csproj`.
- Run `dotnet test` before submitting changes; ensure demos still run if rendering code is touched.

## Commit & Pull Request Guidelines
- Commits should be focused and descriptive (e.g., “Upgrade projects to net8.0 and add roadmap”). Reference issue IDs when applicable (`Fixes #123`).
- Pull requests must include: summary of changes, test evidence (`dotnet test`, demo run if applicable), and mention of affected backends (Vulkan/OpenGL/Metal).
- Keep PRs rebased on `imos-dev`; squash or clean up fixups before review.

## Additional Tips
- Optional backends can be excluded via MSBuild properties (`/p:ExcludeVulkan=true`). Ensure these configurations still build when modifying project files.
- Native dependencies (SDL2, RenderDoc, VR runtimes) load through `NativeLibraryLoader`. Use the existing helper patterns when adding new libraries.
