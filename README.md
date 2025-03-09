local UIS = game:GetService("UserInputService")
local camera = game.workspace.CurrentCamera
local TS = game:GetService("TweenService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local MaxDistance = 500
local ChamsColor = Color3.fromRGB(0, 255, 0)

local ESPObjects = {}
local NameTags = {}


local Settings = {
    ChamsEnabled = false,
    InnerTransparency = 1,
    TeamCheck = false,
    UseTeamColor = false,
    ShowNames = false
}


-- ESP Settings
local ESP = {
    enabled = true,
    boxes = false,
    names = false,
    tracers = false,
    health = false,
    teamColor = false,
    showDistance = false,
    showHealthNumber = false,
    maxDistance = 1000,
    boxElements = {},
    nameElements = {},
    tracerElements = {},
    healthElements = {},
    skeletonElements = {},
    settings = {
        textSize = 8,
        boxThickness = 1,
        tracerThickness = 1,
        transparency = 1,
    },
    colors = {
        box = Color3.fromRGB(255, 255, 255),
        tracer = Color3.fromRGB(255, 255, 255),
        health = Color3.fromRGB(0, 255, 0),
        text = Color3.fromRGB(255, 255, 255)
    }
}

-- Cache frequently used values
local Vector2new = Vector2.new
local Vector3new = Vector3.new
local Color3new = Color3.new
local mathfloor = math.floor
local stringformat = string.format
local tableremove = table.remove


local noclipEnabled = false
local noclipConnection = nil

local isKillAuraEnabled = false
local teleportDistance = 3

-- Aimbot Variables
local AIM_SMOOTHNESS = 1
local g = {
    aim = false,
    aimbotEnabled = false
}
local localPlayer = Players.LocalPlayer

-- Discord Link
local DiscordLink = "https://discord.gg/xX5SVJFJYA"

-- Load Rayfield
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

setclipboard("https://discord.gg/xX5SVJFJYA")

local Window = Rayfield:CreateWindow({
   Name = "Counter Blox Overhaul by Morro v1.00",
   Icon = 0,
   LoadingTitle = "Loading...",
   LoadingSubtitle = "by Morro",
   Theme = "Amethyst",
   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false,
   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil,
      FileName = "Morro"
   },
   Discord = {
      Enabled = false,
      Invite = "noinvitelink",
      RememberJoins = true
   },
   KeySystem = true,
   KeySettings = {
      Title = "Counter Blox: Reforged v1.00",
      Subtitle = "By Morro'",
      Note = "Discord link has been copied to your clipboard!",
      FileName = "Key",
      SaveKey = false,
      GrabKeyFromSite = true,
      Key = {"https://pastebin.com/raw/9KraP7ku"}
   }
})



-- Optimized distance format function
local function formatDistance(distance)
    return stringformat("%.0f", distance)
end

-- Cleanup all ESP function
local function cleanupAllESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if ESP.boxElements[player] then
            ESP.boxElements[player].Visible = false
            ESP.nameElements[player].Visible = false
            ESP.tracerElements[player].Visible = false
            
            local healthBar = ESP.healthElements[player]
            if healthBar then
                healthBar.background.Visible = false
                healthBar.fill.Visible = false
                healthBar.outline.Visible = false
                healthBar.text.Visible = false
            end
            
        end
     end
 end
-- Optimized cleanup function
local function cleanupESPElement(player)
    local elements = ESP.boxElements[player]
    if elements then
        ESP.boxElements[player]:Remove()
        ESP.nameElements[player]:Remove()
        ESP.tracerElements[player]:Remove()
        
        local healthElements = ESP.healthElements[player]
        if healthElements then
            healthElements.background:Remove()
            healthElements.fill:Remove()
            healthElements.outline:Remove()
            healthElements.text:Remove()
        end
        
        ESP.boxElements[player] = nil
        ESP.nameElements[player] = nil
        ESP.tracerElements[player] = nil
        ESP.healthElements[player] = nil
    end
end

-- Optimized ESP element creation
local function createESPElement(player)
    if not ESP.boxElements[player] then
        -- Box ESP
        local box = Drawing.new("Square")
        box.Thickness = ESP.settings.boxThickness
        box.Filled = false
        box.Transparency = ESP.settings.transparency
        ESP.boxElements[player] = box
        
        -- Name ESP
        local name = Drawing.new("Text")
        name.Size = ESP.settings.textSize
        name.Center = true
        name.Outline = true
        name.Transparency = ESP.settings.transparency
        ESP.nameElements[player] = name
        
        -- Tracer ESP
        local tracer = Drawing.new("Line")
        tracer.Thickness = ESP.settings.tracerThickness
        tracer.Transparency = ESP.settings.transparency
        ESP.tracerElements[player] = tracer
        
        -- Health ESP
        local healthBar = {
            background = Drawing.new("Square"),
            fill = Drawing.new("Square"),
            outline = Drawing.new("Square"),
            text = Drawing.new("Text")
        }
        
        healthBar.background.Filled = true
        healthBar.background.Color = Color3new(0, 0, 0)
        healthBar.background.Transparency = ESP.settings.transparency
        
        healthBar.fill.Filled = true
        healthBar.fill.Transparency = ESP.settings.transparency
        
        healthBar.outline.Thickness = 1
        healthBar.outline.Filled = false
        healthBar.outline.Color = Color3new(0, 0, 0)
        healthBar.outline.Transparency = ESP.settings.transparency

        healthBar.text.Size = ESP.settings.textSize
        healthBar.text.Center = true
        healthBar.text.Outline = true
        healthBar.text.Transparency = ESP.settings.transparency
        
        ESP.healthElements[player] = healthBar
    end
end

-- Optimized update function
local function updateESP()
    if not ESP.enabled then
        cleanupAllESP()
        return
    end
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player == localPlayer then continue end
        
        local character = player.Character
        local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart")
        local humanoid = character and character:FindFirstChild("Humanoid")
        
        if not (character and humanoidRootPart and humanoid and humanoid.Health > 0) then
            if ESP.boxElements[player] then
                ESP.boxElements[player].Visible = false
                ESP.nameElements[player].Visible = false
                ESP.tracerElements[player].Visible = false
                local healthBar = ESP.healthElements[player]
                if healthBar then
                    healthBar.background.Visible = false
                    healthBar.fill.Visible = false
                    healthBar.outline.Visible = false
                    healthBar.text.Visible = false
                end
            end
            continue
        end

        local distance = (camera.CFrame.Position - humanoidRootPart.Position).Magnitude
        if distance > ESP.maxDistance then
            if ESP.boxElements[player] then
                ESP.boxElements[player].Visible = false
                ESP.nameElements[player].Visible = false
                ESP.tracerElements[player].Visible = false
                local healthBar = ESP.healthElements[player]
                if healthBar then
                    healthBar.background.Visible = false
                    healthBar.fill.Visible = false
                    healthBar.outline.Visible = false
                    healthBar.text.Visible = false
                end
            end
            continue
        end

        local vector, onScreen = camera:WorldToViewportPoint(humanoidRootPart.Position)
        
        if onScreen then
            createESPElement(player)
            
            local color = ESP.teamColor and player.TeamColor.Color or ESP.colors.box
            local size = Vector2new(2000 / vector.Z, 2500 / vector.Z)
            local position = Vector2new(vector.X - size.X/2, vector.Y - size.Y/2)
            
            -- Box ESP
            if ESP.boxes then
                local box = ESP.boxElements[player]
                box.Size = size
                box.Position = position
                box.Color = color
                box.Visible = true
            else
                ESP.boxElements[player].Visible = false
            end
            
            -- Name ESP (Optimized)
            if ESP.names then
                local name = ESP.nameElements[player]
                if ESP.showDistance then
                    name.Text = player.Name .. ' [' .. formatDistance(distance) .. 'm]'
                else
                    name.Text = player.Name
                end
                name.Position = Vector2new(vector.X, position.Y - 15)
                name.Color = ESP.colors.text
                name.Visible = true
            else
                ESP.nameElements[player].Visible = false
            end
            
            -- Tracer ESP
            if ESP.tracers then
                local tracer = ESP.tracerElements[player]
                tracer.From = Vector2new(camera.ViewportSize.X/2, camera.ViewportSize.Y)
                tracer.To = Vector2new(vector.X, vector.Y)
                tracer.Color = color
                tracer.Visible = true
            else
                ESP.tracerElements[player].Visible = false
            end
            
            -- Health ESP
            if ESP.health then
                local healthPercent = humanoid.Health / humanoid.MaxHealth
                local healthBar = ESP.healthElements[player]
                local barWidth = 4
                
                healthBar.background.Size = Vector2new(barWidth, size.Y)
                healthBar.background.Position = Vector2new(position.X - barWidth - 2, position.Y)
                healthBar.background.Visible = true
                
                healthBar.fill.Size = Vector2new(barWidth, size.Y * healthPercent)
                healthBar.fill.Position = Vector2new(position.X - barWidth - 2, position.Y + size.Y * (1 - healthPercent))
                healthBar.fill.Color = Color3new(1 - healthPercent, healthPercent, 0)
                healthBar.fill.Visible = true
                
                healthBar.outline.Size = Vector2new(barWidth, size.Y)
                healthBar.outline.Position = Vector2new(position.X - barWidth - 2, position.Y)
                healthBar.outline.Visible = true

                if ESP.showHealthNumber then
                    healthBar.text.Text = mathfloor(humanoid.Health)
                    healthBar.text.Position = Vector2new(position.X - barWidth - 2, position.Y - 15)
                    healthBar.text.Color = Color3new(1 - healthPercent, healthPercent, 0)
                    healthBar.text.Visible = true
                else
                    healthBar.text.Visible = false
                end
            else
                local healthBar = ESP.healthElements[player]
                healthBar.background.Visible = false
                healthBar.fill.Visible = false
                healthBar.outline.Visible = false
                healthBar.text.Visible = false
            end
        else
            if ESP.boxElements[player] then
                ESP.boxElements[player].Visible = false
                ESP.nameElements[player].Visible = false
                ESP.tracerElements[player].Visible = false
                local healthBar = ESP.healthElements[player]
                healthBar.background.Visible = false
                healthBar.fill.Visible = false
                healthBar.outline.Visible = false
                healthBar.text.Visible = false
            end
        end
    end
end


        

local function IsValidCharacter(character)
    return character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("Head")
end

local function ShouldHighlight(player)
    return player and (player ~= localPlayer) and Settings.ChamsEnabled and (not Settings.TeamCheck or (player.Team ~= localPlayer.Team))
end

local function CreateNameTag(player)
    if not Settings.ShowNames or NameTags[player] then return end

    local character = player.Character
    if not character then return end

    local head = character:FindFirstChild("Head")
    if not head then return end

    if NameTags[player] then
        NameTags[player]:Destroy()
        NameTags[player] = nil
    end

    local Billboard = Instance.new("BillboardGui")
    Billboard.Name = "NameESP"
    Billboard.Size = UDim2.new(0, 200, 0, 50)
    Billboard.StudsOffset = Vector3.new(0, 2, 0)
    Billboard.AlwaysOnTop = true
    Billboard.LightInfluence = 0
    Billboard.MaxDistance = MaxDistance
    Billboard.Adornee = head

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 0, 20)
    Label.BackgroundTransparency = 1
    Label.TextColor3 = Color3.new(1, 1, 1)
    Label.TextStrokeTransparency = 0
    Label.TextStrokeColor3 = Color3.new(0, 0, 0)
    Label.Font = Enum.Font.GothamBold
    Label.TextSize = 14
    Label.Text = player.Name
    Label.Parent = Billboard

    Billboard.Parent = CoreGui
    NameTags[player] = Billboard
end

local function RemoveNameTag(player)
    if NameTags[player] then
        NameTags[player]:Destroy()
        NameTags[player] = nil
    end
end

local function HighlightPlayer(player)
    if not player or not player.Character then return end

    if ESPObjects[player] then
        ESPObjects[player]:Destroy()
        ESPObjects[player] = nil
    end

    local highlight = Instance.new("Highlight")
    highlight.FillColor = (Settings.UseTeamColor and player.Team and player.Team.TeamColor.Color) or ChamsColor
    highlight.FillTransparency = Settings.InnerTransparency
    highlight.OutlineColor = highlight.FillColor
    highlight.OutlineTransparency = 0
    highlight.Parent = player.Character

    ESPObjects[player] = highlight

    if Settings.ShowNames then
        CreateNameTag(player)
    end
end

local function RemoveHighlight(player)
    if ESPObjects[player] then
        ESPObjects[player]:Destroy()
        ESPObjects[player] = nil
        RemoveNameTag(player)
    end
end

local function UpdateHighlight(player)
    if not ESPObjects[player] then return end

    local highlight = ESPObjects[player]
    highlight.FillColor = (Settings.UseTeamColor and player.Team and player.Team.TeamColor.Color) or ChamsColor
    highlight.OutlineColor = highlight.FillColor
    highlight.FillTransparency = Settings.InnerTransparency
end

local function UpdatePlayer(player)
    if ShouldHighlight(player) then
        HighlightPlayer(player)
    else
        RemoveHighlight(player)
    end
end

local function UpdateAllPlayers()
    for _, player in ipairs(Players:GetPlayers()) do
        UpdatePlayer(player)
    end
end

local function SetupPlayer(player)
    if not player or (player == LocalPlayer) then return end

    player.CharacterAdded:Connect(function()
        task.wait(0.1)
        UpdatePlayer(player)
    end)

    player.CharacterRemoving:Connect(function()
        RemoveHighlight(player)
    end)
end

for _, player in ipairs(Players:GetPlayers()) do
    SetupPlayer(player)
    if player.Character then
        UpdatePlayer(player)
    end
end

Players.PlayerAdded:Connect(function(player)
    SetupPlayer(player)
end)

Players.PlayerRemoving:Connect(function(player)
    RemoveHighlight(player)
end)

RunService.Heartbeat:Connect(function()
    if not Settings.ChamsEnabled then return end

    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end

        if ShouldHighlight(player) then
            if not ESPObjects[player] or not ESPObjects[player].Parent then
                UpdatePlayer(player)
            end
        else
            RemoveHighlight(player)
        end
    end
end)



-- Functions
local function getClosestEnemy()
    local closestPlayer = nil
    local shortestDistance = math.huge
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Team ~= localPlayer.Team and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local distance = (localPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).magnitude
            if distance < shortestDistance then
                closestPlayer = player
                shortestDistance = distance
            end
        end
    end
    
    return closestPlayer
end

local function teleportBehind(player)
    if player and player.Character and localPlayer.Character then
        local enemyRoot = player.Character:FindFirstChild("HumanoidRootPart")
        local playerRoot = localPlayer.Character:FindFirstChild("HumanoidRootPart")
        
        if enemyRoot and playerRoot then
            local enemyPosition = enemyRoot.Position
            local enemyLookVector = enemyRoot.CFrame.LookVector
            local newPosition = enemyPosition - (enemyLookVector * teleportDistance)
            playerRoot.CFrame = CFrame.new(newPosition, enemyPosition)
        end
    end
end


local function toggleNoclip()
    if noclipConnection then
        noclipConnection:Disconnect()
        noclipConnection = nil
    end
    
    if noclipEnabled then
        noclipConnection = RunService.Stepped:Connect(function()
            if localPlayer.Character then
                for _, part in pairs(localPlayer.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    end
end


-- Aimbot Functions
local function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer then
            local character = player.Character
            if character and character:FindFirstChild("Head") and character:FindFirstChild("Humanoid") 
            and character.Humanoid.Health > 0 then
                local pos = camera:WorldToScreenPoint(character.Head.Position)
                local mousePos = Vector2.new(UIS:GetMouseLocation().X, UIS:GetMouseLocation().Y)
                local distance = (Vector2.new(pos.X, pos.Y) - mousePos).Magnitude

                if distance < shortestDistance and player.Team ~= localPlayer.Team then
                    closestPlayer = player
                    shortestDistance = distance
                end
            end
        end
    end

    return closestPlayer
end

local function aimAt(player)
    if not player or not player.Character then return end
    
    local head = player.Character:FindFirstChild("Head")
    if not head then return end

    local targetPos = head.Position
    local targetCFrame = CFrame.new(camera.CFrame.Position, targetPos)
    
    camera.CFrame = camera.CFrame:Lerp(targetCFrame, AIM_SMOOTHNESS)
end

-- Introduction Tab
local IntroTab = Window:CreateTab("Introduction")
IntroTab:CreateSection("⭐ Welcome to Counter Blox Overhaul ⭐")
IntroTab:CreateButton({
    Name = "Copy Discord Invite",
    Callback = function()
        setclipboard("https://discord.gg/xX5SVJFJYA")
        Rayfield:Notify({
            Title = "Discord Invite",
            Content = "Discord invite has been copied to your clipboard!",
            Duration = 3,
            Image = 4483362458,
            Actions = {
                Ignore = {
                    Name = "Okay!",
                    Callback = function()
                        print("User acknowledged notification")
                    end
                },
            },
        })
    end
})

-- Aim Tab
local AimTab = Window:CreateTab("Combat")
AimTab:CreateSection("🎯 Targeting Features")

AimTab:CreateToggle({
    Name = "Aimbot",
    CurrentValue = false,
    Flag = "Aimbot",
    Callback = function(Value)
        g.aimbotEnabled = Value
        end,
})

AimTab:CreateSlider({
    Name = "Aim Smoothness",
    Range = {0.1, 1},
    Increment = 0.1,
    Suffix = "",
    CurrentValue = AIM_SMOOTHNESS,
    Flag = "AimSmoothness",
    Callback = function(Value)
        AIM_SMOOTHNESS = Value
    end,
})

-- ESP Tab
local ESPTab = Window:CreateTab("Visuals")
ESPTab:CreateSection("👁️ Player ESP Settings")


-- Main Tab
ESPTab:CreateToggle({
    Name = "Enable ESP",
    CurrentValue = true,
    Flag = "MasterToggle",
    Callback = function(Value)
        ESP.enabled = Value
        if not Value then
            cleanupAllESP()
        end
    end
})

ESPTab:CreateToggle({
    Name = "Boxes",
    CurrentValue = false,
    Callback = function(Value) ESP.boxes = Value end
})

ESPTab:CreateToggle({
    Name = "Names",
    CurrentValue = false,
    Callback = function(Value) ESP.names = Value end
})

ESPTab:CreateToggle({
    Name = "Tracers",
    CurrentValue = false,
    Callback = function(Value) ESP.tracers = Value end
})

ESPTab:CreateToggle({
    Name = "Health Bars",
    CurrentValue = false,
    Callback = function(Value) ESP.health = Value end
})

ESPTab:CreateToggle({
    Name = "Show Health Number",
    CurrentValue = false,
    Callback = function(Value) ESP.showHealthNumber = Value end
})



ESPTab:CreateToggle({
    Name = "Show Distance",
    CurrentValue = false,
    Callback = function(Value) ESP.showDistance = Value end
})

ESPTab:CreateToggle({
    Name = "Team Colors",
    CurrentValue = false,
    Callback = function(Value) ESP.teamColor = Value end
})

ESPTab:CreateSlider({
    Name = "Text Size",
    Range = {8, 24},
    Increment = 1,
    Suffix = "px",
    CurrentValue = 8,
    Callback = function(Value)
        ESP.settings.textSize = Value
        for _, player in pairs(Players:GetPlayers()) do
            if ESP.nameElements[player] then
                ESP.nameElements[player].Size = Value
            end
            if ESP.healthElements[player] then
                ESP.healthElements[player].text.Size = Value
            end
        end
    end
})

ESPTab:CreateSlider({
    Name = "Box Thickness",
    Range = {1, 5},
    Increment = 1,
    Suffix = "px",
    CurrentValue = 1,
    Callback = function(Value)
        ESP.settings.boxThickness = Value
        for _, player in pairs(Players:GetPlayers()) do
            if ESP.boxElements[player] then
                ESP.boxElements[player].Thickness = Value
            end
        end
    end
})

ESPTab:CreateSlider({
    Name = "Tracer Thickness",
    Range = {1, 5},
    Increment = 1,
    Suffix = "px",
    CurrentValue = 1,
    Callback = function(Value)
        ESP.settings.tracerThickness = Value
        for _, player in pairs(Players:GetPlayers()) do
            if ESP.tracerElements[player] then
                ESP.tracerElements[player].Thickness = Value
            end
        end
    end
})

ESPTab:CreateSlider({
    Name = "ESP Transparency",
    Range = {0, 1},
    Increment = 0.1,
    Suffix = "%",
    CurrentValue = 1,
    Callback = function(Value)
        ESP.settings.transparency = Value
        for _, player in pairs(Players:GetPlayers()) do
            if ESP.boxElements[player] then
                ESP.boxElements[player].Transparency = Value
                ESP.nameElements[player].Transparency = Value
                ESP.tracerElements[player].Transparency = Value
                
                local healthBar = ESP.healthElements[player]
                healthBar.background.Transparency = Value
                healthBar.fill.Transparency = Value
                healthBar.outline.Transparency = Value
                healthBar.text.Transparency = Value
            end
        end
    end
})

ESPTab:CreateSlider({
    Name = "Max Distance",
    Range = {100, 5000},
    Increment = 100,
    Suffix = "m",
    CurrentValue = 1000,
    Callback = function(Value)
        ESP.maxDistance = Value
    end
})

ESPTab:CreateColorPicker({
    Name = "ESP Color",
    Color = ESP.colors.box,
    Callback = function(Value)
        ESP.colors.box = Value
        ESP.colors.tracer = Value
        for _, player in pairs(Players:GetPlayers()) do
            if not ESP.teamColor and ESP.boxElements[player] then
                ESP.boxElements[player].Color = Value
                ESP.tracerElements[player].Color = Value
            end
        end
    end
})

ESPTab:CreateColorPicker({
    Name = "Text Color",
    Color = ESP.colors.text,
    Callback = function(Value)
        ESP.colors.text = Value
        for _, player in pairs(Players:GetPlayers()) do
            if ESP.nameElements[player] then
                ESP.nameElements[player].Color = Value
            end
        end
    end
})

-- Chams Tab
local ChamsTab = Window:CreateTab("Chams")
ChamsTab:CreateSection("💫 Player Highlights")

local EnableChamsToggle = ChamsTab:CreateToggle({
    Name = "Enable Chams",
    CurrentValue = false,
    Flag = "ChamsEnabled",
    Callback = function(value)
        Settings.ChamsEnabled = value
        UpdateAllPlayers()
    end
})

local ChamsColorPicker = ChamsTab:CreateColorPicker({
    Name = "Chams Color",
    Color = ChamsColor,
    Flag = "ChamsColor",
    Callback = function(value)
        ChamsColor = value
        if not Settings.UseTeamColor then
            for _, player in ipairs(Players:GetPlayers()) do
                UpdateHighlight(player)
            end
        end
    end
})

local TransparencySlider = ChamsTab:CreateSlider({
    Name = "Inner Visibility",
    Range = {0, 1},
    Increment = 0.1,
    Suffix = "Transparency",
    CurrentValue = 1,
    Flag = "InnerTransparency",
    Callback = function(value)
        Settings.InnerTransparency = value
        for _, player in ipairs(Players:GetPlayers()) do
            UpdateHighlight(player)
        end
    end
})

local TeamCheckToggle = ChamsTab:CreateToggle({
    Name = "Don't Show Teammates",
    CurrentValue = false,
    Flag = "TeamCheck",
    Callback = function(value)
        Settings.TeamCheck = value
        UpdateAllPlayers()
    end
})

local UseTeamColorToggle = ChamsTab:CreateToggle({
    Name = "Use Team Colors",
    CurrentValue = false,
    Flag = "TeamColors",
    Callback = function(value)
        Settings.UseTeamColor = value
        UpdateAllPlayers()
    end
})

local ShowNamesToggle = ChamsTab:CreateToggle({
    Name = "Show Names",
    CurrentValue = false,
    Flag = "ShowNames",
    Callback = function(value)
        Settings.ShowNames = value
        UpdateAllPlayers()
    end
})



local MiscTab = Window:CreateTab("Misc")
ChamsTab:CreateSection("Additional Features")



MiscTab:CreateToggle({
    Name = "Noclip",
    CurrentValue = false,
    Flag = "Noclip",
    Callback = function(Value)
        noclipEnabled = Value
        toggleNoclip()
    end
})

local Label = MiscTab:CreateLabel("Kill Aura is prefect for fighting other exploiters", 4483362458, Color3.fromRGB(0, 155, 255), true) -- Title, Icon, Color, IgnoreTheme

local KillAuraToggle = MiscTab:CreateToggle({
    Name = "Kill Aura",
    CurrentValue = false,
    Flag = "KillAuraToggle",
    Callback = function(Value)
        isKillAuraEnabled = Value
       
    end,
})

-- Create Slider
local DistanceSlider = MiscTab:CreateSlider({
    Name = "Teleport Distance",
    Range = {1, 10},
    Increment = 0.5,
    Suffix = "Studs",
    CurrentValue = 3,
    Flag = "TeleportDistance",
    Callback = function(Value)
        teleportDistance = Value
    end,
})

-- Create Button
MiscTab:CreateButton({
    Name = "Reset Position",
    Callback = function()
        if localPlayer.Character then
            localPlayer.Character:FindFirstChild("Humanoid").Health = 0
        end
    end,
})

-- Main Loop
RunService.Heartbeat:Connect(function()
    if isKillAuraEnabled then
        local closestEnemy = getClosestEnemy()
        if closestEnemy then
            teleportBehind(closestEnemy)
            
            pcall(function()
                local args = {
                    [1] = closestEnemy.Character.Head,
                    [2] = closestEnemy.Character.Head.Position,
                    [3] = "Scout",
                    [4] = 100,
                    [5] = localPlayer.Character.Gun
                }
                game:GetService("ReplicatedStorage").Events.DamagePlayer:FireServer(unpack(args))
            end)
        end
    end
end)




-- Aimbot Connection
local aimLoop

UIS.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        g.aim = true
        
        if g.aimbotEnabled and not aimLoop then
            aimLoop = RunService.RenderStepped:Connect(function()
                if g.aim and g.aimbotEnabled then
                    local target = getClosestPlayer()
                    if target then
                        aimAt(target)
                    end
                end
            end)
        end
    end
end)

UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        g.aim = false
        if aimLoop then
            aimLoop:Disconnect()
            aimLoop = nil
        end
    end
end)

-- Clean up
game.Players.LocalPlayer.OnTeleport:Connect(function()
    if aimLoop then
        aimLoop:Disconnect()
    end
end)

CoreGui.ChildRemoved:Connect(function(child)
    if child.Name == "Rayfield" then
        for _, object in pairs(ESPObjects) do
            object:Destroy()
        end
        for _, tag in pairs(NameTags) do
            tag:Destroy()
        end
        table.clear(ESPObjects)
        table.clear(NameTags)
    end
end)

game.Players.LocalPlayer.CharacterAdded:Connect(function()
    if noclipEnabled then
        toggleNoclip()
    end
end)

-- Connect update function
RunService.RenderStepped:Connect(updateESP)

-- Cleanup on player removal
Players.PlayerRemoving:Connect(cleanupESPElement)
