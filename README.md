local function Check(productName, key)
    local hwid = game:GetService("RbxAnalyticsService"):GetClientId()
    if not hwid or not productName then
        return false
    end
    local data = {
        action = "checkAndSetHwid",
        productName = productName,
        key = key,
        hwid = hwid
    }
    local request = http_request or request or (http and http.request) or (syn and syn.request)
    local jsonData = game:GetService("HttpService"):JSONEncode(data)
    local response = request{
        Url = "https://servicemain.vercel.app/api/whitelist",
        Method = 'POST',
        Headers = {
            ['Content-Type'] = 'application/json'
        },
        Body = jsonData
    }
    if response.StatusCode == 200 then
        local result = game:GetService("HttpService"):JSONDecode(response.Body)

        if result.status == 'success' then
            return true
        end
    else
        local result = game:GetService("HttpService"):JSONDecode(response.Body)
        if result.Success then
            return true
        end
    end
    local result = game:GetService("HttpService"):JSONDecode(response.Body)
    game.Players.LocalPlayer:Kick(result.message or "Error: Unknown issue")
    return false
end
local product = "lerk.lua"
script_key = script_key or ""
local checkdone = Check(product, script_key) or false
if not checkdone then return end
local setting = getgenv().setting
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local TextButton = Instance.new("TextButton")
local UITextSizeConstraint = Instance.new("UITextSizeConstraint")

ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.BackgroundTransparency = 0
Frame.Position = UDim2.new(1, -90, 0, 0)
Frame.Size = UDim2.new(0, 80, 0, 40)
TextButton.Parent = Frame
TextButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextButton.BackgroundTransparency = 1.000
TextButton.Size = UDim2.new(1, 0, 1, 0)
TextButton.Font = Enum.Font.Code
TextButton.Text = "Violent GUI"
TextButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TextButton.TextScaled = true
TextButton.TextSize = 30
TextButton.TextStrokeColor3 = Color3.fromRGB(255, 255, 255)
TextButton.TextStrokeTransparency = 0.000
TextButton.TextWrapped = true
TextButton.MouseButton1Down:Connect(function()
    Library:Toggle()
end)

UITextSizeConstraint.Parent = TextButton
UITextSizeConstraint.MaxTextSize = 30

local player = game.Players.LocalPlayer
local function onCharacterAdded(character)
    ScreenGui.Parent = player.PlayerGui
end

local function connectCharacterAdded()
    player.CharacterAdded:Connect(onCharacterAdded)
end

connectCharacterAdded()

player.CharacterRemoving:Connect(function()
    ScreenGui.Parent = nil
end)

local setting = getgenv().setting
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local CamlockState = setting.camlock.enabled or true
local SmoothEnabled = setting.camlock.smoothenabled or true
local Smoothness = setting.camlock.smoothvalue or 1
local LPrediction = setting.prediction.prediction or 0.122
local DistanceThreshold = setting.prediction.distancethreshold or 25
local JumpOffset = setting.prediction.jumpoffset or 0.08
local AirshotPart = setting.aimpart or "UpperTorso"
local TargetAimEnabled = setting.targetaim or true
local SilentAimTarget = nil
local camera = workspace.CurrentCamera
local originalCFrame = camera.CFrame
local runningConnection = nil
local delayair = setting.autoair.delay or 0.2
local l = loadstring(game:HttpGet("https://raw.githubusercontent.com/laagginq/ui-libraries/main/dxhooknotify/src.lua", true))()
local function FindClosestNiggaToCenter()
    local closestPlayer = nil
    local closestDistance = math.huge
    local screenCenter = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y / 2)

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local character = player.Character
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            local position, onScreen = workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position)

            if onScreen then
                local distance = (screenCenter - Vector2.new(position.X, position.Y)).Magnitude
                if distance < closestDistance then
                    closestPlayer = player
                    closestDistance = distance
                end
            end
        end
    end

    return closestPlayer
end

-- stupid ass button
local gui = Instance.new("ScreenGui")
gui.Name = "Dukeshadow"
gui.Parent = game.CoreGui
local TextButton = Instance.new("TextButton")
TextButton.Text = "Violent.lol"
TextButton.TextSize = 25
TextButton.TextColor3 = Color3.new(1, 1, 1)
TextButton.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
TextButton.BorderColor3 = Color3.new(255, 0, 0)
TextButton.BorderSizePixel = 4
TextButton.BackgroundTransparency = 1
TextButton.Font = Enum.Font.Code
TextButton.Size = UDim2.new(0.2, 0, 0.2, 0)
TextButton.Position = UDim2.new(0, 0.9, 0.3, 1)
TextButton.Parent = gui
TextButton.Draggable = true
local cornerUI = Instance.new("UICorner")
cornerUI.CornerRadius = UDim.new(0, 5)
cornerUI.Parent = TextButton

local TextButton1 = Instance.new("TextButton")
TextButton1.Text = "Auto air"
TextButton1.TextSize = 25
TextButton1.TextColor3 = Color3.new(1, 1, 1)
TextButton1.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
TextButton1.BorderColor3 = Color3.new(255, 0, 0)
TextButton1.BorderSizePixel = 4
TextButton1.BackgroundTransparency = 1
TextButton1.Font = Enum.Font.Code
TextButton1.Size = UDim2.new(0.2, 0, 0.2, 0)
TextButton1.Position = UDim2.new(0, 0.9, 0.3, 1)
TextButton1.Parent = gui
TextButton1.Draggable = true
local cornerUI1 = Instance.new("UICorner")
cornerUI1.CornerRadius = UDim.new(0, 5)
cornerUI1.Parent = TextButton1

local TextButton2 = Instance.new("TextButton")
TextButton2.Text = "CFrame"
TextButton2.TextSize = 25
TextButton2.TextColor3 = Color3.new(1, 1, 1)
TextButton2.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
TextButton2.BorderColor3 = Color3.new(255, 0, 0)
TextButton2.BorderSizePixel = 4
TextButton2.BackgroundTransparency = 1
TextButton2.Font = Enum.Font.Code
TextButton2.Size = UDim2.new(0.2, 0, 0.2, 0)
TextButton2.Position = UDim2.new(0, 0.9, 0.3, 1)
TextButton2.Parent = gui
TextButton2.Draggable = true
local cornerUI2 = Instance.new("UICorner")
cornerUI2.CornerRadius = UDim.new(0, 5)
cornerUI2.Parent = TextButton1
TextButton2.MouseButton1Click:Connect(function()
setting.CFrameSpeed.enabled = not setting.CFrameSpeed.enabled
TextButton2.TextColor3 = setting.CFrameSpeed.enabled and Color3.new(0, 1, 0) or Color3.new(1, 1, 1)
end)
TextButton1.MouseButton1Click:Connect(function()
setting.autoair.enabled = not setting.autoair.enabled
TextButton1.TextColor3 = setting.autoair.enabled and Color3.new(0, 1, 0) or Color3.new(1, 1, 1)
end)
TextButton.MouseButton1Click:Connect(function()
    TargetAimEnabled = not TargetAimEnabled

    if TargetAimEnabled then
        SilentAimTarget = FindClosestNiggaToCenter()

        
        if SilentAimTarget then
            runningConnection = RunService.Heartbeat:Connect(function(deltaTime)
            TextButton.TextColor3 = Color3.new(0, 1, 0)
                if TargetAimEnabled and SilentAimTarget and SilentAimTarget.Character and SilentAimTarget.Character:FindFirstChild("HumanoidRootPart") then
                    local character = SilentAimTarget.Character
                    local targetPosition

                    
                    if character.Humanoid.FloorMaterial == Enum.Material.Air then
                        local head = character:FindFirstChild(AirshotPart)
                        if head then
                            targetPosition = head.Position + Vector3.new(0,JumpOffset,0)
                        else
                            targetPosition = character.Head.Position + Vector3.new(0,JumpOffset,0)
                        end
                    else
                        
                        targetPosition = character.Head.Position
                    end

                    local predictionValue = LPrediction
                    local targetCFrame = CFrame.new(camera.CFrame.p, targetPosition + (character.HumanoidRootPart.Velocity * predictionValue))
                    if targetCFrame.Y < 0 and setting.antigroundshoot then
                    targetCFrame = Vector3.new(targetCFrame.X,0,targetCFrame.Z)
                    end

                    if CamlockState then
                    if SmoothEnabled then
                        camera.CFrame = camera.CFrame:Lerp(targetCFrame, Smoothness)
                    else
                        camera.CFrame = targetCFrame
                    end
                    end
                end
            end)
        end
    else
        if runningConnection then
            runningConnection:Disconnect()
            TextButton.TextColor3 = Color3.new(1, 1, 1)
        end
        SilentAimTarget = "8blaq is nigger"
    end
end)
RunService.Heartbeat:Connect(function()
   if setting.autoair.enabled and SilentAimTarget ~= "8blaq is nigger" and SilentAimTarget.Character then
       local TargetRootPart = SilentAimTarget.Character:FindFirstChild("HumanoidRootPart")
       if TargetRootPart then
           local humanoid = SilentAimTarget.Character:FindFirstChild("Humanoid")
           if humanoid and humanoid:GetState() == Enum.HumanoidStateType.Jumping or humanoid:GetState() == Enum.HumanoidStateType.Freefall then
               local Character = game:GetService("Players").LocalPlayer.Character
               if Character then
                   local Tool = Character:FindFirstChildOfClass("Tool")
                   if Tool then
                       Tool:Activate()
                   end
               end
           end
       end
   end
end)
local mt = getrawmetatable(game)
local oldNamecall = mt.__namecall
setreadonly(mt, false)

local function nigga(arg)
    if typeof(arg) == "table" then
        for key, value in pairs(arg) do
            if typeof(value) == "table" then
                nigga(value)
            elseif typeof(value) == "Vector3" then
                local targetPart = SilentAimTarget.Character.HumanoidRootPart
                local predictionValue = LPrediction
                local targetCFrame = targetPart.Position + (targetPart.Velocity * predictionValue)
                 if targetCFrame.Y < 0 and setting.antigroundshoot then
                    targetCFrame = Vector3.new(targetCFrame.X,0,targetCFrame.Z)
                    end
                arg[key] = targetCFrame
            end
        end
    elseif typeof(arg) == "Vector3" then
        local targetPart = SilentAimTarget.Character.HumanoidRootPart
        local predictionValue = LPrediction
                        local targetCFrame = targetPart.Position + (targetPart.Velocity * predictionValue)
                 if targetCFrame.Y < 0 and setting.antigroundshoot then
                    targetCFrame = Vector3.new(targetCFrame.X,0,targetCFrame.Z)
                    end
        return targetCFrame
end
    return arg
end

mt.__namecall = newcclosure(function(self, ...)
    local args = {...}
    local method = getnamecallmethod()

    if TargetAimEnabled and SilentAimTarget ~= "8blaq is nigger" and method == "FireServer" then
        for i, arg in ipairs(args) do
            if typeof(arg) == "Vector3" or typeof(arg) == "table" then
                args[i] = nigga(arg)
            end
        end
    end

    return oldNamecall(self, unpack(args))
end)

setreadonly(mt, true)


local function NewLine(thickness, color)
    local line = Drawing.new("Line")
    line.Thickness = thickness
    line.Color = color
    line.Transparency = 1
    line.Visible = false
    return line
end

local function NewText(color, size, font)
    local text = Drawing.new("Text")
    text.Color = color
    text.Size = size
    text.Font = font
    text.Outline = true
    text.OutlineColor = Color3.fromRGB(0, 0, 0)
    text.Visible = false
    return text
end

local function CreateESPComponents(player)
    local lines = {
        top = NewLine(1, setting.ESP.Box_Color),
        bottom = NewLine(1, setting.ESP.Box_Color),
        left = NewLine(1, setting.ESP.Box_Color),
        right = NewLine(1, setting.ESP.Box_Color)
    }

    local nameESP = NewText(setting.ESP.Name_Color, setting.ESP.Font_Size, setting.ESP.Font)
    local distanceESP = NewText(setting.ESP.Distance_Color, setting.ESP.Font_Size, setting.ESP.Font)
    local hpText = NewText(Color3.fromRGB(0, 255, 0), setting.ESP.Font_Size, setting.ESP.Font)

    return lines, nameESP, distanceESP, hpText
end

local function UpdateESP(lines, nameESP, distanceESP, hpText, player)
    if not setting.ESP.Enabled then
        for _, line in pairs(lines) do
            line.Visible = false
        end
        nameESP.Visible = false
        distanceESP.Visible = false
        hpText.Visible = false
        return
    end

    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local rootPos = player.Character.HumanoidRootPart.Position
        local distance = (rootPos - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
        
        if distance <= math.huge then
            local rootScreenPos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(rootPos)
            if onScreen then
                local headPos = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
                local legPos = workspace.CurrentCamera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position - Vector3.new(0, 3, 0))

                local boxHeight = (headPos - legPos).Magnitude * 1.5
                local boxWidth = boxHeight / 2 * 1.3

                if setting.ESP.BoxEsp then
                    lines.top.From = Vector2.new(rootScreenPos.X - boxWidth / 2, rootScreenPos.Y - boxHeight / 2)
                    lines.top.To = Vector2.new(rootScreenPos.X + boxWidth / 2, rootScreenPos.Y - boxHeight / 2)
                    lines.bottom.From = Vector2.new(rootScreenPos.X - boxWidth / 2, rootScreenPos.Y + boxHeight / 2)
                    lines.bottom.To = Vector2.new(rootScreenPos.X + boxWidth / 2, rootScreenPos.Y + boxHeight / 2)
                    lines.left.From = Vector2.new(rootScreenPos.X - boxWidth / 2, rootScreenPos.Y - boxHeight / 2)
                    lines.left.To = Vector2.new(rootScreenPos.X - boxWidth / 2, rootScreenPos.Y + boxHeight / 2)
                    lines.right.From = Vector2.new(rootScreenPos.X + boxWidth / 2, rootScreenPos.Y - boxHeight / 2)
                    lines.right.To = Vector2.new(rootScreenPos.X + boxWidth / 2, rootScreenPos.Y + boxHeight / 2)

                    for _, line in pairs(lines) do
                        line.Color = setting['ESP']['Box_Color']
                        line.Visible = true
                    end
                else
                    for _, line in pairs(lines) do
                        line.Visible = false
                    end
                end

                if setting.ESP.NameEsp then
                    nameESP.Position = Vector2.new(rootScreenPos.X, rootScreenPos.Y - boxHeight / 2 - 15)
                    nameESP.Text = player.Name
                    nameESP.Visible = true
                    nameESP.Color = setting.ESP.Name_Color
                else
                    nameESP.Visible = false
                end

                if setting.ESP.DistanceEsp then
                    distanceESP.Position = Vector2.new(rootScreenPos.X, rootScreenPos.Y + boxHeight / 2 + 5)
                    distanceESP.Text = string.format("%.1f", distance) .. " studs"
                    distanceESP.Visible = true
                    distanceESP.Color = setting.ESP.Distance_Color
                else
                    distanceESP.Visible = false
                end

                if setting.ESP.hpesp then
                    hpText.Visible = true
                    hpText.Position = Vector2.new(rootScreenPos.X, rootScreenPos.Y - boxHeight / 2 - 25)
                    hpText.Text = string.format("HP: %d", player.Character:FindFirstChildOfClass("Humanoid").Health)
                else
                    hpText.Visible = false
                end
            else
                for _, line in pairs(lines) do
                    line.Visible = false
                end
                nameESP.Visible = false
                distanceESP.Visible = false
                hpText.Visible = false
            end
        else
            for _, line in pairs(lines) do
                line.Visible = false
            end
            nameESP.Visible = false
            distanceESP.Visible = false
            hpText.Visible = false
        end
    else
        for _, line in pairs(lines) do
            line.Visible = false
        end
        nameESP.Visible = false
        distanceESP.Visible = false
        hpText.Visible = false
    end
end

local function ESP(player)
    if player == game.Players.LocalPlayer then
        return
    end

    local lines, nameESP, distanceESP, hpText = CreateESPComponents(player)

    game:GetService("RunService").RenderStepped:Connect(function()
        UpdateESP(lines, nameESP, distanceESP, hpText, player)
    end)
end

for _, player in ipairs(game.Players:GetPlayers()) do
    ESP(player)
end

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        ESP(player)
    end)
end)

game.Players.PlayerRemoving:Connect(function(player)
    if player.Character then
        for _, component in pairs(player.Character:GetChildren()) do
            if component:IsA("Drawing") then
                component:Remove()
            end
        end
    end
end)
camera.CFrame = originalCFrame

loadstring(game:HttpGet("https://raw.githubusercontent.com/Pixeluted/adoniscries/main/Source.lua",true))()

local autopred = setting.prediction.autopred or false
local lastTime = tick()
local pingTotal = 0
local pingCount = 0
local ClosePrediction = 0.1
local FarPrediction = 0.2

RunService.Heartbeat:Connect(function()
if setting.CFrameSpeed.enabled then
	pcall(function()
	local LocalPlayer = game.Players.LocalPlayer;
	if (LocalPlayer.Character.HumanoidRootPart and game.Players.LocalPlayer.Character) then
		LocalPlayer.Character.HumanoidRootPart.CFrame = LocalPlayer.Character.HumanoidRootPart.CFrame + (LocalPlayer.Character.Humanoid.MoveDirection * setting.CFrameSpeed.value);
	end
end)
end
end)
RunService.Heartbeat:Connect(function()
    if autopred then
        local currentTime = tick()
        local ping = (currentTime - lastTime) * 1000
        lastTime = currentTime
        pingTotal = pingTotal + ping
        pingCount = pingCount + 1

        
        local avgPing = pingTotal / pingCount
        ClosePrediction = 0.1 + (avgPing / 2000)
        FarPrediction = 0.2 + (avgPing / 1000)
    end
end)


local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/LionTheGreatRealFrFr/MobileLinoriaLib/main/Library.lua"))()
local ThemeManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/LionTheGreatRealFrFr/MobileLinoriaLib/main/addons/ThemeManager.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/LionTheGreatRealFrFr/MobileLinoriaLib/main/addons/SaveManager.lua"))()

local Window = Library:CreateWindow({
    Title = "Violent",
    Center = true,
    AutoShow = true,
    Position = UDim2.new(0.5, -200, 0.5, -150),
    Size = UDim2.new(0, 400, 0, 300)
})

local Tabs = {
    Main = Window:AddTab("Main Settings"),
    ESP = Window:AddTab("Visuals"),
    UISettings = Window:AddTab("UI Settings")
}

local MainSettings = Tabs.Main:AddLeftGroupbox("Main Settings")
MainSettings:AddDropdown("aimpart", {
    Text = "Aim Part",
    Values = {"Head", "UpperTorso", "HumanoidRootPart"},
    Default = "UpperTorso",
    Callback = function(value)
        AirshotPart = value
    end
})

MainSettings:AddToggle("targetaim", {
    Text = "Enable Target Aim",
    Default = TargetAimEnabled,
    Callback = function(value)
        TargetAimEnabled = value
    end
})

MainSettings:AddToggle("camlock_enabled", {
    Text = "Enable Camlock",
    Default = CamlockState,
    Callback = function(value)
        CamlockState = value
    end
})
MainSettings:AddToggle("Violent", {
    Text = "Enable Anti Ground Shoot",
    Default = setting.antigroundshoot,
    Callback = function(value)
        setting.antigroundshoot = value
    end
})

MainSettings:AddToggle("camlock_smoothenabled", {
    Text = "Enable Smooth",
    Default = SmoothEnabled,
    Callback = function(value)
        SmoothEnabled = value
    end
})

MainSettings:AddInput("camlock_smoothvalue", {
    Text = "Smoothness Value",
    Default = Smoothness,
    Numeric = true,
    Callback = function(value)
        Smoothness = tonumber(value)
    end
})

MainSettings:AddToggle("autopred", {
    Text = "Auto Prediction",
    Default = autopred,
    Callback = function(value)
        autopred = value
    end
})

MainSettings:AddInput("prediction_value", {
    Text = "Prediction Value",
    Default = LPredictionn,
    Numeric = true,
    Callback = function(value)
        LPrediction = tonumber(value)
    end
})

MainSettings:AddInput("jumpoffset", {
    Text = "Jump Offset",
    Default = JumpOffset,
    Numeric = true,
    Callback = function(value)
        JumpOffset = tonumber(value)
    end
})

MainSettings:AddInput("autoair_delay", {
    Text = "Auto Air Delay",
    Default = delayair,
    Numeric = true,
    Callback = function(value)
        delayair = tonumber(value)
    end
})

local CFrameSpeedSettings = Tabs.Main:AddRightGroupbox("CFrame Speed Settings")
CFrameSpeedSettings:AddToggle("cframespeed_enabled", {
    Text = "Enable CFrame Speed",
    Default = setting.CFrameSpeed.enabled,
    Callback = function(value)
        setting.CFrameSpeed.enabled = value
    end
})

CFrameSpeedSettings:AddInput("cframespeed_value", {
    Text = "Speed Value",
    Default = setting.CFrameSpeed.value,
    Numeric = true,
    Callback = function(value)
        setting.CFrameSpeed.value = tonumber(value)
    end
})

local ESPSettings = Tabs.ESP:AddLeftGroupbox("ESP Settings")
ESPSettings:AddToggle("esp_enabled", {
    Text = "Enable ESP",
    Default = setting.ESP.Enabled,
    Callback = function(value)
        setting.ESP.Enabled = value
    end
})

ESPSettings:AddToggle("boxesp", {
    Text = "Box ESP",
    Default = setting.ESP.BoxEsp,
    Callback = function(value)
        setting.ESP.BoxEsp = value
    end
}):AddColorPicker("box_color", {
    Default = setting.ESP.Box_Color,
    Title = "Box Color",
    Callback = function(value)
        setting.ESP.Box_Color = value
    end
})

ESPSettings:AddToggle("nameesp", {
    Text = "Name ESP",
    Default = setting.ESP.NameEsp,
    Callback = function(value)
        setting.ESP.NameEsp = value
    end
}):AddColorPicker("name_color", {
    Default = setting.ESP.Name_Color,
    Title = "Name Color",
    Callback = function(value)
        setting.ESP.Name_Color = value
    end
})

local MenuGroup = Tabs['UISettings']:AddLeftGroupbox('Menu')
MenuGroup:AddButton('Unload', function() Library:Unload() end)
MenuGroup:AddLabel('Menu bind'):AddKeyPicker('MenuKeybind', { Default = 'End', NoUI = true, Text = 'Menu keybind' })

Library.ToggleKeybind = Options.MenuKeybind

ThemeManager:SetLibrary(Library)
SaveManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({ 'MenuKeybind' })
ThemeManager:SetFolder('8blaqlua')
SaveManager:SetFolder('8blaqlua/configs')
SaveManager:BuildConfigSection(Tabs['UISettings'])
ThemeManager:ApplyToTab(Tabs['UISettings'])
SaveManager:LoadAutoloadConfig()

Library:OnUnload(function()
    print("UI Library unloaded!")
end)

Library.ToggleKeybind = Enum.KeyCode.RightShift
