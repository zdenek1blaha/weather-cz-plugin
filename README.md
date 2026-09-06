# Weather CZ plugin

Skill-only OpenAI plugin wrapper for the existing `weather-forecast-cz` skill.

## Repository structure

- `.agents/plugins/marketplace.json` — marketplace catalog used for workspace import.
- `plugins/weather-cz/.codex-plugin/plugin.json` — plugin manifest.
- `plugins/weather-cz/skills/weather-forecast-cz/` — original skill payload.

## Import into a supported ChatGPT workspace

1. Push this repository to GitHub (public or private).
2. In ChatGPT, open **Workspace settings → Plugins → Add → Import marketplace**.
3. Set **Source** to the GitHub repository URL. Leave **Path** empty because the marketplace manifest is at the repository root under `.agents/plugins/marketplace.json`.
4. Import the marketplace.
5. Open the imported **Weather CZ** plugin and set its installation policy to **Available** or **Installed**, as appropriate.
6. In a supported ChatGPT surface, invoke the installed plugin with `@Weather CZ` or via **+ → More**, when those controls are available.

## Notes

- This is a skill-only plugin; it does not define an app or MCP server.
- The bundled skill still requires web/network access because its workflow fetches live meteorological information.
- Workspace/plugin availability depends on plan, workspace settings, role, region, and supported surface.
