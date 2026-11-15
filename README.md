# NASA Space App Challenge

## Shippies’ Mission

**Shippies’ Mission** is a Roblox Studio prototype created for the NASA Space Apps Challenge. It simulates the planning and design process behind a scientific space mission, with players coordinating across science, physics, spacecraft engineering, and mission resources.

## Gameplay

The game supports one to four players. The crew works across four roles and rooms:

- **Physicist — Simulation Room:** Completes minigames to determine spacecraft velocity, trajectory, and orbital requirements, then shares the results with the spacecraft engineer. The team researched mission-specific requirements for the spacecraft represented in the prototype.
- **Scientist — Science Lab:** Selects scientific actions and instruments that support the mission objective, then submits the proposal and payload details. The team researched the actions and tools needed for the objective.
- **Spacecraft Engineer — Spacecraft Station:** Uses the science and physics reports to select spacecraft components, including the engine, fuel tank, fuel amount, hull, payload bay, and nose cone. Mission and spacecraft information was based on NASA mission data.
- **Resource Manager — Monitoring Room:** Coordinates the stages, tracks budget, payload mass, and fuel, reviews reports, approves the fuel request, and launches once the mission is ready.

A single player can take on all four roles and move between rooms. The solo timer is adjusted to make this workflow manageable. After launch, the game evaluates decisions and provides feedback on mission performance.

## Prototype limitation

The prototype currently includes only one route: **Mars → MAVEN**, set to **medium difficulty**. Other destinations, spacecraft, and difficulty settings are not available in this version.

## Built with

- Roblox Studio
- Luau

Gemini AI helped turn the team’s initial room and mechanic sketches into detailed Roblox designs. Gemini AI, OpenAI Codex, and Claude assisted with writing the game code. The team researched the mission and instrument details represented in the game.

## Repository contents

- `game/ShippiesMission.rbxl` — the Roblox place file. Open it in Roblox Studio.
- `src/ServerScriptService/` — server scripts extracted from the place.
- `src/StarterPlayerScripts/` — client scripts extracted from the place.

The `.rbxl` file is the complete place snapshot. The `.lua` files are provided separately for script review and editing; apply any script changes in Roblox Studio and save the place file to keep both representations synchronized.

## Play the published game

[Play Shippies’ Mission on Roblox](https://www.roblox.com/games/109536135244901)

## Open the game in Roblox Studio

1. Open `game/ShippiesMission.rbxl` in Roblox Studio.
2. Press **Play** to try the experience. Use Studio’s multi-client test options to explore multiple crew roles.
3. Confirm the crew roles at the central hologram and follow the room workflow to prepare the mission.
