local oresFolder = workspace:FindFirstChild("PlacedOre")

local function createESP(obj)
    if obj:FindFirstChild("Highlight") then return end
    local h = Instance.new("Highlight")
    h.FillColor = Color3.fromRGB(0,255,0)
    h.OutlineColor = Color3.fromRGB(255,255,255)
    h.FillTransparency = 0.5
    h.Parent = obj
end

local function removeESP(obj)
    local h = obj:FindFirstChild("Highlight")
    if h then h:Destroy() end
end

local espEnabled = true

local function updateESP()
    if not oresFolder then return end
    for _,v in pairs(oresFolder:GetChildren()) do
        if espEnabled then
            createESP(v)
        else
            removeESP(v)
        end
    end
end

if oresFolder then
    oresFolder.ChildAdded:Connect(function(v)
        task.wait(0.1)
        if espEnabled then
            createESP(v)
        end
    end)
end

local gui = Instance.new("ScreenGui")
gui.Parent = game.CoreGui

local button = Instance.new("TextButton")
button.Size = UDim2.new(0,120,0,40)
button.Position = UDim2.new(0,20,0,20)
button.Text = "ESP: ON"
button.Parent = gui

button.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    button.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    updateESP()
end)

updateESP()
