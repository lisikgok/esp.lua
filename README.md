--// WAIT GAME \\--
repeat task.wait() until game:IsLoaded()

--// SERVICES \\--
local Players = game:GetService("Players")
local Player = Players.LocalPlayer

--// GUI \\--
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/JustEzpi/ROBLOX-Scripts/main/Generic_GUI"))()
local Window = Library:CreateWindow("Ore ESP FIXED")

--// VARIABLES \\--
local oresFolder = workspace:WaitForChild("PlacedOre")

local ESPEnabled = false
local HighlightEnabled = false

local espObjects = {}
local highlights = {}

--// COLORS \\--
local rarityColors = {
	["CrystallineMetalOre"] = Color3.fromRGB(0,255,255)
}

--// DISTANCE \\--
local function getDistance(part)
	local char = Player.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return 0 end
	return (char.HumanoidRootPart.Position - part.Position).Magnitude
end

--// 🔥 ГЛУБОКИЙ ПОИСК PART \\--
local function getMainPart(obj)
	if obj:IsA("BasePart") then
		return obj
	end

	for _, v in pairs(obj:GetDescendants()) do
		if v:IsA("BasePart") then
			return v
		end
	end

	return nil
end

--// CREATE ESP \\--
local function createESP(ore)
	local part = getMainPart(ore)
	if not part then return end
	if espObjects[part] then return end

	-- Billboard
	local billboard = Instance.new("BillboardGui")
	billboard.Name = "ORE_ESP"
	billboard.Size = UDim2.new(0, 120, 0, 40)
	billboard.AlwaysOnTop = true
	billboard.Adornee = part
	billboard.Parent = part

	local label = Instance.new("TextLabel")
	label.Size = UDim2.new(1, 0, 1, 0)
	label.BackgroundTransparency = 1
	label.TextScaled = true
	label.Font = Enum.Font.SourceSansBold
	label.Parent = billboard

	espObjects[part] = label

	-- Highlight
	local highlight = Instance.new("Highlight")
	highlight.Name = "ORE_HIGHLIGHT"
	highlight.FillTransparency = 0.5
	highlight.OutlineTransparency = 0
	highlight.Enabled = false
	highlight.Parent = ore

	highlights[part] = highlight
end

--// LOAD \\--
for _, ore in pairs(oresFolder:GetChildren()) do
	createESP(ore)
end

oresFolder.ChildAdded:Connect(function(child)
	task.wait(0.1)
	createESP(child)
end)

--// UPDATE (АНТИ-ЛАГ) \\--
task.spawn(function()
	while true do
		task.wait(0.25)

		if ESPEnabled then
			for part, label in pairs(espObjects) do
				if part and part.Parent then
					local dist = math.floor(getDistance(part))
					label.Text = "Ore [" .. dist .. "m]"
					label.TextColor3 = Color3.fromRGB(0,255,255)
				end
			end
		end

		if HighlightEnabled then
			for _, highlight in pairs(highlights) do
				if highlight then
					highlight.FillColor = Color3.fromRGB(0,255,255)
				end
			end
		end
	end
end)

--// GUI \\--
local Folder = Window:AddFolder("Ore ESP")

Folder:AddToggle({
	text = "ESP",
	callback = function(state)
		ESPEnabled = state

		for _, obj in pairs(oresFolder:GetDescendants()) do
			if obj:IsA("BillboardGui") then
				obj.Enabled = state
			end
		end
	end
})

Folder:AddToggle({
	text = "Highlight",
	callback = function(state)
		HighlightEnabled = state

		for _, obj in pairs(oresFolder:GetDescendants()) do
			if obj:IsA("Highlight") then
				obj.Enabled = state
			end
		end
	end
})

Library:Init()
