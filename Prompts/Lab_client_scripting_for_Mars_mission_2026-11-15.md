---
title: "Lab client scripting for Mars mission"
date: 2026-11-15T19:39:02.184Z
source: "https://claude.ai/chat/7cc024ac-60e9-4b57-a667-7c94890088cf"
---

# Lab client scripting for Mars mission

*Exported: 15/11/2026, 1:39:02 p.m.*
*Messages: 10*

---

## You `2026-11-15T08:39:01.662763Z`

HologramClient:-- =========================================================================
-- MAIN LAB CLIENT (holograma)
-- Location: StarterPlayer > StarterPlayerScripts > MainLabClient (LocalScript)
-- CAMBIOS: sin Phobos, y los botones de objetivo se enlazan solos (Obj_*).
-- =========================================================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local TweenService = game:GetService("TweenService")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera

local LabRemotes = ReplicatedStorage:WaitForChild("MainLabRemotes")
local CameraFocusEvent = LabRemotes:WaitForChild("SetHologramCamera")
local LeaderActionEvent = LabRemotes:WaitForChild("LeaderHologramAction")

local function bindHologramButtons()
	local lab = workspace:WaitForChild("MainLaboratoryComplex")
	local board = lab:WaitForChild("CentralHologramScreen")
	local holoGui = board:WaitForChild("HologramSurfaceGui")
	local mainFrame = holoGui:WaitForChild("Frame")

	local screen1 = mainFrame:WaitForChild("Screen1_Destination")
	local screen15 = mainFrame:WaitForChild("Screen1_5_Objectives")
	local screen2 = mainFrame:WaitForChild("Screen2_Roles")

	-- Screen 1: destinations
	for _, destName in ipairs({"MOON", "MARS", "CERES"}) do
		screen1:WaitForChild("Dest_" .. destName).MouseButton1Click:Connect(function()
			LeaderActionEvent:FireServer("SelectDestination", destName)
		end)
	end
	screen1:WaitForChild("NextStageButton").MouseButton1Click:Connect(function()
		LeaderActionEvent:FireServer("NextToObjective")
	end)

	-- Screen 1.5: objectives (cualquier boton "Obj_<Id>")
	local bound = {}
	local function bindObjective(obj)
		if bound[obj] or not obj:IsA("TextButton") then return end
		if string.sub(obj.Name, 1, 4) ~= "Obj_" then return end
		bound[obj] = true
		local objId = string.sub(obj.Name, 5)
		obj.MouseButton1Click:Connect(function()
			LeaderActionEvent:FireServer("SelectObjective", objId)
		end)
	end
	for _, child in ipairs(screen15:GetChildren()) do
		bindObjective(child)
	end
	screen15.ChildAdded:Connect(bindObjective)

	screen15:WaitForChild("BackToDestButton").MouseButton1Click:Connect(function()
		LeaderActionEvent:FireServer("BackToDestination")
	end)
	screen15:WaitForChild("NextToRolesButton").MouseButton1Click:Connect(function()
		LeaderActionEvent:FireServer("NextToRoles")
	end)

	-- Screen 2: roles
	screen2:WaitForChild("BackToObjButton").MouseButton1Click:Connect(function()
		LeaderActionEvent:FireServer("BackToObjective")
	end)
	for slotIdx = 1, 4 do
		local slotFrame = screen2:WaitForChild("PlayerSlot_" .. slotIdx)
		slotFrame:WaitForChild("RoleCycleBtn").MouseButton1Click:Connect(function()
			LeaderActionEvent:FireServer("CycleRole", slotIdx)
		end)
	end
	screen2:WaitForChild("ConfirmRolesButton").MouseButton1Click:Connect(function()
		LeaderActionEvent:FireServer("ConfirmRoles")
	end)
end

CameraFocusEvent.OnClientEvent:Connect(function(shouldZoom, anchorCFrame, lookAtPos, isLeader)
	if shouldZoom then
		camera.CameraType = Enum.CameraType.Scriptable
		local targetCFrame = CFrame.lookAt(anchorCFrame.Position, lookAtPos)
		TweenService:Create(
			camera,
			TweenInfo.new(1.1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out),
			{CFrame = targetCFrame}
		):Play()
	else
		camera.CameraType = Enum.CameraType.Custom
		local hum = localPlayer.Character and localPlayer.Character:FindFirstChild("Humanoid")
		if hum then camera.CameraSubject = hum end
	end
end)

task.spawn(bindHologramButtons)
I have these scripts for my game on roblox. I need to update sciencieLab. I need first the scientist to select the correspondent actions according to the objective. A list will be presented to the scientist of all possible actions and certain should be chosen. In this case only 2.
The list of actions:
High-resolution surface photography: Captures images sharp enough to spot boulders and ancient riverbeds to confirm where liquid water once flowed and ensure rovers will not crash upon landing.
Mineral mapping: Scans surface rocks for chemical "fingerprints" of minerals (such as clays, sulfates, and carbonates) that can only form in the presence of liquid water.
Subsurface radar sounding: Probes up to 1 km beneath the Martian crust and polar ice caps to detect hidden layers of underground water ice.
Ion escape tracking: Measures the speed and direction of charged atmospheric atoms (oxygen, hydrogen, carbon dioxide) being stripped away into space.
Solar wind & space weather monitoring: Measures streams of solar particles, solar flares, and extreme ultraviolet (EUV) radiation slamming into Mars to see how solar storms accelerate atmospheric loss.
Global ultraviolet imaging: Takes wide-angle UV scans of Mars’s upper atmosphere and surrounding hydrogen/oxygen gas clouds.
 I need to put 9 tools. Each tool should be interactivable, once you interact it should display the brief description, the cost, and the weight of the selected tool. The first time you interact it should add the all that information to the action sheet. Each tool should have an image as the "model" to be interacted with. Then after interacting with every tool and had collected their info, the scientis will have to interact with the computer to select from all 9 possible tools the ones they deemed necessary. After having selected the tools, the budget (the sum of the cost) will be send to the monitoring room to be rejected or accepted. The total weight (payload) will be send to the spacecraft station sheet. I now will provide the 9 tools and their information.


HiRISE (High Resolution Imaging Science Experiment): Telescope camera that resolves objects as small as 30 cm across  to study water-carved terrain and landing hazards. 
Weight: ~65 kg 
Cost: ~40M–45M
CRISM (Compact Reconnaissance Imaging Spectrometer for Mars): A visible/infrared spectrometer with 544 color channels that identifies water-altered minerals on the ground. 
Weight: 32.92 kg. 
Est. Cost: ~35M–40M.

SHARAD (Shallow Radar): An underground-penetrating radar using a 10-meter dipole antenna to locate buried water ice and map polar ice layers.
Weight: 15 kg. 
Est. Cost: ~20M–25M. 
IUVS (Imaging Ultraviolet Spectrograph): An ultraviolet camera/spectrometer that remotely maps upper-atmosphere gases and escaping hydrogen/oxygen coronas.
STATIC (SupraThermal and Thermal Ion Composition): Measures the composition and velocity of low-to-moderate energy ions escaping Mars's gravity.
SWIA (Solar Wind Ion Analyzer): Measures the density, speed, and temperature of incoming solar wind ions.
SWEA (Solar Wind Electron Analyzer): Measures solar wind and ionospheric electrons to trace magnetic field lines.
SEP (Solar Energetic Particle): Two sensors that detect high-energy protons and helium blasted from the Sun during solar storms.
LPW (Langmuir Probe and Waves + EUV Monitor): Two 7-meter booms that measure ionospheric electron density/temperature and electric waves, paired with an Extreme Ultraviolet (EUV) sensor tracking solar radiation.
Weight & Cost Summary: NASA integrates MAVEN’s 8 instruments as a single 65 kg science suite (ranging from ~3 kg for small particle/magnetic sensors up to ~12–15 kg each for IUVS and NGIMS). Out of MAVEN’s $366.8 million development budget, the total science instrument suite accounts for approximately 80M–110M.

**Attachments:**
- attachment (txt, 9.5 KB)
- attachment (txt, 20.3 KB)
- attachment (txt, 37.0 KB)
- attachment (txt, 19.5 KB)
- attachment (txt, 19.5 KB)
- attachment (txt, 14.4 KB)
- attachment (txt, 21.2 KB)
- attachment (txt, 32.5 KB)
- attachment (txt, 24.3 KB)
- attachment (txt, 35.1 KB)
- attachment (txt, 21.3 KB)

---

## Claude `2026-11-15T08:46:10.209309Z`

I'll write both scripts, then run a syntax check on them.

I rewrote both ScienceLab scripts: `ScienceLabManager` (Script) and `ScienceLabClient` (LocalScript). I only syntax-checked them with the Luau analyzer. I haven't run them in Studio, so test the full flow once.

### How it works now

1. **Actions sheet.** The scientist sees all 6 actions and must pick exactly 2 (`ACTIONS_REQUIRED`). Each action is tagged MRO or MAVEN, and the server rejects picks that don't match the active objective.
2. **Instrument stations.** The 9 instruments sit on the 3 side tables, 3 per table. Each one is a floating image card, and the image is the "model". Interacting shows its description, weight and cost. The first time, that data is also recorded on the Action Sheet's **TOOL DATA** tab. These stations only work after the actions are confirmed.
3. **Computer.** It stays locked until all 9 instruments have been inspected. Then the scientist toggles instruments and sees live totals for weight and cost. Submit checks that each chosen action has at least one instrument that enables it (`REQUIRE_TOOLS_FOR_ACTIONS`).
4. **Submit.**
   - `ScienceCost` goes to Monitoring, and `ScienceReportSent = true` puts Science in the leader's review queue.
   - `ScienceWeight` (kg) goes to the Spacecraft payload.
   - If the leader rejects, editing reopens. Everything resets when `MissionStarted` becomes false.

The wall screen now shows your actions, selected instruments and report status instead of the old per-tool IN PROCESS / ACCEPTED / REJECTED columns, since the leader approves the whole report from Monitoring now.

### Things to check

- **Images:** put your asset IDs in the `Image = ""` field of each entry in `TOOLS`. Until then a colored placeholder with the instrument name shows.
- **MAVEN numbers are my estimates.** You only gave a total for the whole suite (about 65 kg, $80M–110M for all 8 instruments), so I split it up. They're marked "(est.)" in the UI. Edit them in `TOOLS`.
- **Cost scale:** real $M becomes game $k at `COST_PER_MILLION = 2`, so HiRISE is $85k against the $1000k budget. Change that constant to rebalance.
- **Two instrument-to-action links are my choice:** HiRISE enables photography, CRISM mineral mapping, SHARAD radar. STATIC enables ion escape. SWIA, SWEA, SEP and LPW each enable solar wind. IUVS enables UV imaging. Edit the `Enables` lists if you see it differently.
- **Moon and Ceres:** their objectives have no actions defined, so the objective check is skipped for them. They'd need their own action and tool lists.
- **Role bug fixed:** with 1–3 players, `AssignedRole` is combined (like "Scientist + Physicist"), so the old `== "Scientist"` check always failed. It now checks `Role_Scientist`. I also set `REQUIRE_SCIENTIST_ROLE = false` for solo testing, like your other rooms.

### Changes needed in your other scripts

**SpacecraftStationManager.** Payload capacity tops out at 90 kg, but HiRISE alone is 65 kg and HiRISE plus CRISM is about 98 kg. Raise the Payload Bay values (for example 50 / 100 / 150) and the Payload bar max in `STAT_DEFS` (for example 150). Otherwise some tool combinations can never be submitted.

**MonitoringRoomManager.** In `computeEvaluation`, the science score assumed 5 actions and 4 tools. Replace it with:
```lua
local sciPts = 12 * math.clamp(actions / 2, 0, 1) + 8 * math.clamp(tools / 2, 0, 1)
```

> **Artifact: ScienceLabManager.lua** (Write the new ScienceLabManager server script)

```lua
-- =========================================================================
-- SCIENCE LAB WING ROOM (GREEN WING - ANGLE 180°) - v2
-- Location: ServerScriptService > ScienceLabManager (Script)
-- Needs LocalScript "ScienceLabClient" in StarterPlayerScripts.
--
-- WORKFLOW
--   1. ACTIONS SHEET (bench): the scientist sees all 6 possible actions and must
--      pick exactly ACTIONS_REQUIRED (2) that fit the mission objective.
--   2. TOOL STATIONS (9 instruments on the 3 side tables): each one is an
--      interactable "model" (image). Interacting shows description, weight and
--      cost. The FIRST time, that info is recorded on the Action Sheet.
--   3. COMPUTER: unlocks when the 9 instruments have been inspected. The scientist
--      picks the instruments needed and submits the report.
--   4. SUBMIT: cost -> Monitoring (ScienceCost, leader approves/rejects),
--      weight -> Spacecraft (ScienceWeight = payload in kg).
-- =========================================================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- =========================================================================
-- CONFIG
-- =========================================================================
local REQUIRE_SCIENTIST_ROLE = false -- true en la version final (false = pruebas en solitario)
local ACTIONS_REQUIRED = 2           -- cuantas acciones debe elegir el cientifico
local ENFORCE_OBJECTIVE_MATCH = true -- las acciones elegidas deben pertenecer al objetivo activo
local REQUIRE_TOOLS_FOR_ACTIONS = true -- cada accion elegida necesita al menos 1 instrumento que la haga posible
local COST_PER_MILLION = 2           -- unidades de budget ($k del juego) por cada $1M reales

-- =========================================================================
-- REMOTES (mismos nombres que antes)
-- =========================================================================
local ScienceRemotes = Instance.new("Folder")
ScienceRemotes.Name = "ScienceLabRemotes"
ScienceRemotes.Parent = ReplicatedStorage

local OpenStationEvent = Instance.new("RemoteEvent")
OpenStationEvent.Name = "OpenScienceStation"
OpenStationEvent.Parent = ScienceRemotes

local ScienceActionEvent = Instance.new("RemoteEvent")
ScienceActionEvent.Name = "ScienceLabAction"
ScienceActionEvent.Parent = ScienceRemotes

local SyncReportEvent = Instance.new("RemoteEvent")
SyncReportEvent.Name = "SyncScienceReport"
SyncReportEvent.Parent = ScienceRemotes

-- Coordenadas del Science Wing (Angle = 180°). Igual que MainLabManager: (0,500,400) + (0,0,50)
local LAB_CENTER = Vector3.new(0, 500, 450)
local WING_ANGLE = 180
local HALF_MAIN = 32
local WING_SIZE = 44
local WING_CENTER_DIST = HALF_MAIN + (WING_SIZE / 2) -- 54 studs

local ACCENT_SAGE = Color3.fromRGB(78, 158, 126)
local ACCENT_SOFT_TEXT = Color3.fromRGB(165, 225, 198)
local CABINET_BODY = Color3.fromRGB(48, 58, 66)
local CABINET_DRAWER = Color3.fromRGB(64, 76, 86)

local STATUS_COLORS = {
	DRAFT = Color3.fromRGB(190, 200, 200),
	SUBMITTED = Color3.fromRGB(255, 205, 80),
	APPROVED = Color3.fromRGB(105, 230, 150),
	REJECTED = Color3.fromRGB(240, 100, 100),
}

local ScienceModel = Instance.new("Model")
ScienceModel.Name = "ScienceLabInterior"
ScienceModel.Parent = workspace

-- El cliente usa esto para mostrar la hoja de reporte en modo pruebas
workspace:SetAttribute("ScienceOpenAccess", not REQUIRE_SCIENTIST_ROLE)

-- =========================================================================
-- 1. GEOMETRY & ROLE HELPERS
-- =========================================================================

local function getWingPos(forwardOffset, lateralOffset, height)
	local totalForward = WING_CENTER_DIST + forwardOffset
	local rad = math.rad(WING_ANGLE)
	local dirX, dirZ = math.sin(rad), math.cos(rad)
	local perpX, perpZ = math.cos(rad), -math.sin(rad)
	return Vector3.new(
		LAB_CENTER.X + dirX * totalForward + perpX * lateralOffset,
		LAB_CENTER.Y + height,
		LAB_CENTER.Z + dirZ * totalForward + perpZ * lateralOffset
	)
end

local function makePart(props)
	local p = Instance.new("Part")
	for k, v in pairs(props) do
		p[k] = v
	end
	p.Anchored = true
	p.Parent = ScienceModel
	return p
end

-- OJO: con 1-3 jugadores el AssignedRole es combinado ("Scientist + Physicist"),
-- por eso se revisa Role_Scientist (atributo booleano de MainLabManager).
local function hasScientistRole(player)
	if not REQUIRE_SCIENTIST_ROLE then return true end
	if player:GetAttribute("Role_Scientist") == true then return true end
	local role = player:GetAttribute("AssignedRole")
	return type(role) == "string" and string.find(role, "Scientist", 1, true) ~= nil
end

local function countKeys(set)
	local n = 0
	for _ in pairs(set) do
		n += 1
	end
	return n
end

-- =========================================================================
-- 2. DATA: ACTIONS & TOOLS
-- =========================================================================

-- Objective = objetivo (ActiveObjective de MainLabManager) al que pertenece la accion
local ACTIONS = {
	{Id = "SurfacePhoto", Objective = "MRO", Short = "Surface photography",
		Title = "High-resolution surface photography",
		Desc = "Captures images sharp enough to spot boulders and ancient riverbeds to confirm where liquid water once flowed and ensure rovers will not crash upon landing."},
	{Id = "MineralMap", Objective = "MRO", Short = "Mineral mapping",
		Title = "Mineral mapping",
		Desc = "Scans surface rocks for chemical \"fingerprints\" of minerals (such as clays, sulfates, and carbonates) that can only form in the presence of liquid water."},
	{Id = "RadarSounding", Objective = "MRO", Short = "Subsurface radar",
		Title = "Subsurface radar sounding",
		Desc = "Probes up to 1 km beneath the Martian crust and polar ice caps to detect hidden layers of underground water ice."},
	{Id = "IonEscape", Objective = "MAVEN", Short = "Ion escape tracking",
		Title = "Ion escape tracking",
		Desc = "Measures the speed and direction of charged atmospheric atoms (oxygen, hydrogen, carbon dioxide) being stripped away into space."},
	{Id = "SolarWind", Objective = "MAVEN", Short = "Solar wind monitoring",
		Title = "Solar wind & space weather monitoring",
		Desc = "Measures streams of solar particles, solar flares, and extreme ultraviolet (EUV) radiation slamming into Mars to see how solar storms accelerate atmospheric loss."},
	{Id = "UVImaging", Objective = "MAVEN", Short = "Global UV imaging",
		Title = "Global ultraviolet imaging",
		Desc = "Takes wide-angle UV scans of Mars's upper atmosphere and surrounding hydrogen/oxygen gas clouds."},
}

-- Weight en kg. CostMid en $M reales (punto medio del rango) -> Cost en budget ($k del juego).
-- Enables = acciones que ese instrumento hace posibles.
-- Image = "rbxassetid://ID" (la imagen que hace de "modelo"). Vacio = placeholder con el nombre.
-- Los pesos/costos de MAVEN son ESTIMACIONES (solo hay un total para toda la suite): ajustalos aqui.
local TOOLS = {
	-- MRO
	{Id = "HiRISE", Name = "HiRISE", Tag = "HiRISE", Mission = "MRO",
		FullName = "High Resolution Imaging Science Experiment",
		Description = "Telescope camera that resolves objects as small as 30 cm across to study water-carved terrain and landing hazards.",
		Weight = 65, WeightText = "~65 kg", CostText = "~$40M-45M", CostMid = 42.5,
		Enables = {"SurfacePhoto"}, Color = Color3.fromRGB(85, 165, 195), Image = ""},
	{Id = "CRISM", Name = "CRISM", Tag = "CRISM", Mission = "MRO",
		FullName = "Compact Reconnaissance Imaging Spectrometer for Mars",
		Description = "A visible/infrared spectrometer with 544 color channels that identifies water-altered minerals on the ground.",
		Weight = 32.92, WeightText = "32.92 kg", CostText = "~$35M-40M", CostMid = 37.5,
		Enables = {"MineralMap"}, Color = Color3.fromRGB(195, 145, 75), Image = ""},
	{Id = "SHARAD", Name = "SHARAD", Tag = "SHARAD", Mission = "MRO",
		FullName = "Shallow Radar",
		Description = "An underground-penetrating radar using a 10-meter dipole antenna to locate buried water ice and map polar ice layers.",
		Weight = 15, WeightText = "15 kg", CostText = "~$20M-25M", CostMid = 22.5,
		Enables = {"RadarSounding"}, Color = Color3.fromRGB(110, 155, 195), Image = ""},
	-- MAVEN
	{Id = "IUVS", Name = "IUVS", Tag = "IUVS", Mission = "MAVEN",
		FullName = "Imaging Ultraviolet Spectrograph",
		Description = "An ultraviolet camera/spectrometer that remotely maps upper-atmosphere gases and escaping hydrogen/oxygen coronas.",
		Weight = 14, WeightText = "~14 kg (est.)", CostText = "~$16M-20M (est.)", CostMid = 18,
		Enables = {"UVImaging"}, Color = Color3.fromRGB(155, 115, 185), Image = ""},
	{Id = "STATIC", Name = "STATIC", Tag = "STATIC", Mission = "MAVEN",
		FullName = "SupraThermal And Thermal Ion Composition",
		Description = "Measures the composition and velocity of low-to-moderate energy ions escaping Mars's gravity.",
		Weight = 9, WeightText = "~9 kg (est.)", CostText = "~$10M-14M (est.)", CostMid = 12,
		Enables = {"IonEscape"}, Color = Color3.fromRGB(85, 175, 125), Image = ""},
	{Id = "SWIA", Name = "SWIA", Tag = "SWIA", Mission = "MAVEN",
		FullName = "Solar Wind Ion Analyzer",
		Description = "Measures the density, speed, and temperature of incoming solar wind ions.",
		Weight = 4, WeightText = "~4 kg (est.)", CostText = "~$6M-10M (est.)", CostMid = 8,
		Enables = {"SolarWind"}, Color = Color3.fromRGB(215, 175, 85), Image = ""},
	{Id = "SWEA", Name = "SWEA", Tag = "SWEA", Mission = "MAVEN",
		FullName = "Solar Wind Electron Analyzer",
		Description = "Measures solar wind and ionospheric electrons to trace magnetic field lines.",
		Weight = 3.5, WeightText = "~3.5 kg (est.)", CostText = "~$6M-10M (est.)", CostMid = 8,
		Enables = {"SolarWind"}, Color = Color3.fromRGB(185, 105, 115), Image = ""},
	{Id = "SEP", Name = "SEP", Tag = "SEP", Mission = "MAVEN",
		FullName = "Solar Energetic Particle",
		Description = "Two sensors that detect high-energy protons and helium blasted from the Sun during solar storms.",
		Weight = 3.5, WeightText = "~3.5 kg (est.)", CostText = "~$6M-10M (est.)", CostMid = 8,
		Enables = {"SolarWind"}, Color = Color3.fromRGB(225, 130, 70), Image = ""},
	{Id = "LPW", Name = "LPW + EUV", Tag = "LPW", Mission = "MAVEN",
		FullName = "Langmuir Probe and Waves + EUV Monitor",
		Description = "Two 7-meter booms that measure ionospheric electron density/temperature and electric waves, paired with an Extreme Ultraviolet (EUV) sensor tracking solar radiation.",
		Weight = 6, WeightText = "~6 kg (est.)", CostText = "~$10M-14M (est.)", CostMid = 12,
		Enables = {"SolarWind"}, Color = Color3.fromRGB(120, 200, 215), Image = ""},
}

local ACTION_BY_ID, TOOL_BY_ID = {}, {}
for _, a in ipairs(ACTIONS) do
	ACTION_BY_ID[a.Id] = a
end
for _, t in ipairs(TOOLS) do
	t.Cost = math.floor(t.CostMid * COST_PER_MILLION + 0.5) -- $k del juego
	TOOL_BY_ID[t.Id] = t
end

local function objectiveHasActions(objectiveId)
	for _, a in ipairs(ACTIONS) do
		if a.Objective == objectiveId then return true end
	end
	return false
end

-- =========================================================================
-- 3. STATE
-- =========================================================================
local stagedActions = {}      -- [actionId] = true (acciones elegidas)
local actionsConfirmed = false
local inspectedTools = {}     -- [toolId] = true (info ya registrada en la hoja)
local selectedTools = {}      -- [toolId] = true (elegidas en la computadora)
local reportSubmitted = false -- enviado a Monitoring
local decision = nil          -- "APPROVED" / "REJECTED" / nil (decision del lider)

local function getReportStatus()
	if reportSubmitted then
		return (decision == "APPROVED") and "APPROVED" or "SUBMITTED"
	end
	return (decision == "REJECTED") and "REJECTED" or "DRAFT"
end

local function orderedIds(list, set)
	local out = {}
	for _, item in ipairs(list) do
		if set[item.Id] then
			table.insert(out, item.Id)
		end
	end
	return out
end

local function computeTotals()
	local cost, weight, count = 0, 0, 0
	for _, t in ipairs(TOOLS) do
		if selectedTools[t.Id] then
			cost += t.Cost
			weight += t.Weight
			count += 1
		end
	end
	return cost, weight, count
end

local function computerReady()
	return actionsConfirmed and countKeys(inspectedTools) >= #TOOLS
end

local function buildState()
	local cost, weight = computeTotals()
	local inspected, inspectedCount = {}, 0
	for _, t in ipairs(TOOLS) do
		if inspectedTools[t.Id] then
			inspected[t.Id] = true
			inspectedCount += 1
		end
	end
	return {
		Destination = tostring(workspace:GetAttribute("ActiveDestination") or "—"),
		ObjectiveId = tostring(workspace:GetAttribute("ActiveObjective") or ""),
		ObjectiveTitle = tostring(workspace:GetAttribute("ActiveObjectiveTitle") or "Awaiting briefing..."),
		ActionsRequired = ACTIONS_REQUIRED,
		Actions = ACTIONS,
		Tools = TOOLS,
		SelectedActions = orderedIds(ACTIONS, stagedActions),
		ActionsConfirmed = actionsConfirmed,
		Inspected = inspected,
		InspectedCount = inspectedCount,
		SelectedTools = orderedIds(TOOLS, selectedTools),
		Totals = {Cost = cost, Weight = weight},
		ReportStatus = getReportStatus(),
	}
end

local refreshWorld -- se asigna mas abajo, cuando ya existen las pantallas 3D

local function broadcast()
	local st = buildState()
	for _, plr in ipairs(Players:GetPlayers()) do
		SyncReportEvent:FireClient(plr, st)
	end
end

local function refreshAll()
	if refreshWorld then refreshWorld() end
	broadcast()
end

local function toast(player, text, isError)
	OpenStationEvent:FireClient(player, "TOAST", text, isError == true)
end

local function openFor(player, viewType)
	if not hasScientistRole(player) then
		OpenStationEvent:FireClient(player, "ACCESS_DENIED")
		return
	end
	OpenStationEvent:FireClient(player, viewType, buildState())
end

-- Science -> Monitoring (budget + envio) y Spacecraft (payload en kg)
local function publishScienceData()
	local cost, weight, count = computeTotals()
	workspace:SetAttribute("ScienceCost", cost)     -- $k
	workspace:SetAttribute("ScienceWeight", weight) -- kg
	workspace:SetAttribute("ScienceActionCount", countKeys(stagedActions))
	workspace:SetAttribute("ScienceToolCount", count)
	workspace:SetAttribute("ScienceReportSent", true) -- Monitoring: PENDING -> SUBMITTED
end

local function inspectTool(player, tool)
	if not hasScientistRole(player) then
		OpenStationEvent:FireClient(player, "ACCESS_DENIED")
		return
	end
	if not actionsConfirmed then
		toast(player, "CONFIRM YOUR MISSION ACTIONS AT THE ACTIONS SHEET FIRST", true)
		return
	end
	local first = not inspectedTools[tool.Id]
	if first then
		inspectedTools[tool.Id] = true
		refreshAll()
	end
	OpenStationEvent:FireClient(player, "TOOL_INFO", buildState(), tool.Id, first)
end

-- =========================================================================
-- 4. WALL SCREEN: SCIENCE STATUS (acciones / instrumentos / reporte)
-- =========================================================================

local wallScreenPos = getWingPos(4, -20.5, 9.2)
local wallScreenOrient = Vector3.new(0, WING_ANGLE - 90, 0)

makePart({
	Name = "ApprovalWallFrame",
	Size = Vector3.new(15.6, 8.6, 0.35),
	Position = getWingPos(4, -20.68, 9.2),
	Orientation = wallScreenOrient,
	Material = Enum.Material.SmoothPlastic,
	Color = ACCENT_SAGE
})

local approvalBoard = makePart({
	Name = "ResourceApprovalScreen",
	Size = Vector3.new(15.0, 8.0, 0.4),
	Position = wallScreenPos,
	Orientation = wallScreenOrient,
	Material = Enum.Material.SmoothPlastic,
	Color = Color3.fromRGB(22, 32, 36)
})

local wallGui = Instance.new("SurfaceGui")
wallGui.Name = "ApprovalStatusGui"
wallGui.Face = Enum.NormalId.Front
wallGui.SizingMode = Enum.SurfaceGuiSizingMode.PixelsPerStud
wallGui.PixelsPerStud = 55
wallGui.LightInfluence = 0.25
wallGui.Parent = approvalBoard

local wallBg = Instance.new("Frame")
wallBg.Size = UDim2.new(1, 0, 1, 0)
wallBg.BackgroundColor3 = Color3.fromRGB(24, 34, 38)
wallBg.Parent = wallGui

local wallTitle = Instance.new("TextLabel")
wallTitle.Size = UDim2.new(1, -24, 0.14, 0)
wallTitle.Position = UDim2.new(0, 12, 0.02, 0)
wallTitle.BackgroundTransparency = 1
wallTitle.Text = "SCIENCE LAB — MISSION DATA STATUS"
wallTitle.TextColor3 = ACCENT_SOFT_TEXT
wallTitle.Font = Enum.Font.GothamBlack
wallTitle.TextScaled = true
wallTitle.Parent = wallBg

local columnConfigs = {
	{Key = "Actions", Title = "MISSION ACTIONS", Color = Color3.fromRGB(105, 205, 145), Bg = Color3.fromRGB(28, 48, 38)},
	{Key = "Tools", Title = "INSTRUMENTS", Color = Color3.fromRGB(215, 175, 85), Bg = Color3.fromRGB(48, 44, 30)},
	{Key = "Report", Title = "REPORT TO LEADER", Color = Color3.fromRGB(110, 175, 215), Bg = Color3.fromRGB(26, 38, 52)},
}

local wallLabels = {}

for idx, col in ipairs(columnConfigs) do
	local colFrame = Instance.new("Frame")
	colFrame.Size = UDim2.new(0.31, 0, 0.78, 0)
	colFrame.Position = UDim2.new(0.02 + (idx - 1) * 0.33, 0, 0.18, 0)
	colFrame.BackgroundColor3 = col.Bg
	colFrame.Parent = wallBg

	local cCorner = Instance.new("UICorner")
	cCorner.CornerRadius = UDim.new(0, 8)
	cCorner.Parent = colFrame

	local cHeader = Instance.new("TextLabel")
	cHeader.Size = UDim2.new(1, -10, 0.14, 0)
	cHeader.Position = UDim2.new(0, 5, 0.03, 0)
	cHeader.BackgroundTransparency = 1
	cHeader.Text = col.Title
	cHeader.TextColor3 = col.Color
	cHeader.Font = Enum.Font.GothamBlack
	cHeader.TextScaled = true
	cHeader.Parent = colFrame

	local listLbl = Instance.new("TextLabel")
	listLbl.Size = UDim2.new(1, -14, 0.78, 0)
	listLbl.Position = UDim2.new(0, 7, 0.19, 0)
	listLbl.BackgroundTransparency = 1
	listLbl.Text = "—"
	listLbl.TextColor3 = Color3.fromRGB(225, 235, 232)
	listLbl.Font = Enum.Font.GothamSemibold
	listLbl.TextScaled = true
	listLbl.TextWrapped = true
	listLbl.TextXAlignment = Enum.TextXAlignment.Left
	listLbl.TextYAlignment = Enum.TextYAlignment.Top
	listLbl.Parent = colFrame

	local limit = Instance.new("UITextSizeConstraint")
	limit.MaxTextSize = 24
	limit.MinTextSize = 8
	limit.Parent = listLbl

	wallLabels[col.Key] = listLbl
end

local function refreshWallScreen()
	-- Acciones
	local actionLines = {}
	for _, a in ipairs(ACTIONS) do
		if stagedActions[a.Id] then
			table.insert(actionLines, (actionsConfirmed and "✓ " or "• ") .. a.Short)
		end
	end
	if #actionLines == 0 then
		actionLines = {"None selected yet", "(pick " .. ACTIONS_REQUIRED .. ")"}
	end
	wallLabels.Actions.Text = table.concat(actionLines, "\n")

	-- Instrumentos
	local toolLines = {string.format("DATA: %d/%d inspected", countKeys(inspectedTools), #TOOLS)}
	local cost, weight, count = computeTotals()
	if count == 0 then
		table.insert(toolLines, "No instruments chosen")
	else
		for _, t in ipairs(TOOLS) do
			if selectedTools[t.Id] then
				table.insert(toolLines, "• " .. t.Name)
			end
		end
	end
	wallLabels.Tools.Text = table.concat(toolLines, "\n")

	-- Reporte
	local status = getReportStatus()
	wallLabels.Report.Text = string.format("%s\n\nCOST: $%dk\nWEIGHT: %.1f kg", status, cost, weight)
	wallLabels.Report.TextColor3 = STATUS_COLORS[status] or Color3.fromRGB(225, 235, 232)
end

-- =========================================================================
-- 5. MAIN WORKBENCH + COMPUTER + ACTIONS SHEET
-- =========================================================================

local benchForward = 19.2
local benchWidth = 41.5
local benchHeight = 3.4
local benchDepth = 4.2

makePart({
	Name = "MainLabWorkbench",
	Size = Vector3.new(benchWidth, benchHeight, benchDepth),
	Position = getWingPos(benchForward, 0, benchHeight / 2),
	Orientation = Vector3.new(0, WING_ANGLE, 0),
	Material = Enum.Material.Metal,
	Color = CABINET_BODY
})

makePart({
	Name = "WorkbenchTrim",
	Size = Vector3.new(benchWidth + 0.2, 0.2, benchDepth + 0.2),
	Position = getWingPos(benchForward, 0, benchHeight + 0.05),
	Orientation = Vector3.new(0, WING_ANGLE, 0),
	Material = Enum.Material.SmoothPlastic,
	Color = ACCENT_SAGE
})

for d = 1, 6 do
	local lat = -17.2 + (d - 1) * 6.88
	makePart({
		Name = "CabinetDrawer_" .. d,
		Size = Vector3.new(6.2, 2.4, 0.2),
		Position = getWingPos(benchForward - (benchDepth / 2 + 0.08), lat, 1.6),
		Orientation = Vector3.new(0, WING_ANGLE, 0),
		Material = Enum.Material.SmoothPlastic,
		Color = CABINET_DRAWER
	})
end

local compLat = -13.5
makePart({
	Name = "ComputerBase",
	Size = Vector3.new(4.2, 0.5, 3.0),
	Position = getWingPos(benchForward, compLat, benchHeight + 0.35),
	Orientation = Vector3.new(0, WING_ANGLE, 0),
	Material = Enum.Material.SmoothPlastic,
	Color = Color3.fromRGB(58, 68, 78)
})

local monitor = makePart({
	Name = "ScientistComputerMonitor",
	Size = Vector3.new(4.0, 2.8, 0.45),
	Position = getWingPos(benchForward + 0.4, compLat, benchHeight + 2.0),
	Orientation = Vector3.new(-10, WING_ANGLE, 0),
	Material = Enum.Material.SmoothPlastic,
	Color = Color3.fromRGB(34, 42, 50)
})

local screenSurface = Instance.new("SurfaceGui")
screenSurface.Face = Enum.NormalId.Front
screenSurface.LightInfluence = 0.3
screenSurface.Parent = monitor

local screenBg = Instance.new("Frame")
screenBg.Size = UDim2.new(1, 0, 1, 0)
screenBg.BackgroundColor3 = Color3.fromRGB(22, 38, 36)
screenBg.Parent = screenSurface

local screenTxt = Instance.new("TextLabel")
screenTxt.Size = UDim2.new(0.9, 0, 0.8, 0)
screenTxt.Position = UDim2.new(0.05, 0, 0.1, 0)
screenTxt.BackgroundTransparency = 1
screenTxt.Text = "SCIENTIST COMPUTER\n[LOCKED]"
screenTxt.TextColor3 = ACCENT_SOFT_TEXT
screenTxt.Font = Enum.Font.GothamBold
screenTxt.TextScaled = true
screenTxt.Parent = screenBg

local compClick = Instance.new("ClickDetector")
compClick.MaxActivationDistance = 14
compClick.Parent = monitor

local compPrompt = Instance.new("ProximityPrompt")
compPrompt.ObjectText = "Scientist Computer"
compPrompt.ActionText = "Choose Instruments"
compPrompt.HoldDuration = 0
compPrompt.MaxActivationDistance = 12
compPrompt.Parent = monitor

local function triggerComputer(player)
	openFor(player, "COMPUTER")
end

compClick.MouseClick:Connect(triggerComputer)
compPrompt.Triggered:Connect(triggerComputer)

local sheetLat = 8.5
local actionSheet = makePart({
	Name = "ActionsProtocolSheet",
	Size = Vector3.new(2.6, 0.12, 3.2),
	Position = getWingPos(benchForward - 0.3, sheetLat, benchHeight + 0.18),
	Orientation = Vector3.new(0, WING_ANGLE + 8, 0),
	Material = Enum.Material.SmoothPlastic,
	Color = Color3.fromRGB(218, 226, 222)
})

local sheetGui = Instance.new("SurfaceGui")
sheetGui.Face = Enum.NormalId.Top
sheetGui.LightInfluence = 0.3
sheetGui.Parent = actionSheet

local sheetLabel = Instance.new("TextLabel")
sheetLabel.Size = UDim2.new(0.9, 0, 0.9, 0)
sheetLabel.Position = UDim2.new(0.05, 0, 0.05, 0)
sheetLabel.BackgroundTransparency = 1
sheetLabel.Text = "MISSION\nACTIONS\nSHEET"
sheetLabel.TextColor3 = Color3.fromRGB(28, 52, 42)
sheetLabel.Font = Enum.Font.GothamBlack
sheetLabel.TextScaled = true
sheetLabel.Parent = sheetGui

local sheetClick = Instance.new("ClickDetector")
sheetClick.MaxActivationDistance = 14
sheetClick.Parent = actionSheet

local sheetPrompt = Instance.new("ProximityPrompt")
sheetPrompt.ObjectText = "Actions Sheet"
sheetPrompt.ActionText = "Select Actions & View Tool Data"
sheetPrompt.HoldDuration = 0
sheetPrompt.MaxActivationDistance = 12
sheetPrompt.Parent = actionSheet

local function triggerSheet(player)
	openFor(player, "ACTIONS_SHEET")
end

sheetClick.MouseClick:Connect(triggerSheet)
sheetPrompt.Triggered:Connect(triggerSheet)

-- =========================================================================
-- 6. THE 9 INSTRUMENT STATIONS (imagen como "modelo" + interaccion)
-- =========================================================================

local toolDisplays = {} -- [toolId] = {Badge = TextLabel, Stroke = UIStroke}

local function createToolDisplay(tool, worldPos)
	-- Base circular
	makePart({
		Name = "ToolRing_" .. tool.Id,
		Size = Vector3.new(0.15, 2.5, 2.5),
		Position = worldPos,
		Shape = Enum.PartType.Cylinder,
		Orientation = Vector3.new(0, 0, 90),
		Material = Enum.Material.SmoothPlastic,
		Color = tool.Color
	})
	makePart({
		Name = "ToolPad_" .. tool.Id,
		Size = Vector3.new(0.2, 2.0, 2.0),
		Position = worldPos + Vector3.new(0, 0.03, 0),
		Shape = Enum.PartType.Cylinder,
		Orientation = Vector3.new(0, 0, 90),
		Material = Enum.Material.SmoothPlastic,
		Color = Color3.fromRGB(34, 44, 52)
	})

	-- "Modelo": parte invisible con una tarjeta-imagen flotante que mira a la camara
	local holder = makePart({
		Name = "ToolModel_" .. tool.Id,
		Size = Vector3.new(3, 3, 0.3),
		Position = worldPos + Vector3.new(0, 2.4, 0),
		Transparency = 1,
		CanCollide = false
	})

	local bb = Instance.new("BillboardGui")
	bb.Name = "ToolCard"
	bb.Size = UDim2.new(3.3, 0, 3.3, 0) -- escala = studs
	bb.LightInfluence = 0
	bb.MaxDistance = 90
	bb.Adornee = holder
	bb.Parent = holder

	local card = Instance.new("Frame")
	card.Size = UDim2.new(1, 0, 1, 0)
	card.BackgroundColor3 = Color3.fromRGB(20, 32, 36)
	card.BackgroundTransparency = 0.08
	card.Parent = bb

	local cardCorner = Instance.new("UICorner")
	cardCorner.CornerRadius = UDim.new(0.08, 0)
	cardCorner.Parent = card

	local cardStroke = Instance.new("UIStroke")
	cardStroke.Color = tool.Color
	cardStroke.Thickness = 3
	cardStroke.Parent = card

	local imgArea = Instance.new("Frame")
	imgArea.Size = UDim2.new(0.86, 0, 0.56, 0)
	imgArea.Position = UDim2.new(0.07, 0, 0.05, 0)
	imgArea.BackgroundColor3 = tool.Color:Lerp(Color3.new(0, 0, 0), 0.72)
	imgArea.BorderSizePixel = 0
	imgArea.Parent = card

	local imgCorner = Instance.new("UICorner")
	imgCorner.CornerRadius = UDim.new(0.08, 0)
	imgCorner.Parent = imgArea

	if tool.Image ~= "" then
		local img = Instance.new("ImageLabel")
		img.Size = UDim2.new(1, 0, 1, 0)
		img.BackgroundTransparency = 1
		img.ScaleType = Enum.ScaleType.Fit
		img.Image = tool.Image
		img.Parent = imgArea
	else
		local ph = Instance.new("TextLabel")
		ph.Size = UDim2.new(0.9, 0, 0.5, 0)
		ph.Position = UDim2.new(0.05, 0, 0.25, 0)
		ph.BackgroundTransparency = 1
		ph.Text = tool.Tag
		ph.TextColor3 = tool.Color
		ph.Font = Enum.Font.GothamBlack
		ph.TextScaled = true
		ph.Parent = imgArea
	end

	local nameLbl = Instance.new("TextLabel")
	nameLbl.Size = UDim2.new(0.92, 0, 0.15, 0)
	nameLbl.Position = UDim2.new(0.04, 0, 0.63, 0)
	nameLbl.BackgroundTransparency = 1
	nameLbl.Text = tool.Name
	nameLbl.TextColor3 = Color3.fromRGB(235, 245, 240)
	nameLbl.Font = Enum.Font.GothamBlack
	nameLbl.TextScaled = true
	nameLbl.Parent = card

	local badge = Instance.new("TextLabel")
	badge.Size = UDim2.new(0.84, 0, 0.14, 0)
	badge.Position = UDim2.new(0.08, 0, 0.82, 0)
	badge.BackgroundColor3 = Color3.fromRGB(32, 48, 50)
	badge.Text = "CLICK TO INSPECT"
	badge.TextColor3 = ACCENT_SOFT_TEXT
	badge.Font = Enum.Font.GothamBold
	badge.TextScaled = true
	badge.Parent = card

	local badgeCorner = Instance.new("UICorner")
	badgeCorner.CornerRadius = UDim.new(0.3, 0)
	badgeCorner.Parent = badge

	local click = Instance.new("ClickDetector")
	click.MaxActivationDistance = 16
	click.Parent = holder

	local prompt = Instance.new("ProximityPrompt")
	prompt.ObjectText = tool.Name
	prompt.ActionText = "Inspect Instrument"
	prompt.HoldDuration = 0
	prompt.MaxActivationDistance = 10
	prompt.RequiresLineOfSight = false
	prompt.Parent = holder

	click.MouseClick:Connect(function(player)
		inspectTool(player, tool)
	end)
	prompt.Triggered:Connect(function(player)
		inspectTool(player, tool)
	end)

	toolDisplays[tool.Id] = {Badge = badge, Stroke = cardStroke}
end

-- =========================================================================
-- 7. WALL TOOL PLATES (decoracion)
-- =========================================================================

for idx, plateLat in ipairs({-11.0, 11.0}) do
	makePart({
		Name = "WallToolPlate_" .. idx,
		Size = Vector3.new(10.5, 5.2, 0.35),
		Position = getWingPos(20.6, plateLat, 9.6),
		Orientation = Vector3.new(0, WING_ANGLE, 0),
		Material = Enum.Material.DiamondPlate,
		Color = Color3.fromRGB(84, 96, 106)
	})

	makePart({
		Name = "WallPlateBorder_" .. idx,
		Size = Vector3.new(10.9, 5.6, 0.2),
		Position = getWingPos(20.75, plateLat, 9.6),
		Orientation = Vector3.new(0, WING_ANGLE, 0),
		Material = Enum.Material.SmoothPlastic,
		Color = ACCENT_SAGE
	})

	for _, circleOffset in ipairs({-2.4, 2.4}) do
		makePart({
			Name = "WallPlateCircle_" .. idx .. "_" .. circleOffset,
			Size = Vector3.new(0.25, 2.8, 2.8),
			Position = getWingPos(20.38, plateLat + circleOffset, 9.6),
			Shape = Enum.PartType.Cylinder,
			Orientation = Vector3.new(0, WING_ANGLE + 90, 0),
			Material = Enum.Material.SmoothPlastic,
			Color = ACCENT_SAGE
		})
	end
end

-- =========================================================================
-- 8. SIDE TABLES: 3 mesas x 3 instrumentos = los 9 instrumentos
--    Izquierda = HiRISE/CRISM/SHARAD, Derecha = IUVS/STATIC/SWIA, Trasera = SWEA/SEP/LPW
--    (cambia el orden de TOOLS arriba para reubicarlos)
-- =========================================================================

local sideTables = {
	{Name = "LeftWallTable",   Forward = -4,  Lateral = -17.5, AngleOffset = 90},
	{Name = "RightWallTable",  Forward = -2,  Lateral = 17.5,  AngleOffset = -90},
	{Name = "RearCenterTable", Forward = -12, Lateral = 0,     AngleOffset = 0}
}

local toolIndex = 0
for _, tbl in ipairs(sideTables) do
	local tAngle = WING_ANGLE + tbl.AngleOffset
	local tCenter = getWingPos(tbl.Forward, tbl.Lateral, 1.6)

	makePart({
		Name = tbl.Name,
		Size = Vector3.new(12, 3.2, 4.2),
		Position = tCenter,
		Orientation = Vector3.new(0, tAngle, 0),
		Material = Enum.Material.Metal,
		Color = CABINET_BODY
	})

	makePart({
		Name = tbl.Name .. "_Trim",
		Size = Vector3.new(12.2, 0.18, 4.4),
		Position = tCenter + Vector3.new(0, 1.65, 0),
		Orientation = Vector3.new(0, tAngle, 0),
		Material = Enum.Material.SmoothPlastic,
		Color = ACCENT_SAGE
	})

	for _, offset in ipairs({-3.6, 0, 3.6}) do
		toolIndex += 1
		local tool = TOOLS[toolIndex]
		if tool then
			local rad = math.rad(tAngle)
			local perpX, perpZ = math.cos(rad), -math.sin(rad)
			local spotPos = Vector3.new(
				tCenter.X + perpX * offset,
				tCenter.Y + 1.78,
				tCenter.Z + perpZ * offset
			)
			createToolDisplay(tool, spotPos)
		end
	end
end

-- =========================================================================
-- 9. REFRESH DEL MUNDO 3D
-- =========================================================================

refreshWorld = function()
	refreshWallScreen()

	-- Computadora
	local n = countKeys(inspectedTools)
	if not actionsConfirmed then
		screenTxt.Text = "SCIENTIST COMPUTER\n[LOCKED: CONFIRM ACTIONS]"
	elseif n < #TOOLS then
		screenTxt.Text = string.format("SCIENTIST COMPUTER\n[LOCKED: INSTRUMENT DATA %d/%d]", n, #TOOLS)
	elseif reportSubmitted then
		screenTxt.Text = "SCIENTIST COMPUTER\n[REPORT " .. getReportStatus() .. "]"
	else
		screenTxt.Text = "SCIENTIST COMPUTER\n[SELECT INSTRUMENTS]"
	end

	-- Tarjetas de instrumentos
	for id, d in pairs(toolDisplays) do
		if inspectedTools[id] then
			d.Badge.Text = "✓ RECORDED"
			d.Badge.TextColor3 = Color3.fromRGB(105, 230, 150)
			d.Badge.BackgroundColor3 = Color3.fromRGB(20, 60, 44)
		else
			d.Badge.Text = "CLICK TO INSPECT"
			d.Badge.TextColor3 = ACCENT_SOFT_TEXT
			d.Badge.BackgroundColor3 = Color3.fromRGB(32, 48, 50)
		end
	end
end

-- =========================================================================
-- 10. SERVER HANDLERS
-- =========================================================================

ScienceActionEvent.OnServerEvent:Connect(function(player, actionType, payload)
	if type(actionType) ~= "string" then return end

	if actionType == "RequestReportSync" then
		SyncReportEvent:FireClient(player, buildState())
		return
	end

	if not hasScientistRole(player) then
		OpenStationEvent:FireClient(player, "ACCESS_DENIED")
		return
	end

	local locked = (reportSubmitted == true)

	if actionType == "ToggleAction" then
		if locked then
			toast(player, "REPORT ALREADY SENT. WAIT FOR THE LEADER'S DECISION", true)
			return
		end
		local id = tostring(payload)
		if not ACTION_BY_ID[id] then return end

		if stagedActions[id] then
			stagedActions[id] = nil
		else
			if countKeys(stagedActions) >= ACTIONS_REQUIRED then
				toast(player, "ONLY " .. ACTIONS_REQUIRED .. " ACTIONS ALLOWED. DESELECT ONE FIRST", true)
				return
			end
			stagedActions[id] = true
		end
		actionsConfirmed = false -- hay que volver a confirmar
		refreshAll()

	elseif actionType == "ConfirmActions" then
		if locked then return end
		if countKeys(stagedActions) ~= ACTIONS_REQUIRED then
			toast(player, "SELECT EXACTLY " .. ACTIONS_REQUIRED .. " ACTIONS", true)
			return
		end
		if ENFORCE_OBJECTIVE_MATCH then
			local objId = workspace:GetAttribute("ActiveObjective")
			if objectiveHasActions(objId) then
				for id in pairs(stagedActions) do
					if ACTION_BY_ID[id].Objective ~= objId then
						toast(player, "THOSE ACTIONS DON'T FIT THE MISSION OBJECTIVE. RE-READ THE BRIEFING", true)
						return
					end
				end
			end
		end
		actionsConfirmed = true
		refreshAll()
		toast(player, "ACTIONS CONFIRMED. INSPECT THE INSTRUMENTS ON THE LAB TABLES", false)

	elseif actionType == "ToggleTool" then
		if locked then
			toast(player, "REPORT ALREADY SENT. WAIT FOR THE LEADER'S DECISION", true)
			return
		end
		if not computerReady() then
			toast(player, "COMPUTER LOCKED. INSPECT ALL INSTRUMENTS FIRST", true)
			return
		end
		local id = tostring(payload)
		if not TOOL_BY_ID[id] then return end
		selectedTools[id] = (not selectedTools[id]) and true or nil
		refreshAll()

	elseif actionType == "SubmitReport" then
		if locked then return end
		if not computerReady() then
			toast(player, "COMPUTER LOCKED. INSPECT ALL INSTRUMENTS FIRST", true)
			return
		end
		local _, _, count = computeTotals()
		if count == 0 then
			toast(player, "SELECT AT LEAST ONE INSTRUMENT", true)
			return
		end
		if REQUIRE_TOOLS_FOR_ACTIONS then
			for id in pairs(stagedActions) do
				local covered = false
				for _, t in ipairs(TOOLS) do
					if selectedTools[t.Id] and table.find(t.Enables, id) then
						covered = true
						break
					end
				end
				if not covered then
					toast(player, "MISSING INSTRUMENT FOR: " .. string.upper(ACTION_BY_ID[id].Short), true)
					return
				end
			end
		end

		reportSubmitted = true
		decision = nil
		publishScienceData()
		refreshAll()
		toast(player, "REPORT SENT TO MONITORING FOR LEADER REVIEW", false)
	end
end)

-- Decision del lider (Monitoring pone Approval_Science = "APPROVED" / "REJECTED")
workspace:GetAttributeChangedSignal("Approval_Science"):Connect(function()
	local v = workspace:GetAttribute("Approval_Science")
	if v == "REJECTED" then
		decision = "REJECTED"
		reportSubmitted = false -- se reabre la edicion
		workspace:SetAttribute("ScienceReportSent", false)
		refreshAll()
	elseif v == "APPROVED" then
		decision = "APPROVED"
		refreshAll()
	end
end)

-- Los valores publicados (ScienceCost/ScienceWeight) se mantienen hasta que se reenvie el reporte.

-- =========================================================================
-- 11. RESET AL TERMINAR LA MISION (Monitoring pone MissionStarted = false)
-- =========================================================================

local function resetScience()
	stagedActions = {}
	actionsConfirmed = false
	inspectedTools = {}
	selectedTools = {}
	reportSubmitted = false
	decision = nil

	workspace:SetAttribute("ScienceReportSent", nil)
	workspace:SetAttribute("Approval_Science", nil)
	workspace:SetAttribute("ScienceCost", 0)
	workspace:SetAttribute("ScienceWeight", 0)
	workspace:SetAttribute("ScienceActionCount", 0)
	workspace:SetAttribute("ScienceToolCount", 0)

	refreshAll()
end

workspace:GetAttributeChangedSignal("MissionStarted"):Connect(function()
	if workspace:GetAttribute("MissionStarted") == false then
		resetScience()
	end
end)

-- Cambios de destino/objetivo actualizan los clientes
for _, attr in ipairs({"ActiveDestination", "ActiveObjective", "ActiveObjectiveTitle"}) do
	workspace:GetAttributeChangedSignal(attr):Connect(broadcast)
end

-- Estado inicial
workspace:SetAttribute("ScienceCost", 0)
workspace:SetAttribute("ScienceWeight", 0)
refreshWorld()

```

> **Artifact: ScienceLabClient.lua** (Write the new ScienceLabClient LocalScript)

```lua
-- =========================================================================
-- SCIENCE LAB CLIENT UI - v2
-- Location: StarterPlayer > StarterPlayerScripts > ScienceLabClient (LocalScript)
-- Vistas: Action Sheet (acciones + datos de instrumentos), ficha de instrumento,
--         computadora (seleccion de instrumentos + envio), reporte de la esquina.
-- El servidor manda el estado completo (SyncScienceReport) y la UI se redibuja sola.
-- =========================================================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")

local ScienceRemotes = ReplicatedStorage:WaitForChild("ScienceLabRemotes")
local OpenStationEvent = ScienceRemotes:WaitForChild("OpenScienceStation")
local ScienceActionEvent = ScienceRemotes:WaitForChild("ScienceLabAction")
local SyncReportEvent = ScienceRemotes:WaitForChild("SyncScienceReport")

local PALETTE = {
	BgMain       = Color3.fromRGB(24, 34, 36),
	BgPanel      = Color3.fromRGB(32, 44, 47),
	BgCard       = Color3.fromRGB(38, 52, 55),
	BgCardActive = Color3.fromRGB(46, 92, 78),
	AccentSoft   = Color3.fromRGB(115, 195, 165),
	PaperBg      = Color3.fromRGB(224, 232, 228),
	PaperInk     = Color3.fromRGB(24, 42, 38),
	TextPrimary  = Color3.fromRGB(225, 238, 234),
	TextMuted    = Color3.fromRGB(170, 195, 188),
	DangerSoft   = Color3.fromRGB(165, 70, 75),
	Gold         = Color3.fromRGB(255, 205, 80),
	Green        = Color3.fromRGB(105, 230, 150),
	Disabled     = Color3.fromRGB(70, 82, 86),
}

local STATUS_COLORS = {
	DRAFT = PALETTE.TextMuted,
	SUBMITTED = PALETTE.Gold,
	APPROVED = PALETTE.Green,
	REJECTED = Color3.fromRGB(240, 100, 100),
}

local function new(class, props, parent)
	local o = Instance.new(class)
	for k, v in pairs(props) do
		o[k] = v
	end
	o.Parent = parent
	return o
end

local function round(o, r)
	new("UICorner", {CornerRadius = UDim.new(0, r or 8)}, o)
end

local state = nil       -- ultimo estado recibido del servidor
local currentView = nil -- {Name = "ACTIONS_SHEET" | "COMPUTER" | "TOOL_INFO", ToolId, FirstTime}
local sheetTab = "ACTIONS"
local lastViewKey = nil

local gui = new("ScreenGui", {Name = "ScienceLabUI", ResetOnSpawn = false}, playerGui)

local function toolById(id)
	if not state then return nil end
	for _, t in ipairs(state.Tools) do
		if t.Id == id then return t end
	end
	return nil
end

local function actionById(id)
	if not state then return nil end
	for _, a in ipairs(state.Actions) do
		if a.Id == id then return a end
	end
	return nil
end

local function isLocked()
	return state ~= nil and (state.ReportStatus == "SUBMITTED" or state.ReportStatus == "APPROVED")
end

-- =========================================================================
-- TOAST
-- =========================================================================
local toastLbl = new("TextLabel", {
	Size = UDim2.new(0, 560, 0, 50), AnchorPoint = Vector2.new(0.5, 0),
	Position = UDim2.new(0.5, 0, 0.07, 0), BackgroundColor3 = PALETTE.DangerSoft, Visible = false,
	Text = "", TextColor3 = PALETTE.TextPrimary, Font = Enum.Font.GothamBold, TextScaled = true, ZIndex = 60,
}, gui)
round(toastLbl, 10)
new("UITextSizeConstraint", {MaxTextSize = 22, MinTextSize = 10}, toastLbl)
local toastToken = 0

local function toast(text, isError)
	toastToken += 1
	local mine = toastToken
	toastLbl.BackgroundColor3 = isError and PALETTE.DangerSoft or PALETTE.BgCardActive
	toastLbl.Text = "  " .. tostring(text) .. "  "
	toastLbl.Visible = true
	task.delay(3, function()
		if toastToken == mine then toastLbl.Visible = false end
	end)
end

-- =========================================================================
-- 1. CORNER REPORT SHEET HUD
-- =========================================================================
local function isScientistLocal()
	if workspace:GetAttribute("ScienceOpenAccess") == true then return true end
	if localPlayer:GetAttribute("Role_Scientist") == true then return true end
	local role = localPlayer:GetAttribute("AssignedRole")
	return type(role) == "string" and string.find(role, "Scientist", 1, true) ~= nil
end

local cornerReportBtn = new("TextButton", {
	Name = "ScientistCornerReportSheet",
	Size = UDim2.new(0, 130, 0, 155), Position = UDim2.new(1, -150, 0, 76),
	BackgroundColor3 = PALETTE.PaperBg, Text = "", Visible = isScientistLocal(),
}, gui)
round(cornerReportBtn, 8)
new("UIStroke", {Color = PALETTE.AccentSoft, Thickness = 2.5}, cornerReportBtn)

new("TextLabel", {
	Size = UDim2.new(1, -12, 0, 32), Position = UDim2.new(0, 6, 0, 8), BackgroundTransparency = 1,
	Text = "MISSION\nREPORT", TextColor3 = PALETTE.PaperInk, Font = Enum.Font.GothamBlack, TextScaled = true,
}, cornerReportBtn)

local crCountLbl = new("TextLabel", {
	Size = UDim2.new(1, -12, 0, 56), Position = UDim2.new(0, 6, 0, 48), BackgroundTransparency = 1,
	Text = "Tools: 0\nActions: 0", TextColor3 = Color3.fromRGB(45, 75, 66),
	Font = Enum.Font.GothamBold, TextScaled = true,
}, cornerReportBtn)

local crHintLbl = new("TextLabel", {
	Size = UDim2.new(1, -12, 0, 26), Position = UDim2.new(0, 6, 1, -32),
	BackgroundColor3 = PALETTE.BgCardActive, Text = "CLICK TO OPEN", TextColor3 = PALETTE.TextPrimary,
	Font = Enum.Font.GothamBold, TextScaled = true,
}, cornerReportBtn)
round(crHintLbl, 5)

-- =========================================================================
-- 2. FULL-SCREEN REPORT OVERLAY
-- =========================================================================
local fullReportFrame = new("Frame", {
	Name = "FullScreenReportSheet", Size = UDim2.new(0.86, 0, 0.86, 0), Position = UDim2.new(0.07, 0, 0.07, 0),
	BackgroundColor3 = PALETTE.PaperBg, Visible = false, ZIndex = 20,
}, gui)
round(fullReportFrame, 14)
new("UIStroke", {Color = PALETTE.BgCardActive, Thickness = 4}, fullReportFrame)

new("TextLabel", {
	Size = UDim2.new(1, -180, 0, 48), Position = UDim2.new(0, 28, 0, 18), BackgroundTransparency = 1,
	Text = "OFFICIAL SCIENCE MISSION REPORT", TextColor3 = PALETTE.PaperInk, Font = Enum.Font.GothamBlack,
	TextScaled = true, TextXAlignment = Enum.TextXAlignment.Left, ZIndex = 21,
}, fullReportFrame)

local frCloseBtn = new("TextButton", {
	Size = UDim2.new(0, 130, 0, 44), Position = UDim2.new(1, -154, 0, 20), BackgroundColor3 = PALETTE.DangerSoft,
	Text = "CLOSE [X]", TextColor3 = PALETTE.TextPrimary, Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 21,
}, fullReportFrame)
round(frCloseBtn, 8)

local reportScroll = new("ScrollingFrame", {
	Size = UDim2.new(1, -56, 1, -96), Position = UDim2.new(0, 28, 0, 78),
	BackgroundColor3 = Color3.fromRGB(234, 240, 237), BorderSizePixel = 0, ZIndex = 21,
	CanvasSize = UDim2.new(0, 0, 0, 0), AutomaticCanvasSize = Enum.AutomaticSize.Y, ScrollBarThickness = 6,
}, fullReportFrame)
new("UIPadding", {
	PaddingTop = UDim.new(0, 16), PaddingLeft = UDim.new(0, 18), PaddingRight = UDim.new(0, 18), PaddingBottom = UDim.new(0, 16),
}, reportScroll)

local reportText = new("TextLabel", {
	Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
	Text = "", TextColor3 = PALETTE.PaperInk, Font = Enum.Font.GothamSemibold, TextSize = 20, TextWrapped = true,
	TextXAlignment = Enum.TextXAlignment.Left, TextYAlignment = Enum.TextYAlignment.Top, ZIndex = 22,
}, reportScroll)

local function rebuildReportText()
	if not state then
		reportText.Text = "Waiting for mission data..."
		return
	end

	local lines = {}
	table.insert(lines, "SCIENTIST IN CHARGE: " .. localPlayer.DisplayName)
	table.insert(lines, "DESTINATION: " .. state.Destination)
	table.insert(lines, "MISSION OBJECTIVE: " .. state.ObjectiveTitle)
	table.insert(lines, "REPORT STATUS: " .. state.ReportStatus)
	table.insert(lines, "------------------------------------------------------------")
	table.insert(lines, string.format("1. MISSION ACTIONS (%d required):", state.ActionsRequired))

	if #state.SelectedActions == 0 then
		table.insert(lines, "   • No actions selected yet.")
	else
		for _, id in ipairs(state.SelectedActions) do
			local a = actionById(id)
			table.insert(lines, (state.ActionsConfirmed and "   [✓] " or "   [ ] ") .. (a and a.Title or id))
		end
	end

	table.insert(lines, "")
	table.insert(lines, string.format("2. INSTRUMENT DATA COLLECTED: %d / %d", state.InspectedCount, #state.Tools))
	table.insert(lines, "")
	table.insert(lines, "3. SELECTED INSTRUMENTS:")

	if #state.SelectedTools == 0 then
		table.insert(lines, "   • No instruments selected yet.")
	else
		for _, id in ipairs(state.SelectedTools) do
			local t = toolById(id)
			if t then
				table.insert(lines, string.format("   • %s — %s | $%dk", t.Name, t.WeightText, t.Cost))
			end
		end
	end

	table.insert(lines, "")
	table.insert(lines, "------------------------------------------------------------")
	table.insert(lines, string.format("TOTAL PAYLOAD WEIGHT: %.1f kg   |   TOTAL COST: $%dk",
		state.Totals.Weight, state.Totals.Cost))

	reportText.Text = table.concat(lines, "\n")
end

cornerReportBtn.MouseButton1Click:Connect(function()
	rebuildReportText()
	fullReportFrame.Visible = true
end)

frCloseBtn.MouseButton1Click:Connect(function()
	fullReportFrame.Visible = false
end)

local function updateCorner()
	cornerReportBtn.Visible = isScientistLocal()
	if state then
		crCountLbl.Text = "Tools: " .. #state.SelectedTools .. "\nActions: " .. #state.SelectedActions
		crHintLbl.Text = (state.ReportStatus == "DRAFT") and "CLICK TO OPEN" or state.ReportStatus
	end
end

-- =========================================================================
-- 3. MODAL WINDOW (body con scroll + footer fijo)
-- =========================================================================
local modal = new("Frame", {
	AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromScale(0.62, 0.82),
	BackgroundColor3 = PALETTE.BgMain, Visible = false, ZIndex = 10,
}, gui)
new("UISizeConstraint", {MinSize = Vector2.new(520, 400), MaxSize = Vector2.new(880, 680)}, modal)
round(modal, 14)
new("UIStroke", {Color = PALETTE.AccentSoft, Thickness = 2}, modal)

local titleLbl = new("TextLabel", {
	Size = UDim2.new(1, -90, 0, 38), Position = UDim2.new(0, 18, 0, 10), BackgroundTransparency = 1,
	Text = "SCIENCE LAB", TextColor3 = PALETTE.AccentSoft, Font = Enum.Font.GothamBold, TextScaled = true,
	TextXAlignment = Enum.TextXAlignment.Left,
}, modal)

local closeBtn = new("TextButton", {
	Size = UDim2.new(0, 42, 0, 38), Position = UDim2.new(1, -54, 0, 10), BackgroundColor3 = PALETTE.DangerSoft,
	Text = "X", TextColor3 = PALETTE.TextPrimary, Font = Enum.Font.GothamBold, TextScaled = true,
}, modal)
round(closeBtn, 8)

local body = new("ScrollingFrame", {
	Size = UDim2.new(1, -36, 1, -132), Position = UDim2.new(0, 18, 0, 56), BackgroundTransparency = 1,
	BorderSizePixel = 0, CanvasSize = UDim2.new(0, 0, 0, 0), AutomaticCanvasSize = Enum.AutomaticSize.Y,
	ScrollBarThickness = 6,
}, modal)
new("UIListLayout", {SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0, 8)}, body)

local footer = new("Frame", {
	Size = UDim2.new(1, -36, 0, 58), Position = UDim2.new(0, 18, 1, -68), BackgroundTransparency = 1,
}, modal)

closeBtn.MouseButton1Click:Connect(function()
	modal.Visible = false
	currentView = nil
end)

local layoutCounter = 0
local function nextOrder()
	layoutCounter += 1
	return layoutCounter
end

local function clearBody()
	layoutCounter = 0
	for _, child in ipairs(body:GetChildren()) do
		if not child:IsA("UIListLayout") then
			child:Destroy()
		end
	end
	for _, child in ipairs(footer:GetChildren()) do
		child:Destroy()
	end
end

-- Texto simple (se ajusta al contenido)
local function addText(text, size, color, font)
	return new("TextLabel", {
		Size = UDim2.new(1, -6, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
		Text = text, TextColor3 = color or PALETTE.TextMuted, Font = font or Enum.Font.GothamSemibold,
		TextSize = size or 16, TextWrapped = true, TextXAlignment = Enum.TextXAlignment.Left,
		TextYAlignment = Enum.TextYAlignment.Top, LayoutOrder = nextOrder(),
	}, body)
end

-- Tarjeta con titulo + descripcion. Si recibe onClick es un boton.
local function addRow(titleText, descText, selected, onClick, titleColor)
	local props = {
		Size = UDim2.new(1, -6, 0, 0), AutomaticSize = Enum.AutomaticSize.Y,
		BackgroundColor3 = selected and PALETTE.BgCardActive or PALETTE.BgCard, LayoutOrder = nextOrder(),
	}
	if onClick then
		props.Text = ""
		props.AutoButtonColor = false
	end
	local row = new(onClick and "TextButton" or "Frame", props, body)
	round(row, 8)
	new("UIStroke", {
		Color = selected and PALETTE.AccentSoft or PALETTE.BgPanel, Thickness = selected and 2 or 1,
	}, row)
	new("UIPadding", {
		PaddingTop = UDim.new(0, 8), PaddingBottom = UDim.new(0, 8),
		PaddingLeft = UDim.new(0, 12), PaddingRight = UDim.new(0, 12),
	}, row)
	new("UIListLayout", {SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0, 3)}, row)

	new("TextLabel", {
		Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
		Text = titleText, TextColor3 = titleColor or PALETTE.TextPrimary, Font = Enum.Font.GothamBold,
		TextSize = 18, TextWrapped = true, TextXAlignment = Enum.TextXAlignment.Left, LayoutOrder = 1,
	}, row)
	if descText and descText ~= "" then
		new("TextLabel", {
			Size = UDim2.new(1, 0, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, BackgroundTransparency = 1,
			Text = descText, TextColor3 = PALETTE.TextMuted, Font = Enum.Font.Gotham,
			TextSize = 14, TextWrapped = true, TextXAlignment = Enum.TextXAlignment.Left,
			TextYAlignment = Enum.TextYAlignment.Top, LayoutOrder = 2,
		}, row)
	end

	if onClick then
		row.MouseButton1Click:Connect(onClick)
	end
	return row
end

local function footerLabel(text, color, widthScale)
	return new("TextLabel", {
		Size = UDim2.new(widthScale or 0.5, -8, 1, 0), BackgroundTransparency = 1, Text = text,
		TextColor3 = color or PALETTE.TextMuted, Font = Enum.Font.GothamBold, TextSize = 17, TextWrapped = true,
		TextXAlignment = Enum.TextXAlignment.Left,
	}, footer)
end

local function footerButton(text, enabled, onClick)
	local b = new("TextButton", {
		Size = UDim2.new(0.46, 0, 0, 48), AnchorPoint = Vector2.new(1, 0.5), Position = UDim2.new(1, 0, 0.5, 0),
		BackgroundColor3 = enabled and PALETTE.AccentSoft or PALETTE.Disabled, Text = text,
		TextColor3 = enabled and PALETTE.BgMain or PALETTE.TextMuted, Font = Enum.Font.GothamBlack,
		TextScaled = true, AutoButtonColor = enabled,
	}, footer)
	round(b, 10)
	new("UITextSizeConstraint", {MaxTextSize = 22, MinTextSize = 10}, b)
	if enabled and onClick then
		b.MouseButton1Click:Connect(onClick)
	end
	return b
end

-- =========================================================================
-- 4. ACTION SHEET (tabs: ACTIONS / TOOL DATA)
-- =========================================================================
local function renderActionSheet()
	titleLbl.Text = "MISSION ACTION SHEET"

	local tabs = new("Frame", {Size = UDim2.new(1, -6, 0, 38), BackgroundTransparency = 1, LayoutOrder = nextOrder()}, body)
	for i, name in ipairs({"ACTIONS", "TOOL DATA"}) do
		local b = new("TextButton", {
			Size = UDim2.new(0.49, 0, 1, 0), Position = UDim2.new((i - 1) * 0.51, 0, 0, 0),
			BackgroundColor3 = (sheetTab == name) and PALETTE.BgCardActive or PALETTE.BgCard,
			Text = name, TextColor3 = PALETTE.TextPrimary, Font = Enum.Font.GothamBlack, TextScaled = true,
		}, tabs)
		round(b, 8)
		new("UITextSizeConstraint", {MaxTextSize = 20, MinTextSize = 10}, b)
		b.MouseButton1Click:Connect(function()
			sheetTab = name
			-- se redibuja desde render()
			if currentView then lastViewKey = nil end
			task.defer(function()
				if modal.Visible then
					clearBody()
					renderActionSheet()
				end
			end)
		end)
	end

	if sheetTab == "ACTIONS" then
		addText("MISSION: " .. state.ObjectiveTitle, 17, PALETTE.AccentSoft, Enum.Font.GothamBold)
		addText(string.format("Choose the %d scientific actions that fulfil this mission's objective, then confirm.",
			state.ActionsRequired), 15)

		local picked = {}
		for _, id in ipairs(state.SelectedActions) do
			picked[id] = true
		end
		local locked = isLocked()

		for i, a in ipairs(state.Actions) do
			local isPicked = picked[a.Id] == true
			addRow(
				(isPicked and "[✓]  " or "[   ]  ") .. i .. ". " .. a.Title,
				a.Desc,
				isPicked,
				(not locked) and function()
					ScienceActionEvent:FireServer("ToggleAction", a.Id)
				end or nil
			)
		end

		local count = #state.SelectedActions
		if locked then
			footerLabel("REPORT " .. state.ReportStatus .. " — ACTIONS LOCKED", PALETTE.Gold)
			footerButton("LOCKED", false)
		elseif state.ActionsConfirmed then
			footerLabel(string.format("SELECTED %d / %d", count, state.ActionsRequired), PALETTE.Green)
			footerButton("✓ ACTIONS CONFIRMED", false)
		else
			footerLabel(string.format("SELECTED %d / %d", count, state.ActionsRequired), PALETTE.TextPrimary)
			footerButton("CONFIRM ACTIONS", count == state.ActionsRequired, function()
				ScienceActionEvent:FireServer("ConfirmActions")
			end)
		end
	else
		addText(string.format("Instrument data recorded: %d / %d. Inspect each instrument on the lab tables to fill this sheet.",
			state.InspectedCount, #state.Tools), 15)

		for _, t in ipairs(state.Tools) do
			if state.Inspected[t.Id] then
				addRow(
					t.Name .. "  —  " .. t.FullName,
					t.Description .. "\nWEIGHT: " .. t.WeightText .. "   |   COST: " .. t.CostText
						.. "  (mission budget: $" .. t.Cost .. "k)",
					false, nil, PALETTE.Green
				)
			else
				addRow("???  (not inspected yet)", "Interact with this instrument on the lab tables to record its data.", false, nil, PALETTE.TextMuted)
			end
		end

		footerLabel(string.format("RECORDED %d / %d", state.InspectedCount, #state.Tools),
			(state.InspectedCount >= #state.Tools) and PALETTE.Green or PALETTE.TextPrimary)
		footerButton("CLOSE", true, function()
			modal.Visible = false
			currentView = nil
		end)
	end
end

-- =========================================================================
-- 5. TOOL INFO (al interactuar con un instrumento)
-- =========================================================================
local function renderToolInfo(toolId, firstTime)
	local tool = toolById(toolId)
	if not tool then return end
	titleLbl.Text = "INSTRUMENT — " .. string.upper(tool.Name)

	addRow(
		tool.Name .. "  —  " .. tool.FullName,
		tool.Description .. "\n\nWEIGHT: " .. tool.WeightText .. "\nESTIMATED COST: " .. tool.CostText
			.. "\nMISSION BUDGET COST: $" .. tool.Cost .. "k",
		false, nil, PALETTE.AccentSoft
	)

	if firstTime then
		addText("✓ NEW DATA RECORDED ON THE ACTION SHEET", 17, PALETTE.Green, Enum.Font.GothamBold)
	else
		addText("This instrument's data is already recorded on the Action Sheet.", 15)
	end

	footerLabel(string.format("INSTRUMENTS RECORDED %d / %d", state.InspectedCount, #state.Tools),
		(state.InspectedCount >= #state.Tools) and PALETTE.Green or PALETTE.TextPrimary)
	footerButton("VIEW ACTION SHEET", true, function()
		sheetTab = "TOOL DATA"
		currentView = {Name = "ACTIONS_SHEET"}
		lastViewKey = nil
		clearBody()
		renderActionSheet()
	end)
end

-- =========================================================================
-- 6. COMPUTER (seleccion de instrumentos + envio)
-- =========================================================================
local function renderComputer()
	titleLbl.Text = "SCIENTIST COMPUTER — CHOOSE INSTRUMENTS"

	if not state.ActionsConfirmed then
		addRow("LOCKED", "Select and confirm your mission actions on the Action Sheet first.", false, nil, PALETTE.Gold)
		footerLabel("COMPUTER LOCKED", PALETTE.Gold)
		return
	end

	if state.InspectedCount < #state.Tools then
		local missing = {}
		for _, t in ipairs(state.Tools) do
			if not state.Inspected[t.Id] then
				table.insert(missing, t.Name)
			end
		end
		addRow(
			string.format("LOCKED — INSTRUMENT DATA %d / %d", state.InspectedCount, #state.Tools),
			"Inspect every instrument on the lab tables to unlock the catalog.\nStill to inspect: " .. table.concat(missing, ", "),
			false, nil, PALETTE.Gold
		)
		footerLabel("COMPUTER LOCKED", PALETTE.Gold)
		return
	end

	local locked = isLocked()
	local names = {}
	for _, id in ipairs(state.SelectedActions) do
		local a = actionById(id)
		table.insert(names, a and a.Short or id)
	end
	addText("MISSION ACTIONS: " .. table.concat(names, "  +  "), 16, PALETTE.AccentSoft, Enum.Font.GothamBold)

	if state.ReportStatus == "REJECTED" then
		addText("✗ The leader REJECTED your report. Adjust the instruments (or actions) and submit again.", 15, STATUS_COLORS.REJECTED, Enum.Font.GothamBold)
	elseif locked then
		addText("Report " .. state.ReportStatus .. ". Editing is locked.", 15, STATUS_COLORS[state.ReportStatus], Enum.Font.GothamBold)
	else
		addText("Pick the instruments you need. Cost goes to Monitoring (leader approval), weight goes to the Spacecraft payload.", 15)
	end

	local picked = {}
	for _, id in ipairs(state.SelectedTools) do
		picked[id] = true
	end

	for _, t in ipairs(state.Tools) do
		local isPicked = picked[t.Id] == true
		addRow(
			string.format("%s %s   —   %s  |  $%dk", isPicked and "[✓]" or "[   ]", t.Name, t.WeightText, t.Cost),
			t.Description,
			isPicked,
			(not locked) and function()
				ScienceActionEvent:FireServer("ToggleTool", t.Id)
			end or nil
		)
	end

	footerLabel(
		string.format("%d selected\n%.1f kg  |  $%dk", #state.SelectedTools, state.Totals.Weight, state.Totals.Cost),
		PALETTE.TextPrimary
	)
	if locked then
		footerButton("REPORT " .. state.ReportStatus, false)
	else
		footerButton("SUBMIT REPORT", #state.SelectedTools > 0, function()
			ScienceActionEvent:FireServer("SubmitReport")
		end)
	end
end

-- =========================================================================
-- 7. RENDER ROUTER
-- =========================================================================
function renderCurrent()
	if not modal.Visible or not currentView or not state then return end

	local viewKey = currentView.Name .. (currentView.ToolId or "") .. (currentView.Name == "ACTIONS_SHEET" and sheetTab or "")
	local keepPos = (viewKey == lastViewKey) and body.CanvasPosition or Vector2.new(0, 0)
	lastViewKey = viewKey

	clearBody()
	if currentView.Name == "ACTIONS_SHEET" then
		renderActionSheet()
	elseif currentView.Name == "COMPUTER" then
		renderComputer()
	elseif currentView.Name == "TOOL_INFO" then
		renderToolInfo(currentView.ToolId, currentView.FirstTime)
	end

	task.spawn(function()
		RunService.RenderStepped:Wait()
		body.CanvasPosition = keepPos
	end)
end

-- =========================================================================
-- 8. REMOTES
-- =========================================================================
SyncReportEvent.OnClientEvent:Connect(function(newState)
	if type(newState) ~= "table" then return end
	state = newState
	updateCorner()
	if fullReportFrame.Visible then
		rebuildReportText()
	end
	renderCurrent()
end)

OpenStationEvent.OnClientEvent:Connect(function(viewType, a, b, c)
	if viewType == "ACCESS_DENIED" then
		toast("ACCESS DENIED: ONLY THE SCIENTIST CAN USE THIS LAB", true)
		return
	elseif viewType == "TOAST" then
		toast(a, b)
		return
	end

	if type(a) == "table" then
		state = a
	end
	if not state then return end

	if viewType == "ACTIONS_SHEET" then
		currentView = {Name = "ACTIONS_SHEET"}
	elseif viewType == "COMPUTER" then
		currentView = {Name = "COMPUTER"}
	elseif viewType == "TOOL_INFO" then
		currentView = {Name = "TOOL_INFO", ToolId = b, FirstTime = (c == true)}
	else
		return
	end

	modal.Visible = true
	lastViewKey = nil
	updateCorner()
	renderCurrent()
end)

-- Visibilidad de la hoja de esquina segun el rol
localPlayer:GetAttributeChangedSignal("AssignedRole"):Connect(function()
	updateCorner()
	if isScientistLocal() then
		ScienceActionEvent:FireServer("RequestReportSync")
	end
end)
localPlayer:GetAttributeChangedSignal("Role_Scientist"):Connect(updateCorner)
workspace:GetAttributeChangedSignal("ScienceOpenAccess"):Connect(updateCorner)

ScienceActionEvent:FireServer("RequestReportSync")

```

---

## You `2026-11-15T14:15:40.984775Z`

There are a few bugs I wanna address. The "pop-up" or like the box with Mission Report Tools Actions Click to open, should be only visible for the scientist after choosing roles on the hologram. The actions are not visible to select and there is no "close" button for the window for these actions.

---

## Claude `2026-11-15T14:17:58.373072Z`

I've fixed both problems in the updated `ScienceLabClient` and `ScienceLabManager`. I could only syntax-check them, so test in Studio.

**Mission Report box**
- It showed up for everyone because my test-mode shortcut treated everyone as a scientist. I removed that.
- It now appears only when the player has `Role_Scientist`, which the hologram sets when the leader presses **Confirm Roles**.
- It disappears again when the mission ends and the roles are cleared. If the full report window was open at that moment, it closes too.
- I also deleted the `ScienceOpenAccess` line from the manager, since nothing uses it now.

**Actions window with nothing to select and no close button**
- The most likely cause is that the window's contents were being drawn behind the window itself. I set the ScreenGui's `ZIndexBehavior` to `Sibling` explicitly, so the rows, tabs and buttons always draw on top of their parent frame.
- I couldn't reproduce this, so that is my best diagnosis. If the actions still don't show up, check Studio's Output window for a red error from `ScienceLabClient` and send it to me.
- Whatever the cause, the window now has several ways to close:
  - a larger red **CLOSE [X]** button in the top-right corner
  - a **CLOSE** button in the footer, on every view
  - clicking the dimmed area outside the window, which also stops clicks passing through to the 3D world
- The window now takes 70% of the screen width and 86% of the height, instead of a minimum size that could be bigger than a small screen.
- On the **TOOL DATA** tab, the old CLOSE button became **BACK TO ACTIONS**, since the footer already has a close button.

---

## You `2026-11-15T14:47:59.715650Z`

There is something I wanna change. When you try to submitt the report as the scientist it tell you what action or tool you are missing but it shouldnt. This will be evaluated on the end of the game. Do save the information of which ones it should've chosen and which one the player did choose, for the final evaluation. We want them to be able to make mistakes and thats why the resource manage has to accept or reject the scientist proposal.

---

## Claude `2026-11-15T14:49:12.893672Z`

I've updated `ScienceLabManager`. The scientist is no longer told when something is wrong. `ScienceLabClient` needs no changes. I haven't run it in Studio.

### What the scientist no longer sees
- **Confirming actions:** any 2 actions are accepted. The "those actions don't fit the objective" message is gone.
- **Submitting the report:** whatever instruments they picked get sent, and the "missing instrument for X" message is gone. It only requires at least one instrument.
- **Hidden answers:** the answer keys (which objective an action belongs to, which actions an instrument enables) are no longer sent to the clients, so an exploiter can't read them.

The Resource Manager is now the only check. Monitoring already handles the leader's approve and reject decisions.

### What gets saved for the final evaluation
Every time the report is submitted, the server saves what the player chose and what they should have chosen as `workspace` attributes. A later submission overwrites the earlier one, so the final submission is the one that counts. Everything clears when the mission resets.

- **Actions**
  - Expected actions are the ones tagged for the active objective. For MRO that is 3, and any 2 of them count as correct.
  - Saved: `ScienceChosenActions`, `ScienceExpectedActions`, `ScienceCorrectActionCount`, `ScienceWrongActionCount`, plus readable `…Text` versions.
- **Instruments**
  - Expected instruments are all the ones that enable the correct actions the player chose. For example, if they chose Solar wind monitoring, all four of SWIA, SWEA, SEP and LPW are expected.
  - Saved: `ScienceChosenTools`, `ScienceExpectedTools`, `ScienceExpectedToolCount`, `ScienceCorrectToolCount`, `ScienceWrongToolCount` (extras), `ScienceMissingToolCount`, plus `…Text` versions including `ScienceWrongToolsText` and `ScienceMissingToolsText`.
- **Extras:** `ScienceSubmissionCount` counts how many times they submitted, and `ScienceEvalDefined` says whether the objective has actions to compare against.

Expected instruments depend on the actions the player picked. If they chose a wrong action, its instruments aren't expected, so a wrong action shows up in the action score rather than being counted twice.

For Moon and Ceres there is nothing defined to compare against, so everything counts as correct.

### Change needed in MonitoringRoomManager
The Science rows in `computeEvaluation` still use the old counts. Replace the science points with:

```lua
local okActions = tonumber(workspace:GetAttribute("ScienceCorrectActionCount")) or 0
local okTools = tonumber(workspace:GetAttribute("ScienceCorrectToolCount")) or 0
local wrongTools = tonumber(workspace:GetAttribute("ScienceWrongToolCount")) or 0
local expectedTools = tonumber(workspace:GetAttribute("ScienceExpectedToolCount")) or 0

local sciPts = 12 * math.clamp(okActions / 2, 0, 1)
	+ 8 * math.clamp((okTools - wrongTools) / math.max(expectedTools, 1), 0, 1)
```

And in the SCIENCE row, replace the `Detail` with:

```lua
Detail = string.format("%d/2 right actions · %d/%d needed tools · %d extra",
	okActions, okTools, expectedTools, wrongTools),
```

Each missed instrument costs points, and so does each extra one.

If you want the evaluation screen to name what they got wrong, the `…Text` attributes already hold those lists.

---

## You `2026-11-15T14:55:26.871676Z`

so monitoringroommanager should look like this?

**Attachments:**
- attachment (txt, 32.9 KB)

---

## Claude `2026-11-15T14:55:48.976433Z`

Almost. Your file has two mistakes in `computeEvaluation`, and either one stops the whole script from working.

**1. A closing brace is missing on the SCIENCE row.** The `Detail = string.format(...)` line needs a `}` after it, before the `TEAMWORK` row starts. As pasted, the file doesn't parse.

**2. The old `sciPts` line is still there.** Your file has the new `sciPts` near the top, and then the old one further down:

```lua
local sciPts = 12 * math.clamp(actions / 5, 0, 1) + 8 * math.clamp(tools / 4, 0, 1)
```

`actions` and `tools` no longer exist, so this would error as soon as the mission is launched. Delete that line and the old comment above it.

Replace the whole `computeEvaluation` function with this version. I ran it through the Luau syntax checker, and it passes. It keeps your new science scoring and removes the old line.

```lua
local function computeEvaluation()
	local total = tonumber(workspace:GetAttribute("MissionBudgetTotal")) or STARTING_BUDGET
	local vel = tonumber(workspace:GetAttribute("SpacecraftVelocity")) or 0
	local maxV = tonumber(workspace:GetAttribute("PhysicistMaxVelocity")) or 30.6
	local payload = tonumber(workspace:GetAttribute("SpacecraftPayload")) or 0
	local sciW = tonumber(workspace:GetAttribute("ScienceWeight")) or 0
	local okActions = tonumber(workspace:GetAttribute("ScienceCorrectActionCount")) or 0
	local okTools = tonumber(workspace:GetAttribute("ScienceCorrectToolCount")) or 0
	local wrongTools = tonumber(workspace:GetAttribute("ScienceWrongToolCount")) or 0
	local expectedTools = tonumber(workspace:GetAttribute("ScienceExpectedToolCount")) or 0

	-- BUDGET (25): puntaje completo si sobra >= 25% del presupuesto
	local budgetPts = 25 * math.clamp(currentBudget / (total * 0.25), 0, 1)
	-- TIME (20): puntaje completo si se lanza con >= 50% del tiempo restante
	local timePts = 20 * math.clamp((countdownTime / COUNTDOWN_START) / 0.5, 0, 1)
	-- SPACECRAFT (20): 10 por aprovechar la velocidad permitida + 10 por aprovechar la carga util
	local velPts = 10 * math.clamp(vel / math.max(maxV, 0.001), 0, 1)
	local payPts = (payload > 0) and (10 * math.clamp(sciW / payload, 0, 1)) or 0
	-- SCIENCE (20): 12 por acciones correctas (de 2) + 8 por instrumentos (correctos - sobrantes) / necesarios
	local sciPts = 12 * math.clamp(okActions / 2, 0, 1)
		+ 8 * math.clamp((okTools - wrongTools) / math.max(expectedTools, 1), 0, 1)
	-- TEAMWORK (15): -5 por cada rechazo del lider
	local teamPts = math.max(0, 15 - 5 * rejectionCount)

	local function r(x)
		return math.floor(x + 0.5)
	end

	local rows = {
		{Name = "BUDGET", Points = r(budgetPts), Max = 25,
			Detail = string.format("$%dk of $%dk left", math.floor(currentBudget), total)},
		{Name = "TIME", Points = r(timePts), Max = 20,
			Detail = formatClock(countdownTime) .. " left on the timer"},
		{Name = "SPACECRAFT", Points = r(velPts + payPts), Max = 20,
			Detail = string.format("%.1f/%.1f km/s · %.1f/%.1f kg payload used", vel, maxV, sciW, payload)},
		{Name = "SCIENCE", Points = r(sciPts), Max = 20,
			Detail = string.format("%d/2 right actions · %d/%d needed tools · %d extra",
				okActions, okTools, expectedTools, wrongTools)},
		{Name = "TEAMWORK", Points = r(teamPts), Max = 15,
			Detail = string.format("%d leader rejection(s)", rejectionCount)},
	}

	local totalPts = 0
	for _, row in ipairs(rows) do
		totalPts += row.Points
	end

	local grade = "D"
	if totalPts >= 90 then grade = "S"
	elseif totalPts >= 80 then grade = "A"
	elseif totalPts >= 65 then grade = "B"
	elseif totalPts >= 50 then grade = "C" end

	return {Total = totalPts, Grade = grade, Rows = rows, ShowFor = EVAL_SCREEN_TIME}
end
```

I didn't find anything else wrong in the rest of your file.

---

## You `2026-11-15T15:04:26.103678Z`

For the simulation hall, we had mini games in the mian screen, the games were like this: in this room we will have a layout as the shown in the image, the main actions that are going to be made in this room is for the player to play threee different minigames and at the end of each minigame a piece of information will be send to the spacecraft section sheet of paper so each time the user succesfully passes a minigame this part should be update.

The first minigame is about max velocity thesuccesfullyld have to mantain a space ship between a region during a period of time, coLetser for this ship to be mantained in this position a key should be type, if the key is not pressed the ship is going to go to the left slowly. As stated before after completing a game succesfully, the max velocity will be sendto sheet of paper on the spacecraft section. Lets use the placeholder of: 110,000 km/hr.

The second minigame is about trayectory. Is a classic spaceship avoiding asteroids. After finishing this minigame the trayectory will be send to the sheet of paper in the spacecraft room. Lets use the placeholder of: interplanetary transfer trayectory and aerobraking.

The third and last minigame is about orbit. The game will be similar to angrybirds in the sense that you have to launch the spaceship in order to enter the orbits/planets for the firtst version of this make it so that the orbit the figure it will make is a near-circular polar orbit. After completing this minigame, the orbit will be send into the sheet of paper in the spacecraft section. Use the placeholder of:  near circular polar orbit. I need you to put these minigames in the main screen of the simulation hall. When the user clicks on the screen, the screen that is gonna display would have three options for three mini games and the options to play, and then right there, the user can play

---

## Claude `2026-11-15T15:10:50.329943Z`



> **Artifact: SimulationRoomManager.lua** (Write the updated SimulationRoomManager server script with minigame support)

```lua
-- =========================================================================
-- SIMULATION ROOM MANAGER (PHYSICIST WING - NORTH, ANGLE 0) - v2 (minigames)
-- Location: ServerScriptService > SimulationRoomManager (Script)
-- Needs LocalScript "SimulationRoomClient" in StarterPlayerScripts.
--
-- Click the main board -> the client opens a hub with 3 minigames.
-- Each minigame the player wins sends its piece of info to the Spacecraft
-- station sheet (workspace attributes Physicist*), and updates the room screens.
--   1. MAX VELOCITY  -> PhysicistMaxVelocityText / PhysicistMaxVelocity (km/s)
--   2. TRAJECTORY    -> PhysicistTrajectoryText
--   3. POLAR ORBIT   -> PhysicistOrbitText
-- Everything resets when Monitoring sets MissionStarted = false.
-- =========================================================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Remotes
local SimRemotes = ReplicatedStorage:FindFirstChild("SimulationRemotes") or Instance.new("Folder")
SimRemotes.Name = "SimulationRemotes"
SimRemotes.Parent = ReplicatedStorage

local OpenSimEvent = SimRemotes:FindFirstChild("OpenSimulationStation") or Instance.new("RemoteEvent")
OpenSimEvent.Name = "OpenSimulationStation"
OpenSimEvent.Parent = SimRemotes

local CompleteMinigameEvent = SimRemotes:FindFirstChild("CompleteMinigame") or Instance.new("RemoteEvent")
CompleteMinigameEvent.Name = "CompleteMinigame"
CompleteMinigameEvent.Parent = SimRemotes

local SyncSimStateEvent = SimRemotes:FindFirstChild("SyncSimulationState") or Instance.new("RemoteEvent")
SyncSimStateEvent.Name = "SyncSimulationState"
SyncSimStateEvent.Parent = SimRemotes

-- Coordinates (Matches MainLabManager North Wing at Angle 0)
local LAB_ORIGIN = Vector3.new(0, 500, 400)
local LAB_CENTER = LAB_ORIGIN + Vector3.new(0, 0, 50)
local WING_ANGLE = 0
local HALF_MAIN = 32
local WING_SIZE = 44
local WING_CENTER_DIST = HALF_MAIN + (WING_SIZE / 2)

local REQUIRE_PHYSICIST_ROLE = false -- true en la version final

-- Info que se envia a Spacecraft (la velocidad numerica va en km/s, igual que Spacecraft)
local PLACEHOLDERS = {
	MaxVelocity = "110,000 km/hr (30.6 km/s)",
	MaxVelocityNumeric = 30.6, -- km/s
	Trajectory = "interplanetary transfer trajectory and aerobraking",
	Orbit = "near circular polar orbit"
}

-- Id del minijuego -> llave de progreso / info que se envia / segundos minimos de juego
-- (el cliente avisa con "START" y luego "completado"; si llega antes del minimo se ignora)
local GAME_KEY = {
	[1] = "Game1_Velocity",
	[2] = "Game2_Trajectory",
	[3] = "Game3_Orbit",
}
local GAME_INFO = {
	[1] = PLACEHOLDERS.MaxVelocity,
	[2] = PLACEHOLDERS.Trajectory,
	[3] = PLACEHOLDERS.Orbit,
}
local MIN_PLAY_SECONDS = {
	[1] = 9,
	[2] = 20,
	[3] = 3,
}

local PALETTE = {
	DeskDark     = Color3.fromRGB(26, 34, 46),
	PanelSlate   = Color3.fromRGB(14, 24, 38),
	ScreenBg     = Color3.fromRGB(8, 16, 26),
	CyanNeon     = Color3.fromRGB(0, 210, 255),
	GoldNeon     = Color3.fromRGB(255, 200, 45),
	GreenOk      = Color3.fromRGB(0, 230, 145),
	White        = Color3.fromRGB(242, 248, 255),
	Dim          = Color3.fromRGB(155, 215, 235)
}

local SimRoomModel = Instance.new("Model")
SimRoomModel.Name = "SimulationRoomInterior"
SimRoomModel.Parent = workspace

local simProgress = {
	Game1_Velocity = false,
	Game2_Trajectory = false,
	Game3_Orbit = false
}

local playStarts = {} -- [player] = {[gameId] = os.clock()}

-- =========================================================================
-- HELPERS
-- =========================================================================

local function getWingPos(forwardOffset, lateralOffset, height)
	local totalForward = WING_CENTER_DIST + forwardOffset
	local rad = math.rad(WING_ANGLE)
	local dirX, dirZ = math.sin(rad), math.cos(rad)
	local perpX, perpZ = math.cos(rad), -math.sin(rad)
	return Vector3.new(
		LAB_CENTER.X + dirX * totalForward + perpX * lateralOffset,
		LAB_CENTER.Y + height,
		LAB_CENTER.Z + dirZ * totalForward + perpZ * lateralOffset
	)
end

local function makePart(props)
	local p = Instance.new("Part")
	for k, v in pairs(props) do
		p[k] = v
	end
	p.Anchored = true
	p.Parent = SimRoomModel
	return p
end

-- Con 1-3 jugadores el AssignedRole es combinado, por eso se usa el atributo booleano Role_Physicist
local function canUseConsole(player)
	if not REQUIRE_PHYSICIST_ROLE then return true end
	return player:GetAttribute("Role_Physicist") == true
		or (player:GetAttribute("IsSquadLeader") == true)
end

-- Textos de info de los minijuegos ya completados (llave = id como string)
local function buildInfo()
	local info = {}
	for id, key in ipairs(GAME_KEY) do
		if simProgress[key] then
			info[tostring(id)] = GAME_INFO[id]
		end
	end
	return info
end

-- =========================================================================
-- 1. CEILING TRIM & FRONT CONSOLE DESK
-- =========================================================================

makePart({
	Name = "SimCeilingNeonFront",
	Size = Vector3.new(41.8, 0.4, 0.4),
	Position = getWingPos(20.7, 0, 18.8),
	Material = Enum.Material.Neon,
	Color = PALETTE.GoldNeon
})

for _, side in ipairs({-1, 1}) do
	makePart({
		Name = "SimCeilingNeonSide_" .. side,
		Size = Vector3.new(0.4, 0.4, 41.8),
		Position = getWingPos(0, side * 20.7, 18.8),
		Material = Enum.Material.Neon,
		Color = PALETTE.CyanNeon
	})
end

makePart({
	Name = "SimFrontDesk",
	Size = Vector3.new(30.0, 3.3, 5.0),
	Position = getWingPos(18.0, 0, 1.65),
	Material = Enum.Material.Metal,
	Color = PALETTE.DeskDark
})

makePart({
	Name = "SimFrontDeskTrim",
	Size = Vector3.new(30.2, 0.25, 0.25),
	Position = getWingPos(15.45, 0, 3.2),
	Material = Enum.Material.Neon,
	Color = PALETTE.GoldNeon
})

-- =========================================================================
-- 2. FRONT WALL: MAIN SIMULATION BOARD
-- =========================================================================

makePart({
	Name = "MainSimBoardFrame",
	Size = Vector3.new(28.4, 11.8, 0.35),
	Position = getWingPos(20.65, 0, 10.6),
	Material = Enum.Material.Neon,
	Color = PALETTE.GoldNeon
})

local mainSimBoard = makePart({
	Name = "MainSimBoardFace",
	Size = Vector3.new(27.2, 10.6, 0.4),
	Position = getWingPos(20.45, 0, 10.6),
	Material = Enum.Material.SmoothPlastic,
	Color = PALETTE.ScreenBg
})

local boardGui = Instance.new("SurfaceGui")
boardGui.Name = "MainSimBoardGui"
boardGui.Face = Enum.NormalId.Front
boardGui.LightInfluence = 0
boardGui.Parent = mainSimBoard

local boardRoot = Instance.new("Frame")
boardRoot.Size = UDim2.new(1, 0, 1, 0)
boardRoot.BackgroundColor3 = PALETTE.ScreenBg
boardRoot.Parent = boardGui

local titleLbl = Instance.new("TextLabel")
titleLbl.Size = UDim2.new(1, -40, 0.13, 0)
titleLbl.Position = UDim2.new(0, 20, 0.03, 0)
titleLbl.BackgroundTransparency = 1
titleLbl.Text = "INTERPLANETARY FLIGHT SIMULATION"
titleLbl.TextColor3 = PALETTE.White
titleLbl.Font = Enum.Font.GothamBlack
titleLbl.TextScaled = true
titleLbl.Parent = boardRoot

local subLbl = Instance.new("TextLabel")
subLbl.Size = UDim2.new(1, -40, 0.07, 0)
subLbl.Position = UDim2.new(0, 20, 0.16, 0)
subLbl.BackgroundTransparency = 1
subLbl.Text = "CLICK THE BOARD TO OPEN THE 3 SIMULATIONS"
subLbl.TextColor3 = PALETTE.Dim
subLbl.Font = Enum.Font.GothamBold
subLbl.TextScaled = true
subLbl.Parent = boardRoot

local routeRow = Instance.new("Frame")
routeRow.Size = UDim2.new(0.9, 0, 0.34, 0)
routeRow.Position = UDim2.new(0.05, 0, 0.26, 0)
routeRow.BackgroundTransparency = 1
routeRow.Parent = boardRoot

local trackLine = Instance.new("Frame")
trackLine.Size = UDim2.new(0.76, 0, 0, 4)
trackLine.Position = UDim2.new(0.12, 0, 0.52, 0)
trackLine.BackgroundColor3 = PALETTE.CyanNeon
trackLine.BorderSizePixel = 0
trackLine.Parent = routeRow

local bodies = {
	{Name = "EARTH", X = 0.12, Sz = 78, Col = Color3.fromRGB(55, 145, 235)},
	{Name = "MOON",  X = 0.42, Sz = 34, Col = Color3.fromRGB(190, 195, 205)},
	{Name = "MARS",  X = 0.66, Sz = 64, Col = Color3.fromRGB(220, 105, 65)},
	{Name = "CERES", X = 0.88, Sz = 52, Col = Color3.fromRGB(145, 145, 155)}
}

for _, b in ipairs(bodies) do
	local orb = Instance.new("Frame")
	orb.Size = UDim2.new(0, b.Sz, 0, b.Sz)
	orb.AnchorPoint = Vector2.new(0.5, 0.5)
	orb.Position = UDim2.new(b.X, 0, 0.52, 0)
	orb.BackgroundColor3 = b.Col
	orb.Parent = routeRow
	Instance.new("UICorner", orb).CornerRadius = UDim.new(1, 0)

	local bLbl = Instance.new("TextLabel")
	bLbl.Size = UDim2.new(0, 110, 0, 24)
	bLbl.AnchorPoint = Vector2.new(0.5, 1)
	bLbl.Position = UDim2.new(b.X, 0, 0.16, 0)
	bLbl.BackgroundTransparency = 1
	bLbl.Text = b.Name
	bLbl.TextColor3 = PALETTE.White
	bLbl.Font = Enum.Font.GothamBlack
	bLbl.TextScaled = true
	bLbl.Parent = routeRow
end

local boardButtons = {} -- [Key] = {Label = TextLabel, Col = Color3}
local btnConfigs = {
	{Id = 1, Key = "Game1_Velocity",   Label = "1. MAX VELOCITY", Col = PALETTE.CyanNeon},
	{Id = 2, Key = "Game2_Trajectory", Label = "2. TRAJECTORY",   Col = PALETTE.GoldNeon},
	{Id = 3, Key = "Game3_Orbit",      Label = "3. POLAR ORBIT",  Col = PALETTE.GreenOk}
}

for idx, cfg in ipairs(btnConfigs) do
	local btnFrame = Instance.new("Frame")
	btnFrame.Size = UDim2.new(0.28, 0, 0.24, 0)
	btnFrame.Position = UDim2.new(0.05 + (idx - 1) * 0.31, 0, 0.67, 0)
	btnFrame.BackgroundColor3 = PALETTE.PanelSlate
	btnFrame.Parent = boardRoot
	Instance.new("UICorner", btnFrame).CornerRadius = UDim.new(0, 10)
	local st = Instance.new("UIStroke", btnFrame)
	st.Color = cfg.Col
	st.Thickness = 2.5

	local bTitle = Instance.new("TextLabel")
	bTitle.Size = UDim2.new(1, -12, 0.48, 0)
	bTitle.Position = UDim2.new(0, 6, 0.06, 0)
	bTitle.BackgroundTransparency = 1
	bTitle.Text = cfg.Label
	bTitle.TextColor3 = PALETTE.White
	bTitle.Font = Enum.Font.GothamBlack
	bTitle.TextScaled = true
	bTitle.Parent = btnFrame

	local bStatus = Instance.new("TextLabel")
	bStatus.Size = UDim2.new(0.88, 0, 0.34, 0)
	bStatus.Position = UDim2.new(0.06, 0, 0.56, 0)
	bStatus.BackgroundColor3 = Color3.fromRGB(22, 34, 50)
	bStatus.Text = "CLICK TO PLAY ▶"
	bStatus.TextColor3 = cfg.Col
	bStatus.Font = Enum.Font.GothamBold
	bStatus.TextScaled = true
	bStatus.Parent = btnFrame
	Instance.new("UICorner", bStatus).CornerRadius = UDim.new(0, 6)

	boardButtons[cfg.Key] = {Label = bStatus, Col = cfg.Col}
end

local boardClick = Instance.new("ClickDetector")
boardClick.MaxActivationDistance = 26
boardClick.Parent = mainSimBoard

local boardPrompt = Instance.new("ProximityPrompt")
boardPrompt.ObjectText = "Simulation Control Board"
boardPrompt.ActionText = "Open 3 Simulation Minigames"
boardPrompt.HoldDuration = 0
boardPrompt.MaxActivationDistance = 15
boardPrompt.Parent = mainSimBoard

local function openBoardForPlayer(player)
	if not canUseConsole(player) then
		OpenSimEvent:FireClient(player, "ACCESS_DENIED")
		return
	end
	OpenSimEvent:FireClient(player, "OPEN_HUB", simProgress, buildInfo())
end

boardClick.MouseClick:Connect(openBoardForPlayer)
boardPrompt.Triggered:Connect(openBoardForPlayer)

-- =========================================================================
-- 3. LEFT WALL SCREEN: INFO
-- =========================================================================

makePart({
	Name = "LeftWallVelFrame",
	Size = Vector3.new(0.35, 8.6, 13.8),
	Position = getWingPos(4.0, -20.65, 10.2),
	Material = Enum.Material.Neon,
	Color = PALETTE.CyanNeon
})

local leftVelScreen = makePart({
	Name = "LeftWallVelScreen",
	Size = Vector3.new(0.2, 7.6, 12.8),
	Position = getWingPos(4.0, -20.4, 10.2),
	Material = Enum.Material.SmoothPlastic,
	Color = PALETTE.ScreenBg
})

local leftGui = Instance.new("SurfaceGui")
leftGui.Name = "LeftVelocityGui"
leftGui.Face = Enum.NormalId.Right
leftGui.LightInfluence = 0
leftGui.Parent = leftVelScreen

local leftBg = Instance.new("Frame")
leftBg.Size = UDim2.new(1, 0, 1, 0)
leftBg.BackgroundColor3 = PALETTE.ScreenBg
leftBg.Parent = leftGui

local function leftLabel(text, color, font, y, h)
	local l = Instance.new("TextLabel")
	l.Size = UDim2.new(1, -30, h, 0)
	l.Position = UDim2.new(0, 15, y, 0)
	l.BackgroundTransparency = 1
	l.Text = text
	l.TextColor3 = color
	l.Font = font
	l.TextScaled = true
	l.TextXAlignment = Enum.TextXAlignment.Left
	l.Parent = leftBg
	return l
end

leftLabel("INFO", PALETTE.Dim, Enum.Font.GothamBlack, 0.06, 0.16)
leftLabel("MAX VELOCITY", PALETTE.GoldNeon, Enum.Font.GothamBlack, 0.24, 0.14)
local leftVelValue = leftLabel("AWAITING SIM 1...", PALETTE.CyanNeon, Enum.Font.GothamBlack, 0.40, 0.28)
local leftLinkStatus = leftLabel("SPACECRAFT SHEET SYNC: 0 / 3 COMPLETED", PALETTE.White, Enum.Font.Code, 0.72, 0.20)

-- =========================================================================
-- 4. RIGHT WALL SCREEN: MISSION CALCULATIONS
-- =========================================================================

makePart({
	Name = "RightWallCalcFrame",
	Size = Vector3.new(0.35, 9.4, 15.2),
	Position = getWingPos(4.0, 20.65, 10.2),
	Material = Enum.Material.Neon,
	Color = PALETTE.CyanNeon
})

local rightCalcScreen = makePart({
	Name = "RightWallCalcScreen",
	Size = Vector3.new(0.2, 8.4, 14.2),
	Position = getWingPos(4.0, 20.4, 10.2),
	Material = Enum.Material.SmoothPlastic,
	Color = PALETTE.ScreenBg
})

local rightGui = Instance.new("SurfaceGui")
rightGui.Name = "MissionCalculationsGui"
rightGui.Face = Enum.NormalId.Left
rightGui.LightInfluence = 0
rightGui.Parent = rightCalcScreen

local rBg = Instance.new("Frame")
rBg.Size = UDim2.new(1, 0, 1, 0)
rBg.BackgroundColor3 = PALETTE.ScreenBg
rBg.Parent = rightGui

local rTitle = Instance.new("TextLabel")
rTitle.Size = UDim2.new(1, -24, 0.11, 0)
rTitle.Position = UDim2.new(0, 12, 0.03, 0)
rTitle.BackgroundTransparency = 1
rTitle.Text = "MISSION CALCULATIONS"
rTitle.TextColor3 = PALETTE.White
rTitle.Font = Enum.Font.GothamBlack
rTitle.TextScaled = true
rTitle.TextXAlignment = Enum.TextXAlignment.Left
rTitle.Parent = rBg

local velSection = Instance.new("Frame")
velSection.Size = UDim2.new(1, -24, 0.42, 0)
velSection.Position = UDim2.new(0, 12, 0.16, 0)
velSection.BackgroundColor3 = PALETTE.PanelSlate
velSection.Parent = rBg
Instance.new("UICorner", velSection).CornerRadius = UDim.new(0, 8)

local velSecHdr = Instance.new("TextLabel")
velSecHdr.Size = UDim2.new(0.62, 0, 0.22, 0)
velSecHdr.Position = UDim2.new(0.03, 0, 0.05, 0)
velSecHdr.BackgroundTransparency = 1
velSecHdr.Text = "VELOCITY COMPUTATION"
velSecHdr.TextColor3 = PALETTE.Dim
velSecHdr.Font = Enum.Font.GothamBold
velSecHdr.TextScaled = true
velSecHdr.TextXAlignment = Enum.TextXAlignment.Left
velSecHdr.Parent = velSection

local velCurrentLbl = Instance.new("TextLabel")
velCurrentLbl.Size = UDim2.new(0.62, 0, 0.34, 0)
velCurrentLbl.Position = UDim2.new(0.03, 0, 0.28, 0)
velCurrentLbl.BackgroundTransparency = 1
velCurrentLbl.Text = "MAX VELOCITY: LOCKED"
velCurrentLbl.TextColor3 = PALETTE.GoldNeon
velCurrentLbl.Font = Enum.Font.GothamBlack
velCurrentLbl.TextScaled = true
velCurrentLbl.TextXAlignment = Enum.TextXAlignment.Left
velCurrentLbl.Parent = velSection

local velVecLbl = Instance.new("TextLabel")
velVecLbl.Size = UDim2.new(0.64, 0, 0.26, 0)
velVecLbl.Position = UDim2.new(0.03, 0, 0.66, 0)
velVecLbl.BackgroundTransparency = 1
velVecLbl.Text = "[Vx: 12.1]  [Vy: -8.4]  [Vz: 19.9]"
velVecLbl.TextColor3 = PALETTE.White
velVecLbl.Font = Enum.Font.Code
velVecLbl.TextScaled = true
velVecLbl.TextXAlignment = Enum.TextXAlignment.Left
velVecLbl.Parent = velSection

local graphBox = Instance.new("Frame")
graphBox.Size = UDim2.new(0.30, 0, 0.78, 0)
graphBox.Position = UDim2.new(0.67, 0, 0.11, 0)
graphBox.BackgroundColor3 = PALETTE.ScreenBg
graphBox.Parent = velSection
Instance.new("UIStroke", graphBox).Color = PALETTE.CyanNeon

local curveHeights = {0.20, 0.32, 0.45, 0.60, 0.76, 0.92}
for i, h in ipairs(curveHeights) do
	local bar = Instance.new("Frame")
	bar.Size = UDim2.new(0.12, 0, h, 0)
	bar.Position = UDim2.new(0.06 + (i - 1) * 0.15, 0, 1 - h, 0)
	bar.BackgroundColor3 = (i >= 5) and PALETTE.GreenOk or PALETTE.CyanNeon
	bar.BorderSizePixel = 0
	bar.Parent = graphBox
end

local pathSection = Instance.new("Frame")
pathSection.Size = UDim2.new(1, -24, 0.36, 0)
pathSection.Position = UDim2.new(0, 12, 0.61, 0)
pathSection.BackgroundColor3 = PALETTE.PanelSlate
pathSection.Parent = rBg
Instance.new("UICorner", pathSection).CornerRadius = UDim.new(0, 8)

local pathHdr = Instance.new("TextLabel")
pathHdr.Size = UDim2.new(0.55, 0, 0.24, 0)
pathHdr.Position = UDim2.new(0.03, 0, 0.06, 0)
pathHdr.BackgroundTransparency = 1
pathHdr.Text = "PATH LENGTH COMPUTATION"
pathHdr.TextColor3 = PALETTE.Dim
pathHdr.Font = Enum.Font.GothamBold
pathHdr.TextScaled = true
pathHdr.TextXAlignment = Enum.TextXAlignment.Left
pathHdr.Parent = pathSection

local plannedLbl = Instance.new("TextLabel")
plannedLbl.Size = UDim2.new(0.40, 0, 0.24, 0)
plannedLbl.Position = UDim2.new(0.57, 0, 0.06, 0)
plannedLbl.BackgroundTransparency = 1
plannedLbl.Text = "PLANNED: 2.1 x 10⁸ km"
plannedLbl.TextColor3 = PALETTE.GoldNeon
plannedLbl.Font = Enum.Font.GothamBlack
plannedLbl.TextScaled = true
plannedLbl.TextXAlignment = Enum.TextXAlignment.Right
plannedLbl.Parent = pathSection

local pathBarBg = Instance.new("Frame")
pathBarBg.Size = UDim2.new(0.94, 0, 0.22, 0)
pathBarBg.Position = UDim2.new(0.03, 0, 0.38, 0)
pathBarBg.BackgroundColor3 = PALETTE.ScreenBg
pathBarBg.Parent = pathSection
Instance.new("UICorner", pathBarBg).CornerRadius = UDim.new(0, 6)

local pathBarFill = Instance.new("Frame")
pathBarFill.Size = UDim2.new(0.35, 0, 1, 0)
pathBarFill.BackgroundColor3 = PALETTE.GoldNeon
pathBarFill.BorderSizePixel = 0
pathBarFill.Parent = pathBarBg
Instance.new("UICorner", pathBarFill).CornerRadius = UDim.new(0, 6)

local traveledLbl = Instance.new("TextLabel")
traveledLbl.Size = UDim2.new(0.46, 0, 0.24, 0)
traveledLbl.Position = UDim2.new(0.03, 0, 0.68, 0)
traveledLbl.BackgroundTransparency = 1
traveledLbl.Text = "TRAVELED: 1.4 x 10⁷ km"
traveledLbl.TextColor3 = PALETTE.White
traveledLbl.Font = Enum.Font.Code
traveledLbl.TextScaled = true
traveledLbl.TextXAlignment = Enum.TextXAlignment.Left
traveledLbl.Parent = pathSection

local remainingLbl = Instance.new("TextLabel")
remainingLbl.Size = UDim2.new(0.48, 0, 0.24, 0)
remainingLbl.Position = UDim2.new(0.49, 0, 0.68, 0)
remainingLbl.BackgroundTransparency = 1
remainingLbl.Text = "REMAINING: 1.96 x 10⁸ km"
remainingLbl.TextColor3 = PALETTE.CyanNeon
remainingLbl.Font = Enum.Font.Code
remainingLbl.TextScaled = true
remainingLbl.TextXAlignment = Enum.TextXAlignment.Right
remainingLbl.Parent = pathSection

-- =========================================================================
-- 5. ACTUALIZAR PANTALLAS Y ENVIAR A SPACECRAFT
-- =========================================================================

local function refreshScreens()
	local doneCount = 0
	for key, done in pairs(simProgress) do
		if done then
			doneCount += 1
			local b = boardButtons[key]
			b.Label.Text = "COMPLETED ✓"
			b.Label.BackgroundColor3 = Color3.fromRGB(14, 72, 54)
			b.Label.TextColor3 = PALETTE.GreenOk
		end
	end

	if simProgress.Game1_Velocity then
		leftVelValue.Text = PLACEHOLDERS.MaxVelocity
		leftVelValue.TextColor3 = PALETTE.GreenOk
		velCurrentLbl.Text = "MAX: " .. PLACEHOLDERS.MaxVelocityNumeric .. " km/s"
		velCurrentLbl.TextColor3 = PALETTE.GreenOk
	end
	leftLinkStatus.Text = string.format("SPACECRAFT SHEET SYNC: %d / 3 COMPLETED", doneCount)

	if simProgress.Game2_Trajectory then
		pathBarFill.Size = UDim2.new(0.72, 0, 1, 0)
		traveledLbl.Text = "TRAVELED: 1.51 x 10⁸ km"
		remainingLbl.Text = "REMAINING: 0.59 x 10⁸ km"
	end
	if simProgress.Game3_Orbit then
		pathBarFill.Size = UDim2.new(1.0, 0, 1, 0)
		pathBarFill.BackgroundColor3 = PALETTE.GreenOk
		traveledLbl.Text = "TRAVELED: 2.10 x 10⁸ km"
		remainingLbl.Text = "ORBIT ACHIEVED ✓"
	end
end

local function syncAllClients()
	local info = buildInfo()
	for _, plr in ipairs(Players:GetPlayers()) do
		SyncSimStateEvent:FireClient(plr, simProgress, info)
	end
end

CompleteMinigameEvent.OnServerEvent:Connect(function(player, gameId, mode)
	if not canUseConsole(player) then return end
	gameId = tonumber(gameId)
	if not gameId or not GAME_KEY[gameId] then return end

	-- El cliente avisa cuando empieza a jugar (para validar que paso el tiempo minimo)
	if mode == "START" then
		playStarts[player] = playStarts[player] or {}
		playStarts[player][gameId] = os.clock()
		return
	end

	local started = playStarts[player] and playStarts[player][gameId]
	if not started or (os.clock() - started) < MIN_PLAY_SECONDS[gameId] then
		return
	end
	playStarts[player][gameId] = nil

	-- Estos atributos los lee SpacecraftStationManager
	if gameId == 1 and not simProgress.Game1_Velocity then
		simProgress.Game1_Velocity = true
		workspace:SetAttribute("PhysicistMaxVelocityText", PLACEHOLDERS.MaxVelocity)
		workspace:SetAttribute("PhysicistMaxVelocity", PLACEHOLDERS.MaxVelocityNumeric)

	elseif gameId == 2 and not simProgress.Game2_Trajectory then
		simProgress.Game2_Trajectory = true
		workspace:SetAttribute("PhysicistTrajectoryText", PLACEHOLDERS.Trajectory)

	elseif gameId == 3 and not simProgress.Game3_Orbit then
		simProgress.Game3_Orbit = true
		workspace:SetAttribute("PhysicistOrbitText", PLACEHOLDERS.Orbit)
	end

	refreshScreens()
	syncAllClients()
end)

Players.PlayerRemoving:Connect(function(plr)
	playStarts[plr] = nil
end)

-- =========================================================================
-- 6. RESET AL TERMINAR LA MISION (Monitoring pone MissionStarted = false)
-- =========================================================================

local function resetSimulation()
	for key in pairs(simProgress) do
		simProgress[key] = false
	end
	playStarts = {}

	for _, attr in ipairs({
		"PhysicistMaxVelocityText", "PhysicistMaxVelocity",
		"PhysicistTrajectoryText", "PhysicistOrbitText",
		}) do
		workspace:SetAttribute(attr, nil)
	end

	for _, b in pairs(boardButtons) do
		b.Label.Text = "CLICK TO PLAY ▶"
		b.Label.BackgroundColor3 = Color3.fromRGB(22, 34, 50)
		b.Label.TextColor3 = b.Col
	end

	leftVelValue.Text = "AWAITING SIM 1..."
	leftVelValue.TextColor3 = PALETTE.CyanNeon
	velCurrentLbl.Text = "MAX VELOCITY: LOCKED"
	velCurrentLbl.TextColor3 = PALETTE.GoldNeon
	pathBarFill.Size = UDim2.new(0.35, 0, 1, 0)
	pathBarFill.BackgroundColor3 = PALETTE.GoldNeon
	traveledLbl.Text = "TRAVELED: 1.4 x 10⁷ km"
	remainingLbl.Text = "REMAINING: 1.96 x 10⁸ km"
	leftLinkStatus.Text = "SPACECRAFT SHEET SYNC: 0 / 3 COMPLETED"

	syncAllClients()
end

workspace:GetAttributeChangedSignal("MissionStarted"):Connect(function()
	if workspace:GetAttribute("MissionStarted") == false then
		resetSimulation()
	end
end)

refreshScreens()

```

> **Artifact: SimulationRoomClient.lua** (Write the SimulationRoomClient with the hub and the 3 minigames)

```lua
-- =========================================================================
-- SIMULATION ROOM CLIENT: hub + 3 minijuegos
-- Location: StarterPlayer > StarterPlayerScripts > SimulationRoomClient (LocalScript)
--
-- Click the main board of the Simulation Hall -> hub with 3 minigames:
--   1. MAX VELOCITY : keep the ship inside the green zone (SPACE / THRUST button)
--   2. TRAJECTORY   : dodge the asteroids (WASD / arrows / on-screen buttons)
--   3. POLAR ORBIT  : pull back and release the ship to get captured into orbit
-- Winning a game tells the server (CompleteMinigame), which sends the info to the
-- Spacecraft station sheet.
-- =========================================================================

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ContextActionService = game:GetService("ContextActionService")

local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")

local SimRemotes = ReplicatedStorage:WaitForChild("SimulationRemotes")
local OpenSimEvent = SimRemotes:WaitForChild("OpenSimulationStation")
local CompleteMinigameEvent = SimRemotes:WaitForChild("CompleteMinigame")
local SyncSimStateEvent = SimRemotes:WaitForChild("SyncSimulationState")

-- Imagenes opcionales ("rbxassetid://ID"). Vacio = dibujo simple hecho con Frames/texto.
-- La nave debe estar dibujada APUNTANDO A LA DERECHA.
local ASSETS = {
	Ship = "",
	Asteroid = "",
	Planet = "",
}

local C = {
	Bg      = Color3.fromRGB(8, 16, 26),
	Panel   = Color3.fromRGB(14, 24, 38),
	Panel2  = Color3.fromRGB(22, 34, 50),
	Cyan    = Color3.fromRGB(0, 210, 255),
	Gold    = Color3.fromRGB(255, 200, 45),
	Green   = Color3.fromRGB(0, 230, 145),
	White   = Color3.fromRGB(242, 248, 255),
	Dim     = Color3.fromRGB(155, 215, 235),
	Red     = Color3.fromRGB(255, 85, 95),
	Gray    = Color3.fromRGB(70, 82, 96),
}

local GAMES = {
	{Id = 1, Key = "Game1_Velocity", Title = "MAX VELOCITY", Color = C.Cyan,
		Desc = "Keep the ship inside the green zone for 10 seconds. Tap or hold SPACE to thrust. Without thrust the ship drifts to the left!",
		Intro = "Keep the ship inside the GREEN ZONE for 10 seconds.\nTap or hold SPACE (or the THRUST button) to push the ship forward.\nWithout thrust it slowly drifts to the left. Don't leave the corridor!"},
	{Id = 2, Key = "Game2_Trajectory", Title = "TRAJECTORY", Color = C.Gold,
		Desc = "Fly through the asteroid field and survive 25 seconds. You have 3 lives. Move with WASD or the arrow keys.",
		Intro = "Survive 25 seconds in the asteroid field.\nMove with WASD / arrow keys (or the on-screen arrows).\nYou have 3 lives."},
	{Id = 3, Key = "Game3_Orbit", Title = "POLAR ORBIT", Color = C.Green,
		Desc = "Pull the ship back and release it to launch. Enter the capture ring slowly enough to be pulled into a polar orbit.",
		Intro = "Drag the ship backwards and release to launch it.\nReach the CAPTURE RING around the planet with a speed in the GREEN range of the power meter to be captured into a near-circular polar orbit."},
}

local WIN_TEXT = {
	[1] = "MAX VELOCITY REACHED",
	[2] = "TRAJECTORY CLEARED",
	[3] = "POLAR ORBIT ACHIEVED",
}

-- Mundo virtual de los minijuegos (16:9); todo se escala al tamano de la pantalla
local W, H = 1600, 900

-- =========================================================================
-- HELPERS
-- =========================================================================
local function new(class, props, parent)
	local o = Instance.new(class)
	for k, v in pairs(props) do
		o[k] = v
	end
	o.Parent = parent
	return o
end

local function round(o, r)
	new("UICorner", {CornerRadius = UDim.new(0, r or 8)}, o)
end

local function circle(o)
	new("UICorner", {CornerRadius = UDim.new(0.5, 0)}, o)
end

-- Coloca un objeto con coordenadas virtuales (centro x, y y tamano w, h)
local function place(obj, x, y, w, h)
	obj.AnchorPoint = Vector2.new(0.5, 0.5)
	obj.Position = UDim2.fromScale(x / W, y / H)
	if w then
		obj.Size = UDim2.fromScale(w / W, h / H)
	end
end

-- =========================================================================
-- ESTADO
-- =========================================================================
local progress = {}  -- [Key] = true cuando el minijuego esta completado
local infos = {}     -- ["1"] = texto de info enviado a Spacecraft
local view = nil     -- "HUB" | "GAME" | nil
local currentSession = nil

local showHub, startGame, stopGame, clearContent

-- =========================================================================
-- VENTANA PRINCIPAL
-- =========================================================================
local gui = new("ScreenGui", {
	Name = "SimulationUI", ResetOnSpawn = false, DisplayOrder = 15,
	ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
}, playerGui)

local toastLbl = new("TextLabel", {
	Size = UDim2.new(0, 520, 0, 48), AnchorPoint = Vector2.new(0.5, 0), Position = UDim2.new(0.5, 0, 0.07, 0),
	BackgroundColor3 = C.Red, Visible = false, Text = "", TextColor3 = C.White,
	Font = Enum.Font.GothamBold, TextScaled = true, ZIndex = 80,
}, gui)
round(toastLbl, 10)
new("UITextSizeConstraint", {MaxTextSize = 22, MinTextSize = 10}, toastLbl)
local toastToken = 0

local function toast(text)
	toastToken += 1
	local mine = toastToken
	toastLbl.Text = "  " .. text .. "  "
	toastLbl.Visible = true
	task.delay(2.8, function()
		if toastToken == mine then toastLbl.Visible = false end
	end)
end

local backdrop = new("TextButton", {
	Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.fromRGB(0, 0, 0), BackgroundTransparency = 0.45,
	BorderSizePixel = 0, Text = "", AutoButtonColor = false, Visible = false, ZIndex = 9,
}, gui)

local modal = new("Frame", {
	AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromScale(0.92, 0.92),
	BackgroundColor3 = C.Bg, Visible = false, ZIndex = 10,
}, gui)
new("UISizeConstraint", {MaxSize = Vector2.new(1200, 760)}, modal)
round(modal, 14)
new("UIStroke", {Color = C.Gold, Thickness = 2.5}, modal)

local titleLbl = new("TextLabel", {
	Size = UDim2.new(1, -170, 0, 36), Position = UDim2.new(0, 18, 0, 10), BackgroundTransparency = 1,
	Text = "INTERPLANETARY FLIGHT SIMULATION", TextColor3 = C.White, Font = Enum.Font.GothamBlack,
	TextScaled = true, TextXAlignment = Enum.TextXAlignment.Left,
}, modal)

local closeBtn = new("TextButton", {
	Size = UDim2.new(0, 120, 0, 36), Position = UDim2.new(1, -138, 0, 10), BackgroundColor3 = C.Red,
	Text = "CLOSE [X]", TextColor3 = C.White, Font = Enum.Font.GothamBlack, TextScaled = true,
}, modal)
round(closeBtn, 8)

local content = new("Frame", {
	Size = UDim2.new(1, -36, 1, -64), Position = UDim2.new(0, 18, 0, 54), BackgroundTransparency = 1,
}, modal)

local function closeWindow()
	stopGame()
	clearContent()
	modal.Visible = false
	backdrop.Visible = false
	view = nil
end
closeBtn.MouseButton1Click:Connect(closeWindow)

stopGame = function()
	if currentSession then
		currentSession:Stop()
		currentSession = nil
	end
end

clearContent = function()
	stopGame()
	for _, ch in ipairs(content:GetChildren()) do
		ch:Destroy()
	end
end

-- =========================================================================
-- HUB
-- =========================================================================
showHub = function()
	clearContent()
	view = "HUB"
	titleLbl.Text = "INTERPLANETARY FLIGHT SIMULATION"

	local doneCount = 0
	for _, g in ipairs(GAMES) do
		if progress[g.Key] then doneCount += 1 end
	end

	local sub = new("TextLabel", {
		Size = UDim2.new(1, 0, 0.08, 0), BackgroundTransparency = 1,
		Text = "CHOOSE A SIMULATION. EACH COMPLETED ONE SENDS ITS RESULT TO THE SPACECRAFT STATION SHEET",
		TextColor3 = C.Dim, Font = Enum.Font.GothamBold, TextScaled = true,
	}, content)
	new("UITextSizeConstraint", {MaxTextSize = 20, MinTextSize = 8}, sub)

	for i, g in ipairs(GAMES) do
		local done = progress[g.Key] == true

		local card = new("Frame", {
			Size = UDim2.new(0.32, 0, 0.78, 0), Position = UDim2.new((i - 1) * 0.34, 0, 0.11, 0),
			BackgroundColor3 = C.Panel,
		}, content)
		round(card, 12)
		new("UIStroke", {Color = g.Color, Thickness = 2.5}, card)

		new("TextLabel", {
			Size = UDim2.new(1, -20, 0.12, 0), Position = UDim2.new(0, 10, 0.03, 0), BackgroundTransparency = 1,
			Text = i .. ". " .. g.Title, TextColor3 = C.White, Font = Enum.Font.GothamBlack, TextScaled = true,
		}, card)

		local desc = new("TextLabel", {
			Size = UDim2.new(1, -24, 0.30, 0), Position = UDim2.new(0, 12, 0.17, 0), BackgroundTransparency = 1,
			Text = g.Desc, TextColor3 = C.Dim, Font = Enum.Font.GothamSemibold, TextScaled = true, TextWrapped = true,
			TextXAlignment = Enum.TextXAlignment.Left, TextYAlignment = Enum.TextYAlignment.Top,
		}, card)
		new("UITextSizeConstraint", {MaxTextSize = 18, MinTextSize = 8}, desc)

		local chip = new("TextLabel", {
			Size = UDim2.new(0.84, 0, 0.08, 0), Position = UDim2.new(0.08, 0, 0.52, 0),
			BackgroundColor3 = done and Color3.fromRGB(14, 72, 54) or C.Panel2,
			Text = done and "COMPLETED ✓" or "NOT COMPLETED", TextColor3 = done and C.Green or C.Dim,
			Font = Enum.Font.GothamBold, TextScaled = true,
		}, card)
		round(chip, 6)

		local infoLbl = new("TextLabel", {
			Size = UDim2.new(0.9, 0, 0.16, 0), Position = UDim2.new(0.05, 0, 0.62, 0), BackgroundTransparency = 1,
			Text = done and ("SENT TO SPACECRAFT:\n" .. (infos[tostring(g.Id)] or "")) or "Result is sent to the Spacecraft sheet when you win.",
			TextColor3 = done and C.White or C.Gray, Font = Enum.Font.GothamSemibold, TextScaled = true, TextWrapped = true,
		}, card)
		new("UITextSizeConstraint", {MaxTextSize = 16, MinTextSize = 8}, infoLbl)

		local play = new("TextButton", {
			Size = UDim2.new(0.8, 0, 0.11, 0), Position = UDim2.new(0.1, 0, 0.85, 0), BackgroundColor3 = g.Color,
			Text = done and "REPLAY ▶" or "PLAY ▶", TextColor3 = Color3.fromRGB(10, 20, 30),
			Font = Enum.Font.GothamBlack, TextScaled = true,
		}, card)
		round(play, 10)
		play.MouseButton1Click:Connect(function()
			startGame(g.Id)
		end)
	end

	new("TextLabel", {
		Size = UDim2.new(1, 0, 0.06, 0), Position = UDim2.new(0, 0, 0.93, 0), BackgroundTransparency = 1,
		Text = string.format("%d / 3 COMPLETED", doneCount), TextColor3 = (doneCount == 3) and C.Green or C.Gold,
		Font = Enum.Font.GothamBlack, TextScaled = true,
	}, content)
end

-- =========================================================================
-- SESION DE JUEGO (escenario 16:9, overlays, entrada, bucle)
-- =========================================================================
local function makeShip(parent)
	local holder = new("Frame", {BackgroundTransparency = 1, ZIndex = 6}, parent)
	if ASSETS.Ship ~= "" then
		new("ImageLabel", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Image = ASSETS.Ship,
			ScaleType = Enum.ScaleType.Fit, ZIndex = 6,
		}, holder)
	else
		new("TextLabel", {
			Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "▶", TextColor3 = C.Cyan,
			Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 6,
		}, holder)
	end
	local flame = new("Frame", {
		Size = UDim2.fromScale(0.35, 0.22), Position = UDim2.fromScale(-0.3, 0.39),
		BackgroundColor3 = Color3.fromRGB(255, 150, 40), BorderSizePixel = 0, Visible = false, ZIndex = 5,
	}, holder)
	round(flame, 6)
	return holder, flame
end

local function makeAsteroid(parent)
	if ASSETS.Asteroid ~= "" then
		return new("ImageLabel", {
			BackgroundTransparency = 1, Image = ASSETS.Asteroid, ScaleType = Enum.ScaleType.Fit, ZIndex = 4,
		}, parent)
	end
	local f = new("Frame", {BackgroundColor3 = Color3.fromRGB(120, 115, 110), BorderSizePixel = 0, ZIndex = 4}, parent)
	circle(f)
	new("UIStroke", {Color = Color3.fromRGB(60, 56, 52), Thickness = 3}, f)
	return f
end

local function makePlanet(parent)
	if ASSETS.Planet ~= "" then
		return new("ImageLabel", {
			BackgroundTransparency = 1, Image = ASSETS.Planet, ScaleType = Enum.ScaleType.Fit, ZIndex = 3,
		}, parent)
	end
	local f = new("Frame", {BackgroundColor3 = Color3.fromRGB(220, 105, 65), BorderSizePixel = 0, ZIndex = 3}, parent)
	circle(f)
	new("UIStroke", {Color = Color3.fromRGB(255, 170, 120), Thickness = 3}, f)
	return f
end

local function newSession(game)
	clearContent()
	view = "GAME"
	titleLbl.Text = "SIMULATION " .. game.Id .. ": " .. game.Title

	local holder = new("Frame", {Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1}, content)
	local stage = new("Frame", {
		AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromScale(1, 1),
		BackgroundColor3 = Color3.fromRGB(4, 8, 16), BorderSizePixel = 0, ClipsDescendants = true,
	}, holder)
	new("UIAspectRatioConstraint", {AspectRatio = W / H}, stage)
	round(stage, 10)
	new("UIStroke", {Color = game.Color, Thickness = 2}, stage)

	-- campo de estrellas fijo
	for _ = 1, 70 do
		local sz = math.random(2, 4)
		new("Frame", {
			Size = UDim2.new(0, sz, 0, sz), Position = UDim2.fromScale(math.random(), math.random()),
			BackgroundColor3 = C.White, BackgroundTransparency = math.random() * 0.6, BorderSizePixel = 0, ZIndex = 1,
		}, stage)
	end

	local s = {
		Game = game, Id = game.Id, Stage = stage, Conns = {}, Keys = {}, Hold = {},
		Running = false, Over = nil, InfoLabel = nil, Update = nil,
	}
	local actionName = "SimGameSink" .. game.Id

	function s:Connect(signal, fn)
		local c = signal:Connect(fn)
		table.insert(self.Conns, c)
		return c
	end

	function s:Stop()
		self.Running = false
		for _, c in ipairs(self.Conns) do
			c:Disconnect()
		end
		self.Conns = {}
		ContextActionService:UnbindAction(actionName)
	end

	function s:HideOverlay()
		if self.Over then
			self.Over:Destroy()
			self.Over = nil
		end
		self.InfoLabel = nil
	end

	-- buttons = {{Text, Color, Fn}}; opts.Info = true muestra la info enviada a Spacecraft
	function s:Overlay(titleText, subText, color, buttons, opts)
		self:HideOverlay()
		local o = new("Frame", {
			Size = UDim2.fromScale(1, 1), BackgroundColor3 = Color3.fromRGB(0, 0, 0), BackgroundTransparency = 0.3,
			BorderSizePixel = 0, ZIndex = 50, Active = true,
		}, stage)
		new("TextLabel", {
			Size = UDim2.fromScale(0.8, 0.16), Position = UDim2.fromScale(0.1, 0.12), BackgroundTransparency = 1,
			Text = titleText, TextColor3 = color, Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 51,
		}, o)
		local sub = new("TextLabel", {
			Size = UDim2.fromScale(0.7, 0.26), Position = UDim2.fromScale(0.15, 0.30), BackgroundTransparency = 1,
			Text = subText, TextColor3 = C.White, Font = Enum.Font.GothamSemibold, TextScaled = true,
			TextWrapped = true, ZIndex = 51,
		}, o)
		new("UITextSizeConstraint", {MaxTextSize = 26, MinTextSize = 8}, sub)

		if opts and opts.Info then
			local box = new("TextLabel", {
				Size = UDim2.fromScale(0.7, 0.12), Position = UDim2.fromScale(0.15, 0.58),
				BackgroundColor3 = C.Panel, ZIndex = 51, Font = Enum.Font.GothamBold, TextScaled = true,
				TextColor3 = C.Green, Text = "",
			}, o)
			round(box, 8)
			new("UITextSizeConstraint", {MaxTextSize = 24, MinTextSize = 8}, box)
			box.Text = "SENT TO SPACECRAFT: " .. (infos[tostring(self.Id)] or "...")
			self.InfoLabel = box
		end

		local n = #buttons
		local bw = 0.26
		local gap = 0.04
		local totalW = n * bw + (n - 1) * gap
		for i, b in ipairs(buttons) do
			local btn = new("TextButton", {
				Size = UDim2.fromScale(bw, 0.12), Position = UDim2.fromScale(0.5 - totalW / 2 + (i - 1) * (bw + gap), 0.76),
				BackgroundColor3 = b.Color, Text = b.Text, TextColor3 = Color3.fromRGB(10, 20, 30),
				Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 52,
			}, o)
			round(btn, 10)
			new("UITextSizeConstraint", {MaxTextSize = 24, MinTextSize = 8}, btn)
			btn.MouseButton1Click:Connect(b.Fn)
		end
		self.Over = o
	end

	function s:Begin()
		self:HideOverlay()
		self.Running = true
		CompleteMinigameEvent:FireServer(self.Id, "START")
	end

	function s:Win()
		if not self.Running then return end
		self.Running = false
		CompleteMinigameEvent:FireServer(self.Id)
		self:Overlay("SUCCESS! " .. WIN_TEXT[self.Id], "The result was sent to the Spacecraft station's sheet of paper.", C.Green, {
			{Text = "◀ BACK TO HUB", Color = C.Cyan, Fn = function() showHub() end},
			{Text = "PLAY AGAIN", Color = C.Gold, Fn = function() startGame(self.Id) end},
		}, {Info = true})
	end

	function s:Lose(reason)
		if not self.Running then return end
		self.Running = false
		self:Overlay("MISSION FAILED", reason, C.Red, {
			{Text = "TRY AGAIN", Color = C.Green, Fn = function() startGame(self.Id) end},
			{Text = "◀ BACK TO HUB", Color = C.Cyan, Fn = function() showHub() end},
		})
	end

	function s:StartScreen()
		self:Overlay(self.Game.Title, self.Game.Intro, self.Game.Color, {
			{Text = "START ▶", Color = C.Green, Fn = function() self:Begin() end},
		})
	end

	-- HUD comun: boton para volver al hub
	local back = new("TextButton", {
		Size = UDim2.new(0, 110, 0, 32), Position = UDim2.new(0, 12, 0, 10), BackgroundColor3 = C.Panel2,
		Text = "◀ HUB", TextColor3 = C.White, Font = Enum.Font.GothamBold, TextScaled = true, ZIndex = 30,
	}, stage)
	round(back, 8)
	new("UITextSizeConstraint", {MaxTextSize = 18, MinTextSize = 8}, back)
	back.MouseButton1Click:Connect(function()
		showHub()
	end)

	-- Entrada de teclado + soltar botones en pantalla
	s:Connect(UserInputService.InputBegan, function(input)
		if UserInputService:GetFocusedTextBox() then return end
		if input.UserInputType == Enum.UserInputType.Keyboard then
			s.Keys[input.KeyCode] = true
		end
	end)
	s:Connect(UserInputService.InputEnded, function(input)
		if input.UserInputType == Enum.UserInputType.Keyboard then
			s.Keys[input.KeyCode] = nil
		elseif input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			table.clear(s.Hold)
		end
	end)

	-- Evita que el personaje salte/camine mientras se juega
	ContextActionService:BindActionAtPriority(actionName, function()
		return Enum.ContextActionResult.Sink
	end, false, Enum.ContextActionPriority.High.Value,
		Enum.KeyCode.Space, Enum.KeyCode.W, Enum.KeyCode.A, Enum.KeyCode.S, Enum.KeyCode.D,
		Enum.KeyCode.Up, Enum.KeyCode.Down, Enum.KeyCode.Left, Enum.KeyCode.Right)

	s:Connect(RunService.RenderStepped, function(dt)
		if s.Running and s.Update then
			s.Update(math.min(dt, 0.05))
		end
	end)

	-- Boton en pantalla que se mantiene presionado (mouse o touch)
	function s:HoldButton(text, name, position, size)
		local b = new("TextButton", {
			Size = size, Position = position, BackgroundColor3 = C.Panel2, BackgroundTransparency = 0.25,
			Text = text, TextColor3 = C.White, Font = Enum.Font.GothamBlack, TextScaled = true,
			AutoButtonColor = false, ZIndex = 20,
		}, stage)
		round(b, 12)
		new("UIStroke", {Color = self.Game.Color, Thickness = 2}, b)
		new("UITextSizeConstraint", {MaxTextSize = 26, MinTextSize = 8}, b)
		b.InputBegan:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
				s.Hold[name] = true
			end
		end)
		b.InputEnded:Connect(function(input)
			if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
				s.Hold[name] = nil
			end
		end)
		return b
	end

	currentSession = s
	return s
end

-- Barra de progreso simple dentro del escenario (devuelve fill, label)
local function makeBar(stage, y, color)
	local bg = new("Frame", {
		AnchorPoint = Vector2.new(0.5, 0), Size = UDim2.new(0.4, 0, 0, 16), Position = UDim2.new(0.5, 0, 0, y),
		BackgroundColor3 = C.Panel2, BorderSizePixel = 0, ZIndex = 30,
	}, stage)
	round(bg, 8)
	local fill = new("Frame", {Size = UDim2.fromScale(0, 1), BackgroundColor3 = color, BorderSizePixel = 0, ZIndex = 31}, bg)
	round(fill, 8)
	return fill
end

local function makeHudLabel(stage, y, text, color, align)
	local l = new("TextLabel", {
		AnchorPoint = Vector2.new(0.5, 0), Size = UDim2.new(0.5, 0, 0, 26), Position = UDim2.new(0.5, 0, 0, y),
		BackgroundTransparency = 1, Text = text, TextColor3 = color or C.White, Font = Enum.Font.GothamBold,
		TextScaled = true, ZIndex = 30, TextXAlignment = align or Enum.TextXAlignment.Center,
	}, stage)
	new("UITextSizeConstraint", {MaxTextSize = 22, MinTextSize = 8}, l)
	return l
end

-- =========================================================================
-- MINIJUEGO 1: MAX VELOCITY (mantener la nave en la zona)
-- =========================================================================
local function buildGame1(s)
	local stage = s.Stage
	local HOLD_TIME = 10
	local ZONE_L, ZONE_R = 0.40, 0.60
	local THRUST = 0.22
	local DRIFT = 0.07

	new("Frame", {
		Size = UDim2.fromScale(0.05, 1), BackgroundColor3 = C.Red, BackgroundTransparency = 0.7,
		BorderSizePixel = 0, ZIndex = 2,
	}, stage)
	new("Frame", {
		Size = UDim2.fromScale(0.05, 1), Position = UDim2.fromScale(0.95, 0), BackgroundColor3 = C.Red,
		BackgroundTransparency = 0.7, BorderSizePixel = 0, ZIndex = 2,
	}, stage)
	local zone = new("Frame", {
		Size = UDim2.fromScale(ZONE_R - ZONE_L, 1), Position = UDim2.fromScale(ZONE_L, 0), BackgroundColor3 = C.Green,
		BackgroundTransparency = 0.85, BorderSizePixel = 0, ZIndex = 2,
	}, stage)
	new("UIStroke", {Color = C.Green, Thickness = 2, Transparency = 0.3}, zone)
	new("TextLabel", {
		Size = UDim2.fromScale(1, 0.07), Position = UDim2.fromScale(0, 0.9), BackgroundTransparency = 1,
		Text = "TARGET ZONE", TextColor3 = C.Green, Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 3,
	}, zone)

	-- estrellas en movimiento (sensacion de velocidad)
	local streaks = {}
	for i = 1, 26 do
		local f = new("Frame", {
			Size = UDim2.new(0, math.random(20, 60), 0, 2), BackgroundColor3 = C.White, BackgroundTransparency = 0.6,
			BorderSizePixel = 0, ZIndex = 2,
		}, stage)
		streaks[i] = {Frame = f, X = math.random(), Y = math.random(), Speed = 0.2 + math.random() * 0.5}
		f.Position = UDim2.fromScale(streaks[i].X, streaks[i].Y)
	end

	local ship, flame = makeShip(stage)
	local x, t, prog = 0.5, 0, 0
	place(ship, x * W, H / 2, 130, 90)

	local bar = makeBar(stage, 46, C.Green)
	local barLbl = makeHudLabel(stage, 66, "STABILITY 0.0 / " .. HOLD_TIME .. " s", C.White)
	local velLbl = makeHudLabel(stage, 94, "VELOCITY: 0 km/hr", C.Cyan)

	local thrustBtn = s:HoldButton("THRUST  [SPACE]", "Thrust", UDim2.new(0.5, -110, 1, -70), UDim2.new(0, 220, 0, 54))

	s.Update = function(dt)
		t += dt
		local thrusting = s.Keys[Enum.KeyCode.Space] == true or s.Hold.Thrust == true
		local drift = DRIFT + 0.025 * math.sin(t * 1.3)
		x += ((thrusting and THRUST or 0) - drift) * dt

		flame.Visible = thrusting
		thrustBtn.BackgroundColor3 = thrusting and C.Green or C.Panel2
		place(ship, x * W, H / 2, 130, 90)

		for _, st in ipairs(streaks) do
			st.X -= st.Speed * dt * (thrusting and 1.6 or 1)
			if st.X < -0.1 then
				st.X = 1.05
				st.Y = math.random()
			end
			st.Frame.Position = UDim2.fromScale(st.X, st.Y)
		end

		if x >= ZONE_L and x <= ZONE_R then
			prog += dt
			ship.Rotation = 0
		else
			prog = math.max(0, prog - dt * 0.5)
		end

		local frac = math.clamp(prog / HOLD_TIME, 0, 1)
		bar.Size = UDim2.fromScale(frac, 1)
		barLbl.Text = string.format("STABILITY %.1f / %d s", prog, HOLD_TIME)
		velLbl.Text = string.format("VELOCITY: %s km/hr", tostring(math.floor(frac * 110000 / 100 + 0.5) * 100):reverse():gsub("(%d%d%d)", "%1,"):reverse():gsub("^,", ""))

		if x < 0.045 or x > 0.955 then
			s:Lose("The ship left the flight corridor!")
		elseif prog >= HOLD_TIME then
			s:Win()
		end
	end

	s:StartScreen()
end

-- =========================================================================
-- MINIJUEGO 2: TRAJECTORY (esquivar asteroides)
-- =========================================================================
local function buildGame2(s)
	local stage = s.Stage
	local DURATION, MAX_LIVES = 25, 3
	local SHIP_SPEED = 560

	local ship, flame = makeShip(stage)
	ship.Rotation = -90
	flame.Visible = true
	local pos = Vector2.new(W / 2, 770)
	place(ship, pos.X, pos.Y, 90, 64)

	local asteroids = {}
	local spawnTimer = 0.4
	local elapsed = 0
	local lives = MAX_LIVES
	local invuln = 0

	local bar = makeBar(stage, 46, C.Gold)
	local timeLbl = makeHudLabel(stage, 66, string.format("SURVIVE  %d s", DURATION), C.White)
	local livesLbl = makeHudLabel(stage, 94, "♥ ♥ ♥", C.Red)

	-- Controles en pantalla (touch / mouse)
	s:HoldButton("◀", "Left", UDim2.new(0, 16, 1, -80), UDim2.new(0, 64, 0, 64))
	s:HoldButton("▶", "Right", UDim2.new(0, 90, 1, -80), UDim2.new(0, 64, 0, 64))
	s:HoldButton("▲", "Up", UDim2.new(1, -154, 1, -80), UDim2.new(0, 64, 0, 64))
	s:HoldButton("▼", "Down", UDim2.new(1, -80, 1, -80), UDim2.new(0, 64, 0, 64))

	local function key(...)
		for _, k in ipairs({...}) do
			if s.Keys[k] then return true end
		end
		return false
	end

	local function spawnAsteroid()
		local r = math.random(24, 58)
		local f = makeAsteroid(stage)
		local a = {
			Frame = f, R = r,
			P = Vector2.new(math.random(50, W - 50), -80),
			V = Vector2.new(math.random(-70, 70), math.random(240, 440)),
		}
		place(f, a.P.X, a.P.Y, r * 2, r * 2)
		table.insert(asteroids, a)
	end

	s.Update = function(dt)
		elapsed += dt

		-- movimiento de la nave
		local dx, dy = 0, 0
		if key(Enum.KeyCode.A, Enum.KeyCode.Left) or s.Hold.Left then dx -= 1 end
		if key(Enum.KeyCode.D, Enum.KeyCode.Right) or s.Hold.Right then dx += 1 end
		if key(Enum.KeyCode.W, Enum.KeyCode.Up) or s.Hold.Up then dy -= 1 end
		if key(Enum.KeyCode.S, Enum.KeyCode.Down) or s.Hold.Down then dy += 1 end
		local move = Vector2.new(dx, dy)
		if move.Magnitude > 0 then
			pos += move.Unit * SHIP_SPEED * dt
		end
		pos = Vector2.new(math.clamp(pos.X, 50, W - 50), math.clamp(pos.Y, 430, H - 40))
		place(ship, pos.X, pos.Y, 90, 64)

		-- parpadeo cuando es invulnerable
		invuln = math.max(0, invuln - dt)
		ship.Visible = (invuln <= 0) or (math.floor(elapsed * 10) % 2 == 0)

		-- aparicion de asteroides (cada vez mas rapido)
		spawnTimer -= dt
		if spawnTimer <= 0 then
			spawnAsteroid()
			spawnTimer = math.max(0.30, 0.75 - elapsed * 0.018)
		end

		for i = #asteroids, 1, -1 do
			local a = asteroids[i]
			a.P += a.V * dt
			place(a.Frame, a.P.X, a.P.Y)
			local remove = false

			if a.P.Y > H + 100 or a.P.X < -100 or a.P.X > W + 100 then
				remove = true
			elseif invuln <= 0 and (a.P - pos).Magnitude < (a.R * 0.85 + 24) then
				lives -= 1
				invuln = 1.2
				remove = true
				livesLbl.Text = string.rep("♥ ", math.max(lives, 0))
			end

			if remove then
				a.Frame:Destroy()
				table.remove(asteroids, i)
			end
		end

		bar.Size = UDim2.fromScale(math.clamp(elapsed / DURATION, 0, 1), 1)
		timeLbl.Text = string.format("SURVIVE  %d s", math.max(0, math.ceil(DURATION - elapsed)))

		if lives <= 0 then
			s:Lose("The hull was breached by the asteroids!")
		elseif elapsed >= DURATION then
			s:Win()
		end
	end

	s:StartScreen()
end

-- =========================================================================
-- MINIJUEGO 3: POLAR ORBIT (lanzar la nave y ser capturado)
-- =========================================================================
local function buildGame3(s)
	local stage = s.Stage

	local ANCHOR = Vector2.new(260, 450)
	local MAX_PULL, MIN_PULL, K = 170, 40, 3.0   -- velocidad de lanzamiento = jalon * K (max 510)
	local PLANET_R, RING_IN, RING_OUT = 85, 140, 240
	local GM = 5.0e6                              -- atraccion del planeta (efecto suave)
	local CAPTURE_MAX = 470                       -- velocidad maxima para ser capturado
	local ORBIT_R = 190
	local ORBIT_SPEED = 2.3                       -- rad/s
	local C0 = Vector2.new(1180, math.random(300, 600))

	-- planeta, eje polar y anillos
	local planet = makePlanet(stage)
	place(planet, C0.X, C0.Y, PLANET_R * 2, PLANET_R * 2)

	local axis = new("Frame", {
		BackgroundColor3 = C.White, BackgroundTransparency = 0.5, BorderSizePixel = 0, ZIndex = 4,
	}, stage)
	place(axis, C0.X, C0.Y, 5, PLANET_R * 2 + 130)
	for _, sign in ipairs({-1, 1}) do
		local l = new("TextLabel", {
			BackgroundTransparency = 1, Text = (sign < 0) and "N" or "S", TextColor3 = C.White,
			Font = Enum.Font.GothamBlack, TextScaled = true, ZIndex = 4,
		}, stage)
		place(l, C0.X, C0.Y + sign * (PLANET_R + 85), 44, 44)
	end

	local function ring(radius, color, transparency, visible)
		local f = new("Frame", {BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 3, Visible = visible}, stage)
		circle(f)
		new("UIStroke", {Color = color, Thickness = 3, Transparency = transparency}, f)
		place(f, C0.X, C0.Y, radius * 2, radius * 2)
		return f
	end
	ring(RING_OUT, C.Cyan, 0.35, true)
	ring(RING_IN, C.Cyan, 0.35, true)
	local orbitRing = ring(ORBIT_R, C.Green, 0, false)

	local zoneLbl = new("TextLabel", {
		BackgroundTransparency = 1, Text = "CAPTURE RING", TextColor3 = C.Cyan, Font = Enum.Font.GothamBold,
		TextScaled = true, ZIndex = 4,
	}, stage)
	place(zoneLbl, C0.X, C0.Y - RING_OUT - 22, 220, 30)

	-- punto de lanzamiento
	local pad = new("Frame", {BackgroundTransparency = 1, BorderSizePixel = 0, ZIndex = 3}, stage)
	circle(pad)
	new("UIStroke", {Color = C.Gold, Thickness = 3, Transparency = 0.3}, pad)
	place(pad, ANCHOR.X, ANCHOR.Y, 120, 120)

	local band = new("Frame", {BackgroundColor3 = C.Gold, BorderSizePixel = 0, ZIndex = 5, Visible = false}, stage)

	local dots = {}
	for i = 1, 17 do
		local d = new("Frame", {BackgroundColor3 = C.White, BorderSizePixel = 0, ZIndex = 4, Visible = false}, stage)
		circle(d)
		dots[i] = d
	end

	local trail = {}
	local ship = makeShip(stage)
	local shipPos = ANCHOR
	local shipVel = Vector2.zero
	place(ship, shipPos.X, shipPos.Y, 80, 56)

	-- HUD
	local msgLbl = makeHudLabel(stage, 52, "", C.White)
	local hintLbl = makeHudLabel(stage, 84, "DRAG THE SHIP BACK AND RELEASE", C.Dim)
	local shotsLbl = new("TextLabel", {
		Size = UDim2.new(0, 160, 0, 26), AnchorPoint = Vector2.new(1, 0), Position = UDim2.new(1, -14, 0, 12),
		BackgroundTransparency = 1, Text = "SHOTS: 0", TextColor3 = C.Dim, Font = Enum.Font.GothamBold,
		TextScaled = true, ZIndex = 30,
	}, stage)

	local meterBg = new("Frame", {
		AnchorPoint = Vector2.new(0, 1), Size = UDim2.new(0, 260, 0, 20), Position = UDim2.new(0, 16, 1, -44),
		BackgroundColor3 = C.Panel2, BorderSizePixel = 0, ZIndex = 30,
	}, stage)
	round(meterBg, 8)
	-- zona verde del medidor (velocidad <= CAPTURE_MAX)
	local greenFrac = (CAPTURE_MAX / K) / MAX_PULL
	local okZone = new("Frame", {
		Size = UDim2.fromScale(greenFrac, 1), BackgroundColor3 = C.Green, BackgroundTransparency = 0.7,
		BorderSizePixel = 0, ZIndex = 31,
	}, meterBg)
	round(okZone, 8)
	local meterFill = new("Frame", {Size = UDim2.fromScale(0, 1), BackgroundColor3 = C.Gold, BorderSizePixel = 0, ZIndex = 32}, meterBg)
	round(meterFill, 8)
	new("TextLabel", {
		AnchorPoint = Vector2.new(0, 1), Size = UDim2.new(0, 260, 0, 18), Position = UDim2.new(0, 16, 1, -66),
		BackgroundTransparency = 1, Text = "POWER  (green = can be captured)", TextColor3 = C.Dim,
		Font = Enum.Font.GothamBold, TextScaled = true, ZIndex = 30, TextXAlignment = Enum.TextXAlignment.Left,
	}, stage)

	-- estado
	local mode = "AIM" -- AIM | FLY | RESET | ORBIT | DONE
	local dragging = false
	local pointer = ANCHOR
	local shots = 0
	local flyTime = 0
	local resetTimer = 0
	local msgTimer = 0
	local warnedFast = false
	local angle, orbitDir, orbitR, swept, trailTimer = 0, 1, 0, 0, 0

	local function say(text, color, seconds)
		msgLbl.Text = text
		msgLbl.TextColor3 = color or C.White
		msgTimer = seconds or 2
	end

	local function step(p, v, dt)
		local d = C0 - p
		local r = d.Magnitude
		if r < 1 then
			return p + v * dt, v
		end
		v = v + d.Unit * (GM / (r * r)) * dt
		return p + v * dt, v
	end

	local function toWorld(screenPos)
		local ap, as = stage.AbsolutePosition, stage.AbsoluteSize
		return Vector2.new((screenPos.X - ap.X) / as.X * W, (screenPos.Y - ap.Y) / as.Y * H)
	end

	local function placeLine(frame, a, b, thickness)
		local mid = (a + b) / 2
		local d = b - a
		frame.AnchorPoint = Vector2.new(0.5, 0.5)
		frame.Position = UDim2.fromScale(mid.X / W, mid.Y / H)
		frame.Size = UDim2.fromScale(math.max(d.Magnitude, 1) / W, thickness / H)
		frame.Rotation = math.deg(math.atan2(d.Y, d.X))
	end

	local function hidePreview()
		for _, d in ipairs(dots) do
			d.Visible = false
		end
	end

	local function updatePreview(vel)
		local p, v = shipPos, vel
		local idx = 0
		local good = vel.Magnitude <= CAPTURE_MAX
		for i = 1, 70 do
			p, v = step(p, v, 0.03)
			if (p - C0).Magnitude <= PLANET_R then break end
			if i % 4 == 0 and idx < #dots then
				idx += 1
				local d = dots[idx]
				d.BackgroundColor3 = good and C.Green or C.Red
				place(d, p.X, p.Y, 10, 10)
				d.Visible = true
			end
		end
		for k = idx + 1, #dots do
			dots[k].Visible = false
		end
	end

	local function resetShip()
		shipPos = ANCHOR
		shipVel = Vector2.zero
		mode = "AIM"
		band.Visible = false
		meterFill.Size = UDim2.fromScale(0, 1)
		hidePreview()
		place(ship, shipPos.X, shipPos.Y, 80, 56)
		ship.Rotation = 0
		warnedFast = false
	end

	local function updateDrag()
		local off = pointer - ANCHOR
		if off.Magnitude > MAX_PULL then
			off = off.Unit * MAX_PULL
		end
		shipPos = ANCHOR + off
		local pull = -off
		local power = pull.Magnitude
		place(ship, shipPos.X, shipPos.Y, 80, 56)
		if power > 1 then
			ship.Rotation = math.deg(math.atan2(pull.Y, pull.X))
		end
		band.Visible = power > 4
		if band.Visible then
			placeLine(band, ANCHOR, shipPos, 5)
		end
		meterFill.Size = UDim2.fromScale(math.clamp(power / MAX_PULL, 0, 1), 1)
		meterFill.BackgroundColor3 = (power * K <= CAPTURE_MAX) and C.Green or C.Red
		if power >= MIN_PULL then
			updatePreview(pull * K)
		else
			hidePreview()
		end
	end

	local function launch()
		local pull = ANCHOR - shipPos
		if pull.Magnitude < MIN_PULL then
			resetShip()
			return
		end
		shots += 1
		shotsLbl.Text = "SHOTS: " .. shots
		hintLbl.Text = ""
		shipVel = pull * K
		flyTime = 0
		mode = "FLY"
		band.Visible = false
		hidePreview()
		meterFill.Size = UDim2.fromScale(0, 1)
	end

	local function missed(text)
		mode = "RESET"
		resetTimer = 1.3
		say(text, C.Red, 1.3)
	end

	local function capture()
		mode = "ORBIT"
		local rel = shipPos - C0
		angle = math.atan2(rel.Y, rel.X)
		orbitDir = ((rel.X * shipVel.Y - rel.Y * shipVel.X) >= 0) and 1 or -1
		orbitR = rel.Magnitude
		swept = 0
		trailTimer = 0
		orbitRing.Visible = true
		say("CAPTURED! ESTABLISHING POLAR ORBIT...", C.Green, 99)
	end

	-- Arrastre con mouse / touch
	local dragArea = new("TextButton", {
		Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = "", AutoButtonColor = false, ZIndex = 3,
	}, stage)
	dragArea.InputBegan:Connect(function(input)
		if not s.Running or mode ~= "AIM" then return end
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			local p = toWorld(Vector2.new(input.Position.X, input.Position.Y))
			if (p - ANCHOR).Magnitude <= 170 then
				dragging = true
				pointer = p
				updateDrag()
			end
		end
	end)
	s:Connect(UserInputService.InputChanged, function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
			pointer = toWorld(Vector2.new(input.Position.X, input.Position.Y))
			updateDrag()
		end
	end)
	s:Connect(UserInputService.InputEnded, function(input)
		if dragging and (input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch) then
			dragging = false
			launch()
		end
	end)

	s.Update = function(dt)
		if msgTimer > 0 then
			msgTimer -= dt
			if msgTimer <= 0 then msgLbl.Text = "" end
		end

		if mode == "FLY" then
			flyTime += dt
			for _ = 1, 2 do
				shipPos, shipVel = step(shipPos, shipVel, dt / 2)
			end
			place(ship, shipPos.X, shipPos.Y, 80, 56)
			ship.Rotation = math.deg(math.atan2(shipVel.Y, shipVel.X))

			local r = (shipPos - C0).Magnitude
			if r <= PLANET_R then
				missed("CRASHED INTO THE PLANET - TRY AGAIN")
			elseif r <= RING_OUT then
				if shipVel.Magnitude <= CAPTURE_MAX then
					capture()
				elseif not warnedFast then
					warnedFast = true
					say("TOO FAST - THE PLANET CAN'T CAPTURE YOU", C.Gold, 1.6)
				end
			end
			if mode == "FLY" and (shipPos.X > W + 80 or shipPos.X < -80 or shipPos.Y < -80 or shipPos.Y > H + 80 or flyTime > 9) then
				missed("MISSED THE ORBIT - TRY AGAIN")
			end

		elseif mode == "RESET" then
			resetTimer -= dt
			if resetTimer <= 0 then
				resetShip()
			end

		elseif mode == "ORBIT" then
			local dTheta = ORBIT_SPEED * dt * orbitDir
			angle += dTheta
			swept += math.abs(dTheta)
			orbitR += (ORBIT_R - orbitR) * math.min(1, dt * 4)
			local rr = orbitR * (1 + 0.02 * math.cos(angle)) -- casi circular (e ~ 0.02)
			shipPos = C0 + Vector2.new(math.cos(angle), math.sin(angle)) * rr
			place(ship, shipPos.X, shipPos.Y, 80, 56)
			ship.Rotation = math.deg(angle + orbitDir * math.pi / 2)

			trailTimer -= dt
			if trailTimer <= 0 then
				trailTimer = 0.06
				local d = new("Frame", {BackgroundColor3 = C.Green, BorderSizePixel = 0, ZIndex = 4}, stage)
				circle(d)
				place(d, shipPos.X, shipPos.Y, 8, 8)
				table.insert(trail, d)
				if #trail > 90 then
					table.remove(trail, 1):Destroy()
				end
			end

			if swept >= math.pi * 2.1 then
				mode = "DONE"
				say("POLAR ORBIT ACHIEVED  -  e = 0.02  -  inclination 90°", C.Green, 99)
				s:Win()
			end
		end
	end

	s:StartScreen()
end

-- =========================================================================
-- INICIO DE JUEGO
-- =========================================================================
local BUILDERS = {buildGame1, buildGame2, buildGame3}

startGame = function(id)
	local game = GAMES[id]
	local builder = BUILDERS[id]
	if not game or not builder then return end
	local s = newSession(game)
	builder(s)
end

-- =========================================================================
-- REMOTES
-- =========================================================================
OpenSimEvent.OnClientEvent:Connect(function(kind, prog, info)
	if kind == "ACCESS_DENIED" then
		toast("ACCESS DENIED: ONLY THE PHYSICIST CAN USE THIS BOARD")
	elseif kind == "OPEN_HUB" then
		progress = prog or {}
		infos = info or {}
		backdrop.Visible = true
		modal.Visible = true
		showHub()
	end
end)

SyncSimStateEvent.OnClientEvent:Connect(function(prog, info)
	progress = prog or {}
	infos = info or {}
	if view == "HUB" then
		showHub()
	elseif view == "GAME" and currentSession and currentSession.InfoLabel then
		currentSession.InfoLabel.Text = "SENT TO SPACECRAFT: " .. (infos[tostring(currentSession.Id)] or "...")
	end
end)

```

---

