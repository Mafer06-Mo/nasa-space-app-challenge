<<<<<<< HEAD
# nasa-space-app-challenge
Shippies' Mission — Roblox mission design prototype for the NASA Space Apps Challenge.
=======
# Mars Mission Design Tycoon

A Roblox Studio prototype that simulates the planning and design process for a scientific space mission. Players coordinate across science, physics, spacecraft engineering, and mission resources to prepare and launch a spacecraft.

## Gameplay

The experience supports one to four players. The crew is assigned four roles, each associated with a dedicated room:

- **Physicist — Simulation Room:** Completes minigames to determine spacecraft velocity, trajectory, and orbital requirements, then shares the results with spacecraft engineering. The team researched mission-specific velocity, trajectory, and orbit requirements for the selected spacecraft.
- **Scientist — Science Lab:** Selects scientific actions and instruments suited to the mission objective, then sends the proposal and payload information to spacecraft engineering. The team researched the actions and tools needed for each objective.
- **Spacecraft Engineer — Spacecraft Station:** Uses the science and physics reports to select spacecraft components, including the engine, fuel tank and amount, hull, payload bay, and nose cone. Component information is based on real missions and was researched using NASA mission databases.
- **Resource Manager — Monitoring Room:** Coordinates the stages, tracks budget, payload mass, and fuel, reviews room reports, approves the fuel request, and launches when the mission is ready.

A single player can take on all four roles and move between rooms. The solo timer is adjusted to make that workflow manageable. After launch, the game evaluates the crew's decisions and provides feedback on mission performance.

The game is designed to help students and aspiring engineers explore how a scientific space mission is planned. It connects disciplines that are often taught separately and highlights the communication and teamwork involved in aerospace engineering.

## Prototype limitation

The current prototype has **one route: Mars → MAVEN**, set to **medium difficulty**. Other destinations, spacecraft, and difficulty settings are not part of this prototype yet.

## Built with

- Roblox Studio
- Luau

Gemini AI helped turn the team's initial room and mechanic sketches into detailed Roblox designs. Gemini AI, OpenAI Codex, and Claude assisted with writing the game code. The team researched the mission and instrument details used in the prototype.

## Repository layout

- `game/MarsMissionTycoon.rbxlx` — complete Roblox place file; open this file in Roblox Studio.
- `src/ServerScriptService/` — server scripts extracted from the place.
- `src/StarterPlayerScripts/` — client scripts extracted from the place, including the role-based navigation guide.

The `.rbxlx` place file is the complete project snapshot. The `.lua` files are provided for script-level review and editing; when changing them, apply the changes in Roblox Studio and save the place file to keep the snapshot in sync.

## Run in Roblox Studio

1. Open `game/MarsMissionTycoon.rbxlx` in Roblox Studio.
2. Use **Play** to try the experience. Use Studio's multi-client test options to explore separate crew roles with multiple players.
3. Confirm the crew roles at the central hologram and follow the room guidance to complete the mission workflow.
>>>>>>> 77bdc4a (Initial project repository)
