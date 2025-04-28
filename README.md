local plr = game.Players.LocalPlayer
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("NotInfected hub (FORSAKEN v1.0)", "BloodTheme")
local MainTab = Window:NewTab("Game")
local ESPSection = MainTab:NewSection("ESP")
local TeleportSection = MainTab:NewSection("Game-Changing")
local OthersTab = Window:NewTab("Others")
local othersSection = OthersTab:NewSection("...")
othersSection:NewLabel("Made by RobloxIs_GoodBoy ^_^")
--code
_G.KillerESP = false
_G.SurvivorESP = false
_G.ItemESP = false
_G.ForceSpeed = false
_G.AutoKill = false
_G.Speed = 1
local Players = game.Workspace.Players
local Killers = Players.Killers
local Survivors = Players.Survivors

local SurThingNames = {"BuildermanSentry","BuildermanDispenser","007n7","SubspaceTripmine"}

local function getPlrCharacter()
    if plr.Character then
        if plr.Character.Parent.Name ~= "Spectating" then
            return plr.Character
        else return nil
        end
    else return nil
    end
end

local function getInGameMap()
    local da_map = workspace.Map.Ingame
    if da_map:FindFirstChild("Map") then
        return da_map
    else return nil
    end
end

local function getSpawnPoints()
    local map = getInGameMap()
    if map then
        return map.Map.SpawnPoints
    end
end

local function GetItems()
    local a = {}
    local map = getInGameMap()
    if map then
        for _,v in pairs(map:GetChildren()) do
            if v and v:IsA("Tool") then
               table.insert(a,v) 
            end
        end
    end
    return a
end

local function getClosestSurvivor()
    local e= math.huge
    local sur = nil

    local plrChar = getPlrCharacter()
    if not plrChar then return nil end

    for _,char in pairs(Survivors:GetChildren()) do
        if char and (char.PrimaryPart.Position - plrChar.PrimaryPart.Position).Magnitude < e then
            sur = char
            e = (char.PrimaryPart.Position - plrChar.PrimaryPart.Position).Magnitude
        end
    end

    return sur
end

local function doSurvivorsESPThingies()
    local da_map = getInGameMap()
    if da_map then
        for _,thing in pairs(da_map:GetChildren()) do
            if thing and thing.Name ~= "Map" and table.find(SurThingNames,thing.Name) then
                if thing:FindFirstChild("ESP") then
                    if not _G.SurvivorESP then
                        thing.ESP:Destroy()
                    end
                elseif _G.SurvivorESP then
                    local newESP = Instance.new("Highlight")
                    newESP.Name = "ESP"
                    newESP.Parent = thing
                    newESP.OutlineTransparency = 1
                    newESP.FillColor = Color3.fromRGB(2, 80, 186)
                end
            end
        end
    end
end

local function refreshESP()
    --add new and refresh color
    for _,char in pairs(Survivors:GetChildren()) do
        if char and char ~= plr.Character then
            if char:FindFirstChild("ESP") then
                if not _G.SurvivorESP then
                    char.ESP:Destroy()
                end
            elseif _G.SurvivorESP then
                local newESP = Instance.new("Highlight")
                newESP.Name = "ESP"
                newESP.Parent = char
                newESP.OutlineTransparency = 1
                newESP.FillColor = Color3.fromRGB(16, 110, 240)
            end
        end
    end
    for _,char in pairs(Killers:GetChildren()) do
        if char and char ~= plr.Character then
            if char:FindFirstChild("ESP") then
                if not _G.KillerESP then
                    char.ESP:Destroy()
                end
            elseif _G.KillerESP then
                local newESP = Instance.new("Highlight")
                newESP.Name = "ESP"
                newESP.Parent = char
                newESP.OutlineTransparency = 1
                newESP.FillColor = Color3.fromRGB(255,0,0)
            end
        end
    end
    doSurvivorsESPThingies()
    --now it's time for items
    local items = GetItems()
    for _,item in pairs(items) do
        if item then
            if item:FindFirstChild("ESP") then
                if not _G.ItemESP then
                    item.ESP:Destroy()
                end
            elseif _G.ItemESP then
                local newESP = Instance.new("Highlight")
                newESP.Name = "ESP"
                newESP.Parent = item
                newESP.OutlineTransparency = 1
                newESP.FillColor = Color3.fromRGB(235, 213, 0)
            end
        end
    end
end
--end

ESPSection:NewToggle("Killer ESP", "Info", function(state)
    _G.KillerESP = state
end)
ESPSection:NewToggle("Survivor ESP", "Info", function(state)
    _G.SurvivorESP = state
end)
ESPSection:NewToggle("Item ESP", "Info", function(state)
    _G.ItemESP = state
end)

TeleportSection:NewButton("Teleport to medkit","Nigga", function()
    local char = getPlrCharacter()
    local map = getInGameMap()
    if char and map then
        local items = GetItems()
        for _,item in pairs(items) do
            if item and item.Name == "Medkit" then
                char.PrimaryPart.CFrame = CFrame.new(item.ItemRoot.Position+Vector3.new(0,2,0))
            end
        end
    end
end)

TeleportSection:NewButton("Teleport to bloxy cola","Nigga", function()
    local char = getPlrCharacter()
    local map = getInGameMap()
    if char and map then
        local items = GetItems()
        for _,item in pairs(items) do
            if item and item.Name == "BloxyCola" then
                char.PrimaryPart.CFrame = CFrame.new(item.ItemRoot.Position+Vector3.new(0,2,0))
            end
        end
    end
end)

TeleportSection:NewButton("Teleport to furthest","Nigga", function()
    local char = getPlrCharacter()
    local spawnPoints = getSpawnPoints()
    if char and spawnPoints then
        local survivors = spawnPoints.Survivors
        local e= -1
        local current = nil
        for _,point in pairs(survivors:GetChildren()) do
            if (point.Position-char.PrimaryPart.Position).Magnitude > e then
                e = (point.Position-char.PrimaryPart.Position).Magnitude
                current = point
            end
        end
        if current then
            char.PrimaryPart.CFrame = CFrame.new(current.Position+Vector3.new(0,2,0))
        end
    end
end)

TeleportSection:NewKeybind("Teleport to mouse pos", "idunoo", Enum.KeyCode.F, function()
	local char = getPlrCharacter()
    if char then
        local mouse = plr:GetMouse()
        if mouse and mouse.Hit then
            char.PrimaryPart.CFrame = CFrame.new(mouse.Hit.Position + Vector3.new(0,2,0))
        end
    end
end)

TeleportSection:NewToggle("Force Speed", "DO NOT SPRINT WHILE ENABLING THIS", function(state)
    _G.ForceSpeed = state
end)

TeleportSection:NewSlider("Speed", "pls don't sprint while enabling", 36, 6, function(s) -- 500 (MaxValue) | 0 (MinValue)
    _G.Speed = s
end)

TeleportSection:NewToggle("Auto Kill as Killer", "HOLD ON TIGHT MY COMPASS IS CURIOSITY SDJADSJASDJSDA", function(state)
    _G.AutoKill = state
end)

othersSection:NewKeybind("ToggleUI", "fick", Enum.KeyCode.LeftAlt, function()
	Library:ToggleUI()
end)

othersSection:NewButton("Copy discord serv link", "thank :D", function()
    setclipboard("https://discord.gg/xqRBN8a79Y")
end)
--mobile
-- Instances:

local ScreenGui = Instance.new("ScreenGui")
local TextButton = Instance.new("TextButton")

--Properties:

ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.DisplayOrder = 9999
ScreenGui.ResetOnSpawn = false

TextButton.Parent = ScreenGui
TextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextButton.BackgroundTransparency = 0.400
TextButton.BorderColor3 = Color3.fromRGB(0, 0, 0)
TextButton.BorderSizePixel = 0
TextButton.Position = UDim2.new(0.930612266, 0, 0.161934346, 0)
TextButton.Size = UDim2.new(0.0598639473, 0, 0.0992736071, 0)
TextButton.Font = Enum.Font.SourceSans
TextButton.Text = "TOGGLE UI"
TextButton.TextColor3 = Color3.fromRGB(0, 0, 0)
TextButton.TextScaled = true
TextButton.TextSize = 14.000
TextButton.TextWrapped = true
TextButton.Visible = false

local UIS = game:GetService("UserInputService")
if UIS.MouseEnabled == false and UIS.KeyboardEnabled == false and UIS.TouchEnabled then
	TextButton.Visible = true
end
TextButton.MouseButton1Click:Connect(function()
    Library:ToggleUI()
end)
--end
game:GetService("RunService").RenderStepped:Connect(function()
	refreshESP()
    local char = getPlrCharacter()
    if char then
        if _G.ForceSpeed then
            char.Humanoid:SetAttribute("BaseSpeed",_G.Speed)
        end
        if char.Parent == Killers then
            if _G.AutoKill then
                local survivor = getClosestSurvivor()
                if survivor then
                    char.PrimaryPart.CFrame = survivor.PrimaryPart.CFrame * CFrame.new(0,0,.5)
                    local arg = "Slash"
                    if char.Name == "c00lkidd" then
                        arg = "Punch"
                    end
                    game:GetService("ReplicatedStorage").Modules.Network.RemoteEvent:FireServer("UseActorAbility",arg)
                end
            end
        end
    end
end)
