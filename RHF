-- 🔰 Nacro Hub | Rayfield Edition (Rage + Misc + Knife Tabs)

local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

local Window = Rayfield:CreateWindow({
    Name = "🔫RHF Hub🔫",
    LoadingTitle = "㊙️Loading RHF Hub㊙️",
    LoadingSubtitle = "👑by NSTG♡BDF👑",
    ConfigurationSaving = {Enabled = false},
    Discord = {Enabled = false},
    KeySystem = false
})

-- === Tabs ===
local RageTab  = Window:CreateTab("💙Rage💙")   -- Hitbox
local MiscTab  = Window:CreateTab("🖤Misc🖤")   -- Utilities, ESP, Push
local KnifeTab = Window:CreateTab("🤍Knife🤍")  -- Knife Mod

---------------------------------------------------------------------
-- RAGE TAB (Hitbox System)
---------------------------------------------------------------------
local Tab = RageTab
Tab:CreateLabel("Universal Hitbox v1.0")

local hitboxEnabled = false
local noCollisionEnabled = false
local hitbox_original_properties = {}
local hitboxSize = 9
local hitboxTransparency = 6
local teamCheck = "FFA"

local defaultBodyParts = {"UpperTorso","Head","HumanoidRootPart"}
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function savedPart(player, part)
    if not hitbox_original_properties[player] then
        hitbox_original_properties[player] = {}
    end
    if not hitbox_original_properties[player][part.Name] then
        hitbox_original_properties[player][part.Name] = {
            CanCollide = part.CanCollide,
            Transparency = part.Transparency,
            Size = part.Size
        }
    end
end

local function restoredPart(player)
    if hitbox_original_properties[player] then
        for partName, properties in pairs(hitbox_original_properties[player]) do
            local part = player.Character and player.Character:FindFirstChild(partName)
            if part and part:IsA("BasePart") then
                part.CanCollide = properties.CanCollide
                part.Transparency = properties.Transparency
                part.Size = properties.Size
            end
        end
    end
end

local function findClosestPart(player, partName)
    if not player.Character then return nil end
    for _, part in ipairs(player.Character:GetChildren()) do
        if part:IsA("BasePart") and part.Name:lower():match(partName:lower()) then
            return part
        end
    end
    return nil
end

local function extendHitbox(player)
    for _, partName in ipairs(defaultBodyParts) do
        local part = player.Character and (player.Character:FindFirstChild(partName) or findClosestPart(player, partName))
        if part and part:IsA("BasePart") then
            savedPart(player, part)
            part.CanCollide = not noCollisionEnabled
            part.Transparency = hitboxTransparency / 10
            part.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
        end
    end
end

local function isEnemy(player)
    if teamCheck == "FFA" or teamCheck == "Everyone" then
        return true
    end
    return player.Team ~= LocalPlayer.Team
end

local function shouldExtendHitbox(player)
    return isEnemy(player)
end

local function updateHitboxes()
    for _, v in ipairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
            if shouldExtendHitbox(v) then
                extendHitbox(v)
            else
                restoredPart(v)
            end
        end
    end
end

local function checkForDeadPlayers()
    for player, _ in pairs(hitbox_original_properties) do
        if not player.Parent or not player.Character or not player.Character:IsDescendantOf(game) then
            restoredPart(player)
            hitbox_original_properties[player] = nil
        end
    end
end

local function onPlayerAdded(player)
    player.CharacterAdded:Connect(function()
        task.wait(0.1)
        if hitboxEnabled then updateHitboxes() end
    end)
    player.CharacterRemoving:Connect(function()
        restoredPart(player)
        hitbox_original_properties[player] = nil
    end)
end
for _, player in ipairs(Players:GetPlayers()) do onPlayerAdded(player) end
Players.PlayerAdded:Connect(onPlayerAdded)

task.spawn(function()
    while true do
        if hitboxEnabled then
            updateHitboxes()
            checkForDeadPlayers()
        end
        task.wait(0.1)
    end
end)

Tab:CreateToggle({
    Name = "Enable Hitbox",
    CurrentValue = false,
    Callback = function(v)
        hitboxEnabled = v
        if not v then
            for _, p in ipairs(Players:GetPlayers()) do restoredPart(p) end
            hitbox_original_properties = {}
        else
            updateHitboxes()
        end
    end
})

Tab:CreateSlider({
    Name = "Hitbox Size",
    Range = {1, 50},
    Increment = 1,
    CurrentValue = hitboxSize,
    Callback = function(v)
        hitboxSize = v
        if hitboxEnabled then updateHitboxes() end
    end
})

Tab:CreateSlider({
    Name = "Hitbox Transparency (1-10)",
    Range = {1, 10},
    Increment = 1,
    CurrentValue = hitboxTransparency,
    Callback = function(v)
        hitboxTransparency = v
        if hitboxEnabled then updateHitboxes() end
    end
})

Tab:CreateDropdown({
    Name = "Team Check",
    Options = {"FFA","Team-Based","Everyone"},
    CurrentOption = "FFA",
    Callback = function(v)
        teamCheck = v
        if hitboxEnabled then updateHitboxes() end
    end
})

Tab:CreateToggle({
    Name = "No Collision",
    CurrentValue = false,
    Callback = function(v)
        noCollisionEnabled = v
        if hitboxEnabled then updateHitboxes() end
    end
})

---------------------------------------------------------------------
-- MISC TAB (Anti-Flash, Instant Use, ESP, Push)
---------------------------------------------------------------------
local antiFlashConnections = {}
local antiFlashActive = false

MiscTab:CreateToggle({
    Name = "Anti Flashbang",
    CurrentValue = false,
    Callback = function(v)
        if v and not antiFlashActive then
            antiFlashActive = true
            local cam = workspace.CurrentCamera

            for _, v in ipairs(cam:GetChildren()) do
                if v:IsA("ColorCorrectionEffect") and v.Name == "Stun" then v:Destroy() end
                if v:IsA("Sound") and (v.Name:lower():find("stun") or v.Name:lower():find("boom")) then v:Destroy() end
            end

            table.insert(antiFlashConnections, cam.ChildAdded:Connect(function(child)
                if not antiFlashActive then return end
                if child:IsA("ColorCorrectionEffect") and child.Name == "Stun" then child:Destroy() end
                if child:IsA("Sound") and (child.Name:lower():find("stun") or child.Name:lower():find("boom")) then child:Destroy() end
            end))

            Rayfield:Notify({Title="Anti-Flashbang", Content="ENABLED"})
        elseif not v and antiFlashActive then
            antiFlashActive = false
            for _, c in ipairs(antiFlashConnections) do c:Disconnect() end
            antiFlashConnections = {}
            Rayfield:Notify({Title="Anti-Flashbang", Content="DISABLED"})
        end
    end
})

-- ✅ Instant Use
local instantUseActive = false
local instantConnections = {}

MiscTab:CreateToggle({
    Name = "Instant Use Tools",
    CurrentValue = false,
    Callback = function(v)
        local lp = Players.LocalPlayer
        local backpack = lp:WaitForChild("Backpack")

        if v and not instantUseActive then
            instantUseActive = true

            local function setupPeel(peel)
                if not peel or peel:GetAttribute("PeelModded") then return end
                peel:SetAttribute("PeelModded", true)
                local throw = peel:FindFirstChild("Throw")
                if not throw or not throw:IsA("RemoteEvent") then return end
                table.insert(instantConnections, peel.Activated:Connect(function()
                    if not instantUseActive then return end
                    local char = lp.Character
                    if not char then return end
                    local root = char:FindFirstChild("HumanoidRootPart")
                    if root then throw:FireServer(root.CFrame) end
                end))
            end

            local function setupFlashbang(fb)
                if not fb or fb:GetAttribute("FlashbangModded") then return end
                fb:SetAttribute("FlashbangModded", true)
                local remote = fb:FindFirstChild("Remote")
                if not remote or not remote:IsA("RemoteEvent") then return end
                local config = fb:FindFirstChild("Config")
                local velocity = config and config:FindFirstChild("Velocity") and config.Velocity.Value or 60
                local mouse = lp:GetMouse()
                table.insert(instantConnections, fb.Activated:Connect(function()
                    if not instantUseActive then return end
                    if mouse then remote:FireServer(mouse.Hit, velocity) end
                end))
            end

            local function setupBandage(bandage)
                if not bandage or bandage:GetAttribute("BandageModded") then return end
                bandage:SetAttribute("BandageModded", true)
                local handle = bandage:FindFirstChild("Handle")
                local useSound = handle and handle:FindFirstChild("UseSound")
                local useRemote = bandage:FindFirstChild("Use")
                table.insert(instantConnections, bandage.Activated:Connect(function()
                    if not instantUseActive then return end
                    if useSound then useSound:Play() end
                    if useRemote and useRemote:IsA("RemoteEvent") then useRemote:FireServer() end
                    task.delay(0.5, function()
                        if useSound and useSound.IsPlaying then useSound:Stop() end
                    end)
                end))
            end

            local function hookTool(tool)
                if tool:IsA("Tool") then
                    if tool.Name == "Peel" then setupPeel(tool)
                    elseif tool.Name == "Flashbang" then setupFlashbang(tool)
                    elseif tool.Name == "Bandage" then setupBandage(tool)
                    end
                end
            end

            for _, t in ipairs(backpack:GetChildren()) do hookTool(t) end
            if lp.Character then
                for _, t in ipairs(lp.Character:GetChildren()) do hookTool(t) end
            end

            table.insert(instantConnections, backpack.ChildAdded:Connect(hookTool))
            if lp.Character then
                table.insert(instantConnections, lp.Character.ChildAdded:Connect(hookTool))
            end
            table.insert(instantConnections, lp.CharacterAdded:Connect(function(char)
                table.insert(instantConnections, char.ChildAdded:Connect(hookTool))
            end))

            Rayfield:Notify({Title="Instant Use", Content="ENABLED"})
        elseif not v and instantUseActive then
            instantUseActive = false
            for _, c in ipairs(instantConnections) do c:Disconnect() end
            instantConnections = {}

            -- reset attributes so it can be re-enabled cleanly
            for _, t in ipairs(backpack:GetChildren()) do
                if t:GetAttribute("PeelModded") then t:SetAttribute("PeelModded", nil) end
                if t:GetAttribute("FlashbangModded") then t:SetAttribute("FlashbangModded", nil) end
                if t:GetAttribute("BandageModded") then t:SetAttribute("BandageModded", nil) end
            end
            if lp.Character then
                for _, t in ipairs(lp.Character:GetChildren()) do
                    if t:GetAttribute("PeelModded") then t:SetAttribute("PeelModded", nil) end
                    if t:GetAttribute("FlashbangModded") then t:SetAttribute("FlashbangModded", nil) end
                    if t:GetAttribute("BandageModded") then t:SetAttribute("BandageModded", nil) end
                end
            end

            Rayfield:Notify({Title="Instant Use", Content="DISABLED"})
        end
    end
})

-- ✅ ESP
local espConnection = nil
MiscTab:CreateToggle({
    Name = "Enable ESP",
    CurrentValue = false,
    Callback = function(v)
        local espActive = v
        local shooterTools = {UZI=true, AK47=true, Shotgun=true, Pistol=true, M4=true}
        local RunService = game:GetService("RunService")

        local function setHighlight(character, color)
            local hl = character:FindFirstChild("Highlight")
            if not hl then
                hl = Instance.new("Highlight")
                hl.Name = "Highlight"
                hl.FillTransparency = 0.3
                hl.OutlineTransparency = 0
                hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                hl.Adornee = character
                hl.Parent = character
            end
            hl.FillColor = color
            hl.OutlineColor = color
        end

        local function setNameESP(player, character, color)
            if not character:FindFirstChild("Head") then return end
            local head = character:FindFirstChild("Head")
            local billboard = head:FindFirstChild("NameESP")
            if not billboard then
                billboard = Instance.new("BillboardGui")
                billboard.Name = "NameESP"
                billboard.Size = UDim2.new(0,75,0,15)
                billboard.Adornee = head
                billboard.AlwaysOnTop = true
                billboard.StudsOffset = Vector3.new(0,2,0)
                billboard.Parent = head
                local textLabel = Instance.new("TextLabel")
                textLabel.Size = UDim2.new(1,0,1,0)
                textLabel.BackgroundTransparency = 1
                textLabel.TextScaled = true
                textLabel.Font = Enum.Font.GothamBold
                textLabel.Name = "ESPText"
                textLabel.Parent = billboard
            end
            local text = billboard:FindFirstChild("ESPText")
            if text then
                text.Text = player.Name
                text.TextColor3 = color
                text.TextStrokeTransparency = 0.7
            end
        end

        local function checkRole(player, character)
            if player == Players.LocalPlayer then return end
            local isShooter = false
            for _, tool in pairs(player.Backpack:GetChildren()) do if tool:IsA("Tool") and shooterTools[tool.Name] then isShooter = true break end end
            for _, tool in pairs(character:GetChildren()) do if tool:IsA("Tool") and shooterTools[tool.Name] then isShooter = true break end end
            for _, tool in pairs(player.StarterGear:GetChildren()) do if tool:IsA("Tool") and shooterTools[tool.Name] then isShooter = true break end end

            if isShooter then
                setHighlight(character,Color3.fromRGB(255,0,0))
                setNameESP(player,character,Color3.fromRGB(255,0,0))
            else
                setHighlight(character,Color3.fromRGB(0,0,255))
                setNameESP(player,character,Color3.fromRGB(255,255,255))
            end
        end

        if espActive then
            espConnection = RunService.Heartbeat:Connect(function()
                for _, player in pairs(Players:GetPlayers()) do
                    if player.Character then checkRole(player, player.Character) end
                end
            end)
            Rayfield:Notify({Title="ESP", Content="ENABLED"})
        else
            if espConnection then espConnection:Disconnect() espConnection=nil end
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local hl = player.Character:FindFirstChild("Highlight")
                    if hl then hl:Destroy() end
                    if player.Character:FindFirstChild("Head") then
                        local espGui = player.Character.Head:FindFirstChild("NameESP")
                        if espGui then espGui:Destroy() end
                    end
                end
            end
            Rayfield:Notify({Title="ESP", Content="DISABLED"})
        end
    end
})

-- ✅ Push (fixed & re-toggle safe)
local pushActive = false
local pushConnections = {}

MiscTab:CreateToggle({
    Name = "Legit Strong Push",
    CurrentValue = false,
    Callback = function(v)
        pushActive = v

        local lp = Players.LocalPlayer
        local function findTool()
            local function scan(container)
                for _, obj in ipairs(container:GetChildren()) do
                    if obj:IsA("Tool") and (obj.Name == "Puh shh" or obj:FindFirstChild("PushRagdollEvent")) then
                        return obj
                    end
                end
            end
            local char = lp.Character or lp.CharacterAdded:Wait()
            return scan(char) or scan(lp.Backpack)
        end

        local function clearPushAttributes()
            local function clearIn(container)
                for _, obj in ipairs(container:GetChildren()) do
                    if obj:IsA("Tool") and obj:GetAttribute("PushHooked") then
                        obj:SetAttribute("PushHooked", nil)
                    end
                end
            end
            local char = lp.Character
            if char then clearIn(char) end
            clearIn(lp.Backpack)
        end

        local function setupTool(tool)
            if not tool or not pushActive then return end
            if tool:GetAttribute("PushHooked") then return end
            tool:SetAttribute("PushHooked", true)

            local pushEvent = tool:FindFirstChild("PushRagdollEvent") or tool:WaitForChild("PushRagdollEvent", 5)
            if not pushEvent then return end
            local pushAnim = tool:FindFirstChild("Push") or tool:FindFirstChildOfClass("Animation")

            local conn = tool.Activated:Connect(function()
                if not pushActive then return end
                local char = lp.Character or lp.CharacterAdded:Wait()
                local hum  = char:FindFirstChildOfClass("Humanoid")
                local root = char:FindFirstChild("HumanoidRootPart")
                if not (hum and root) then return end

                if pushAnim and pushAnim:IsA("Animation") then
                    local animator = hum:FindFirstChildOfClass("Animator") or Instance.new("Animator", hum)
                    local track = animator:LoadAnimation(pushAnim)
                    track:Play()
                end

                task.wait(0.05)

                local hitboxSize = Vector3.new(10,10,10)
                local targetCFrame = root.CFrame
                local dir = root.CFrame.LookVector * -150000
                local pushForce = Vector3.new(dir.X, dir.Y+60000, dir.Z)

                for i=1,2 do
                    pcall(function() pushEvent:FireServer(targetCFrame, hitboxSize, pushForce) end)
                    task.wait(0.08)
                end
            end)
            table.insert(pushConnections, conn)
        end

        if pushActive then
            -- turn on
            task.defer(function()
                local tool = findTool()
                setupTool(tool)
            end)
            table.insert(pushConnections, lp.CharacterAdded:Connect(function()
                task.wait(1)
                setupTool(findTool())
            end))
            table.insert(pushConnections, lp.Backpack.ChildAdded:Connect(function(tool)
                if tool:IsA("Tool") and (tool.Name=="Puh shh" or tool:FindFirstChild("PushRagdollEvent")) then
                    setupTool(tool)
                end
            end))
            Rayfield:Notify({Title="Push Mod", Content="ENABLED"})
        else
            -- turn off
            for _, c in ipairs(pushConnections) do c:Disconnect() end
            pushConnections = {}
            clearPushAttributes()
            Rayfield:Notify({Title="Push Mod", Content="DISABLED"})
        end
    end
})

---------------------------------------------------------------------
-- KNIFE TAB
---------------------------------------------------------------------
KnifeTab:CreateToggle({
    Name = "Knife Mod (20x Red Hitbox)",
    CurrentValue = false,
    Callback = function(v)
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        local function setupKnife(Knife)
            if not Knife then return end

            local Handle = Knife:WaitForChild("Handle", 5)
            local KnifeEvent = Knife:WaitForChild("KnifeEvent", 5)
            if not Handle or not KnifeEvent then
                warn("[Knife Mod] Knife missing parts")
                return
            end

            if Knife:GetAttribute("KnifeModded") then return end
            Knife:SetAttribute("KnifeModded", true)

            for _, obj in ipairs(Knife:GetDescendants()) do
                if obj:IsA("Animation") then obj:Destroy()
                elseif obj:IsA("LocalScript") then obj:Destroy() end
            end
            for _, sound in ipairs(Handle:GetDescendants()) do
                if sound:IsA("Sound") then sound:Destroy() end
            end

            Knife.Activated:Connect(function()
                if not v then return end -- ✅ Only runs if toggle is on
                local Character = LocalPlayer.Character
                if not Character then return end  

                local Head = Character:FindFirstChild("Head")
                if not Head then return end

                local hitCFrame = Head.CFrame * CFrame.new(0, -1, -3)

                local Hitbox = Instance.new("Part")
                Hitbox.Size = Vector3.new(20, 20, 20)
                Hitbox.Transparency = 0.7
                Hitbox.Color = Color3.new(1, 0, 0)
                Hitbox.Material = Enum.Material.ForceField
                Hitbox.Anchored = true
                Hitbox.CanCollide = false
                Hitbox.CFrame = hitCFrame
                Hitbox.Parent = workspace

                KnifeEvent:FireServer(hitCFrame, Vector3.new(20, 20, 20))

                task.delay(0.2, function()
                    if Hitbox then Hitbox:Destroy() end
                end)
            end)
        end

        local function clearKnives()
            local function clearIn(container)
                for _, tool in ipairs(container:GetChildren()) do
                    if tool:IsA("Tool") and tool.Name == "Knife" then
                        tool:SetAttribute("KnifeModded", nil)
                    end
                end
            end
            clearIn(LocalPlayer.Backpack)
            if LocalPlayer.Character then
                clearIn(LocalPlayer.Character)
            end
        end

        if v then
            -- enable
            local backpack = LocalPlayer:WaitForChild("Backpack")
            for _, tool in ipairs(backpack:GetChildren()) do
                if tool.Name == "Knife" then setupKnife(tool) end
            end
            if LocalPlayer.Character then
                for _, tool in ipairs(LocalPlayer.Character:GetChildren()) do
                    if tool.Name == "Knife" then setupKnife(tool) end
                end
            end
            backpack.ChildAdded:Connect(function(child)
                if child.Name == "Knife" then setupKnife(child) end
            end)
            LocalPlayer.CharacterAdded:Connect(function(char)
                char.ChildAdded:Connect(function(child)
                    if child.Name == "Knife" then setupKnife(child) end
                end)
            end)
            Rayfield:Notify({Title="Knife Mod", Content="ENABLED"})
        else
            -- disable
            clearKnives()
            Rayfield:Notify({Title="Knife Mod", Content="DISABLED"})
        end
    end
})

---------------------------------------------------------------------
-- TROLLING TAB (Puh shh Trap Mod - Full Stealth: No Animation + No Blue Part)
---------------------------------------------------------------------
local TrollingTab = Window:CreateTab("👾Trolling👾")

local trapToggleActive = false
local activeTraps = {}
local trapGui = nil
local toolData = {}
local lifecycleConns = {}
local partConn = nil

local Players = game:GetService("Players")
local lp = Players.LocalPlayer

-- helper: find the Puh shh tool
local function findTool()
    local function scan(container)
        for _, obj in ipairs(container:GetChildren()) do
            if obj:IsA("Tool") and (obj.Name == "Puh shh" or obj:FindFirstChild("PushRagdollEvent")) then
                return obj
            end
        end
    end
    local char = lp.Character or lp.CharacterAdded:Wait()
    return scan(char) or scan(lp:WaitForChild("Backpack"))
end

-- remove blue parts spawned by default push script
local function startBluePartKiller()
    if partConn then return end
    partConn = workspace.ChildAdded:Connect(function(obj)
        if not trapToggleActive then return end
        if obj:IsA("Part") then
            if obj.Size == Vector3.new(3,3,4) and obj.BrickColor == BrickColor.new("Bright blue") and obj.Material == Enum.Material.ForceField then
                obj:Destroy()
            end
        end
    end)
end

local function stopBluePartKiller()
    if partConn then
        partConn:Disconnect()
        partConn = nil
    end
end

-- trap spawner
local function spawnTrapLoop(pushEvent, trapCFrame, hitboxSize)
    local ghost = Instance.new("Part")
    ghost.Size = hitboxSize
    ghost.Anchored = true
    ghost.CanCollide = false
    ghost.CanTouch = false
    ghost.CanQuery = false
    ghost.Color = Color3.fromRGB(255, 0, 0)
    ghost.Transparency = 0.6
    ghost.Material = Enum.Material.Neon
    ghost.CFrame = trapCFrame
    ghost.Parent = workspace.CurrentCamera

    local trap = {ghost = ghost, alive = true}
    table.insert(activeTraps, trap)

    task.spawn(function()
        while trap.alive and ghost and ghost.Parent and trapToggleActive do
            local dir = trapCFrame.LookVector * -150000
            local force = Vector3.new(dir.X, dir.Y + 60000, dir.Z)
            pushEvent:FireServer(trapCFrame, hitboxSize, force)
            task.wait(0.1)
        end
    end)
end

-- suppress animation id & hook Activated
local function suppressAndHookTool(tool)
    if not tool or toolData[tool] then return end
    local pushAnim = tool:FindFirstChild("Push")
    local savedId = nil
    if pushAnim and pushAnim:IsA("Animation") then
        savedId = pushAnim.AnimationId
        pushAnim.AnimationId = "" -- suppress animation fully
    end

    local pushEvent = tool:FindFirstChild("PushRagdollEvent")
    local conn
    if pushEvent then
        conn = tool.Activated:Connect(function()
            if not trapToggleActive then return end
            local char = lp.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            if not root then return end
            local hitboxSize = Vector3.new(13,13,13)
            local trapCFrame = root.CFrame
            spawnTrapLoop(pushEvent, trapCFrame, hitboxSize)
        end)
    end

    toolData[tool] = {anim = pushAnim, originalId = savedId, conn = conn}
end

-- restore tool
local function restoreTool(tool)
    local data = toolData[tool]
    if not data then return end
    if data.anim and data.anim:IsA("Animation") then
        data.anim.AnimationId = data.originalId or ""
    end
    if data.conn then data.conn:Disconnect() end
    toolData[tool] = nil
end

-- GUI
local function setupGui()
    if trapGui then return end
    trapGui = Instance.new("ScreenGui")
    trapGui.Name = "TrapControlGui"
    trapGui.ResetOnSpawn = false
    trapGui.Parent = lp:WaitForChild("PlayerGui")

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 160, 0, 60)
    frame.Position = UDim2.new(0, 2, 0, 2)
    frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    frame.BackgroundTransparency = 0.2
    frame.BorderSizePixel = 0
    frame.Active = true
    frame.Parent = trapGui

    local clearBtn = Instance.new("TextButton")
    clearBtn.Size = UDim2.new(1, -10, 1, -10)
    clearBtn.Position = UDim2.new(0, 5, 0, 5)
    clearBtn.Text = "🗑 Clear Traps"
    clearBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
    clearBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    clearBtn.Font = Enum.Font.SourceSansBold
    clearBtn.TextSize = 16
    clearBtn.Parent = frame

    clearBtn.MouseButton1Click:Connect(function()
        for _, trap in ipairs(activeTraps) do
            if trap.ghost then trap.ghost:Destroy() end
            trap.alive = false
        end
        activeTraps = {}
    end)
end

-- cleanup
local function cleanupAll()
    for _, trap in ipairs(activeTraps) do
        if trap.ghost then trap.ghost:Destroy() end
        trap.alive = false
    end
    activeTraps = {}

    if trapGui then trapGui:Destroy() trapGui = nil end
    for tool,_ in pairs(toolData) do restoreTool(tool) end
    toolData = {}
    for _,c in ipairs(lifecycleConns) do c:Disconnect() end
    lifecycleConns = {}
    stopBluePartKiller()
end

-- toggle
TrollingTab:CreateToggle({
    Name = "Puh shh Trap Mod (Full Stealth)",
    CurrentValue = false,
    Callback = function(v)
        if v then
            trapToggleActive = true
            setupGui()
            local tool = findTool()
            if tool then suppressAndHookTool(tool) end

            table.insert(lifecycleConns, lp.CharacterAdded:Connect(function()
                task.wait(1)
                local t = findTool()
                if t then suppressAndHookTool(t) end
            end))
            table.insert(lifecycleConns, lp.Backpack.ChildAdded:Connect(function(child)
                if child:IsA("Tool") and (child.Name == "Puh shh" or child:FindFirstChild("PushRagdollEvent")) then
                    task.wait(0.2)
                    suppressAndHookTool(child)
                end
            end))

            startBluePartKiller()
            Rayfield:Notify({Title="Push Trap Mod", Content="ENABLED (full stealth)"})
        else
            trapToggleActive = false
            cleanupAll()
            Rayfield:Notify({Title="Push Trap Mod", Content="DISABLED (restored)"})
        end
    end
})

---------------------------------------------------------------------
-- PLAYER TAB (Jump Cooldown Remover)
---------------------------------------------------------------------
local PlayerTab = Window:CreateTab("▶️Player▶️")  -- 👤 Player utilities

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local runService = game:GetService("RunService")

-- Track toggle state
local AntiCooldown = false
local AntiLoop

-- Function to disable cooldown scripts
local function disableLocalJumpScripts()
	local pg = LocalPlayer:WaitForChild("PlayerGui")
	for _, v in ipairs(pg:GetDescendants()) do
		if v:IsA("LocalScript") then
			local name = v.Name:lower()
			if name:find("jump") or name:find("cooldown") then
				pcall(function()
					v.Disabled = true
				end)
			end
		end
	end
end

-- Toggle in Player Tab
PlayerTab:CreateToggle({
	Name = "Remove Jump Cooldown",
	CurrentValue = false,
	Flag = "RemoveJumpCooldown", 
	Callback = function(state)
		AntiCooldown = state
		if state then
			-- Start loop without console spam
			AntiLoop = runService.RenderStepped:Connect(disableLocalJumpScripts)
			Rayfield:Notify({Title="Jump Cooldown", Content="Auto-removal Enabled!"})
		else
			-- Stop loop
			if AntiLoop then
				AntiLoop:Disconnect()
				AntiLoop = nil
			end
			Rayfield:Notify({Title="Jump Cooldown", Content="Auto-removal Disabled."})
		end
	end,
})

---------------------------------------------------------------------
-- INVISIBLE TAB (Rayfield) - GUI only spawns when enabled
---------------------------------------------------------------------
local InvisibleTab = Window:CreateTab("👻Invisible👻") -- 👻 Tab

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local soundService = game:GetService("SoundService")

-- Vars
local invis_on = false
local defaultSpeed = 16
local boostedSpeed = 48
local isSpeedBoosted = false
local savedCFrame
local storedAnimate
local fakeBody
local screenGui -- GUI holder

-- Sound
local sound = Instance.new("Sound", soundService)
sound.SoundId = "rbxassetid://942127495"
sound.Volume = 1

-- Helpers
local function setTransparency(character, transparency)
	for _, part in pairs(character:GetDescendants()) do
		if part:IsA("BasePart") or part:IsA("Decal") then
			part.Transparency = transparency
		end
	end
end

-- Invisibility toggle
local function toggleInvisibility()
	invis_on = not invis_on
	sound:Play()

	if invis_on then
		game.StarterGui:SetCore("SendNotification", {
			Title = "Invis (on)",
			Duration = 3,
			Text = "STATUS:"
		})
	else
		game.StarterGui:SetCore("SendNotification", {
			Title = "Invis (off)",
			Duration = 3,
			Text = "STATUS:"
		})
	end

	local character = player.Character
	if not character then return end
	local hrp = character:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local animate = character:FindFirstChild("Animate")

	if invis_on then
		if animate and not storedAnimate then
			storedAnimate = animate:Clone()
			animate.Disabled = true
			animate.Parent = nil
		end

		savedCFrame = hrp.CFrame

		local Seat = Instance.new("Seat", workspace)
		Seat.Anchored = false
		Seat.CanCollide = false
		Seat.Name = "invischair"
		Seat.Transparency = 1
		Seat.CFrame = savedCFrame

		local torso = character:FindFirstChild("Torso") or character:FindFirstChild("UpperTorso")
		if torso then
			local Weld = Instance.new("Weld", Seat)
			Weld.Part0 = Seat
			Weld.Part1 = torso
		end

		if not fakeBody then
			fakeBody = character:Clone()
			fakeBody.Parent = workspace
			for _, p in pairs(fakeBody:GetDescendants()) do
				if p:IsA("BasePart") then
					p.Anchored = false
					p.CanCollide = true
				end
			end
		end

		setTransparency(character, 0.5)
	else
		local invisChair = workspace:FindFirstChild("invischair")
		if invisChair then invisChair:Destroy() end

		if storedAnimate then
			storedAnimate.Parent = character
			storedAnimate.Disabled = false
			storedAnimate = nil
		end

		if fakeBody then
			fakeBody:Destroy()
			fakeBody = nil
		end

		setTransparency(character, 0)
		if savedCFrame then hrp.CFrame = savedCFrame end
	end
end

-- Speed toggle
local function toggleSpeedBoost(button)
	isSpeedBoosted = not isSpeedBoosted
	sound:Play()
	local humanoid = player.Character and player.Character:FindFirstChild("Humanoid")

	if humanoid then
		if isSpeedBoosted then
			humanoid.WalkSpeed = boostedSpeed
			if button then button.BackgroundColor3 = Color3.fromRGB(0, 255, 0) end
			game.StarterGui:SetCore("SendNotification", {
				Title = "Speed Boost (on)",
				Duration = 3,
				Text = "Speed: " .. boostedSpeed
			})
		else
			humanoid.WalkSpeed = defaultSpeed
			if button then button.BackgroundColor3 = Color3.fromRGB(255, 0, 0) end
			game.StarterGui:SetCore("SendNotification", {
				Title = "Speed Boost (off)",
				Duration = 3,
				Text = "Speed: " .. defaultSpeed
			})
		end
	end
end

-- Rayfield Toggle
InvisibleTab:CreateToggle({
	Name = "Toggle Invisibility GUI",
	CurrentValue = false,
	Flag = "ToggleInvisibilityGUI",
	Callback = function(state)
		if state then
			if not screenGui then
				screenGui = Instance.new("ScreenGui")
				screenGui.ResetOnSpawn = false
				screenGui.Parent = player:WaitForChild("PlayerGui")

				local frame = Instance.new("Frame", screenGui)
				frame.Size = UDim2.new(0, 120, 0, 130)
				frame.Position = UDim2.new(0.5, -60, 0.5, -65)
				frame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
				frame.Active = true
				frame.Draggable = true

				local toggleButton = Instance.new("TextButton", frame)
				toggleButton.Size = UDim2.new(0, 100, 0, 30)
				toggleButton.Position = UDim2.new(0, 10, 0, 25)
				toggleButton.Text = "INVISIBLE"
				toggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
				toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
				toggleButton.Font = Enum.Font.SourceSans
				toggleButton.TextScaled = true
				toggleButton.MouseButton1Click:Connect(toggleInvisibility)

				local speedButton = Instance.new("TextButton", frame)
				speedButton.Size = UDim2.new(0, 100, 0, 30)
				speedButton.Position = UDim2.new(0, 10, 0, 65)
				speedButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
				speedButton.Text = "SPEED BOOST"
				speedButton.TextScaled = true
				speedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
				speedButton.Font = Enum.Font.SourceSans
				speedButton.MouseButton1Click:Connect(function()
					toggleSpeedBoost(speedButton)
				end)

				local closeButton = Instance.new("TextButton", frame)
				closeButton.Size = UDim2.new(0, 20, 0, 20)
				closeButton.Position = UDim2.new(1, -25, 0, 5)
				closeButton.Text = "X"
				closeButton.BackgroundColor3 = Color3.fromRGB(255, 123, 0)
				closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
				closeButton.Font = Enum.Font.SourceSans
				closeButton.TextSize = 18
				closeButton.MouseButton1Click:Connect(function()
					frame.Visible = false
				end)

				-- Reset speed on respawn
				player.CharacterAdded:Connect(function(character)
					isSpeedBoosted = false
					local humanoid = character:WaitForChild("Humanoid")
					humanoid.WalkSpeed = defaultSpeed
					speedButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
				end)

				game.StarterGui:SetCore("SendNotification", {
					Title = "GUI Spawned",
					Duration = 3,
					Text = "Invisible Controls Ready"
				})
			end
		else
			if screenGui then
				screenGui:Destroy()
				screenGui = nil
				if invis_on then toggleInvisibility() end
				isSpeedBoosted = false

				game.StarterGui:SetCore("SendNotification", {
					Title = "GUI Destroyed",
					Duration = 3,
					Text = "Invisible Controls Removed"
				})
			end
		end
	end,
})

---------------------------------------------------------------------
-- 🔔 Shooter Notification System (with Roblox Profile Link)
---------------------------------------------------------------------
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Shooter tools
local shooterTools = {
    UZI = true,
    AK47 = true,
    Shotgun = true,
    Pistol = true,
    M4 = true
}

-- Track notified shooters
local notifiedShooters = {}

-- Check if player is shooter
local function isShooter(player)
    if not player.Character then return false end
    for _, tool in pairs(player.Backpack:GetChildren()) do
        if tool:IsA("Tool") and shooterTools[tool.Name] then return true end
    end
    for _, tool in pairs(player.StarterGear:GetChildren()) do
        if tool:IsA("Tool") and shooterTools[tool.Name] then return true end
    end
    for _, tool in pairs(player.Character:GetChildren()) do
        if tool:IsA("Tool") and shooterTools[tool.Name] then return true end
    end
    return false
end

-- Notify with DisplayName + Profile link
local function notifyShooter(player)
    if notifiedShooters[player] then return end
    notifiedShooters[player] = true

    Rayfield:Notify({
        Title = "Shooter Detected",
        Content = player.DisplayName .. " (@" .. player.Name .. ")\nProfile: https://www.roblox.com/users/" .. player.UserId .. "/profile",
        Duration = 8
    })
end

---------------------------------------------------------------------
-- Rayfield Tab
---------------------------------------------------------------------
local NotifyTab = Window:CreateTab("🔔Notification🔔") -- 🔔 Shooter Alerts

local rsConnection = nil
NotifyTab:CreateToggle({
    Name = "Shooter Alerts",
    CurrentValue = false,
    Callback = function(v)
        if v then
            notifiedShooters = {}
            rsConnection = game:GetService("RunService").Heartbeat:Connect(function()
                for _, player in pairs(Players:GetPlayers()) do
                    if player ~= LocalPlayer and isShooter(player) then
                        notifyShooter(player)
                    end
                end
            end)
            Rayfield:Notify({
                Title = "Shooter Alerts",
                Content = "✅ Enabled",
                Duration = 3
            })
        else
            if rsConnection then rsConnection:Disconnect() rsConnection = nil end
            notifiedShooters = {}
            Rayfield:Notify({
                Title = "Shooter Alerts",
                Content = "❌ Disabled",
                Duration = 3
            })
        end
    end
})

--// Objective ESP Tab
local ObjectiveTab = Window:CreateTab("🚨Objectives🚨", 4483362458)

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local currentHighlight
local currentBillboard
local objectiveConnection

-- Helper: find BasePart for Highlight/Billboard
local function getAnyBasePart(obj)
    if obj:IsA("BasePart") then
        return obj
    elseif obj:IsA("Model") then
        return obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart", true)
    else
        return obj:FindFirstChildWhichIsA("BasePart", true)
    end
end

-- Cleanup ESP
local function clearObjectiveESP()
    if currentHighlight then
        currentHighlight:Destroy()
        currentHighlight = nil
    end
    if currentBillboard then
        currentBillboard:Destroy()
        currentBillboard = nil
    end
end

-- Highlight + Billboard objective
local function highlightObjective(data)
    clearObjectiveESP()

    local objectiveName = data.ObjectiveText

    for _, map in ipairs(Workspace:GetChildren()) do
        local objectivesFolder = map:FindFirstChild("Objectives")
        if objectivesFolder then
            for _, objPart in ipairs(objectivesFolder:GetChildren()) do
                local textValue = objPart:FindFirstChild("ObjectiveText")
                if textValue and textValue.Value == objectiveName then
                    local adornee = getAnyBasePart(objPart)
                    if adornee then
                        -- Cyan Highlight
                        local highlight = Instance.new("Highlight")
                        highlight.Adornee = adornee
                        highlight.FillColor = Color3.fromRGB(0, 255, 255)
                        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                        highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                        highlight.Parent = adornee
                        currentHighlight = highlight

                        -- BillboardGui
                        local billboard = Instance.new("BillboardGui")
                        billboard.Name = "ObjectiveBillboard"
                        billboard.Size = UDim2.new(0, 200, 0, 30)
                        billboard.StudsOffset = Vector3.new(0, 4, 0)
                        billboard.Adornee = adornee
                        billboard.AlwaysOnTop = true
                        billboard.Parent = adornee

                        local title = Instance.new("TextLabel")
                        title.Size = UDim2.new(1, 0, 1, 0)
                        title.BackgroundTransparency = 1
                        title.TextColor3 = Color3.fromRGB(0, 255, 255)
                        title.TextStrokeTransparency = 0.3
                        title.Font = Enum.Font.GothamBold
                        title.TextScaled = true
                        title.Text = "[OBJECTIVE] " .. objectiveName
                        title.Parent = billboard

                        currentBillboard = billboard

                        warn("✅ ESP applied to:", objectiveName)
                        return
                    end
                end
            end
        end
    end
end

-- Toggle for Objective ESP
ObjectiveTab:CreateToggle({
    Name = "Objective ESP",
    CurrentValue = false,
    Callback = function(Value)
        if Value then
            warn("🔎 Listening for objective updates...")
            objectiveConnection = ReplicatedStorage.Remotes.ObjectiveUpdateEvent.OnClientEvent:Connect(function(data)
                -- Debug: print everything in the data
                print("📩 Objective Event Fired")
                for k, v in pairs(data) do
                    print("   ", k, "=", v)
                end

                -- Objective completed
                if data.Completed == true then
                    if currentBillboard then
                        local title = currentBillboard:FindFirstChildOfClass("TextLabel")
                        if title then
                            -- ✅ Success if rewards exist
                            if (data.TotalXP and data.TotalXP > 0) or (data.TotalCash and data.TotalCash > 0) then
                                title.Text = "✅ COMPLETED!"
                                title.TextColor3 = Color3.fromRGB(0, 255, 0)
                            else
                                title.Text = "❌ FAILED!"
                                title.TextColor3 = Color3.fromRGB(255, 0, 0)
                            end
                        end
                    end
                    task.delay(2, clearObjectiveESP)
                    return
                end

                -- New objective given
                if data.ObjectiveText then
                    highlightObjective(data)
                else
                    warn("⚠️ Event fired but no ObjectiveText key found")
                end
            end)
        else
            if objectiveConnection then
                objectiveConnection:Disconnect()
                objectiveConnection = nil
            end
            clearObjectiveESP()
            warn("❌ Objective ESP disabled")
        end
    end
})
