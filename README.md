--[[
    TETEU EXPLOITS
    Base completa para Real Executor / Synapse / Fluxus
    Tema: Preto & Dourado
    Nota: Desenhe o box com Drawing API (mais estável).
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local Config = {
    Aimbot = { Enabled = false, FOV = 120, Smooth = 0.25, TeamCheck = false },
    ESP = { Enabled = true, Color = Color3.fromRGB(255, 215, 0), Box = true, Name = true, Health = true, Line = false },
    Movement = { Speed = 16, JumpPower = 50, InfJump = false, Noclip = false },
}

local function getChar()
    return LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
end

local function getHum()
    local c = getChar()
    return c and c:FindFirstChildOfClass("Humanoid")
end

-----------------------------------------------------------
-- UI PREO/DOURADO
-----------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TETEU_EXPLOITS"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = game:GetService("CoreGui")

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 480, 0, 360)
Main.Position = UDim2.new(0.5, -240, 0.5, -180)
Main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.Parent = ScreenGui

Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 12)
local Stroke = Instance.new("UIStroke", Main)
Stroke.Color = Color3.fromRGB(255, 215, 0)
Stroke.Thickness = 1.5
Stroke.Transparency = 0.2

local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 42)
TitleBar.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
TitleBar.BorderSizePixel = 0
TitleBar.Parent = Main
Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 12)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -80, 1, 0)
Title.Position = UDim2.new(0, 16, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "TETEU EXPLOITS"
Title.TextColor3 = Color3.fromRGB(255, 215, 0)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 32, 0, 32)
Close.Position = UDim2.new(1, -42, 0, 5)
Close.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Close.Text = "X"
Close.TextColor3 = Color3.fromRGB(255, 215, 0)
Close.Font = Enum.Font.GothamBold
Close.TextSize = 16
Close.Parent = TitleBar
Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 6)
Close.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-----------------------------------------------------------
-- ESP (Drawing API)
-----------------------------------------------------------
local esp = {}
local draw = Drawing.new

local function createESP(plr)
    local box = draw("Square")
    box.Thickness = 1
    box.Color = Color3.fromRGB(255, 215, 0)
    box.Filled = false

    local name = draw("Text")
    name.Size = 14
    name.Center = true
    name.Outline = true
    name.Color = Color3.fromRGB(255, 215, 0)

    local hp = draw("Text")
    hp.Size = 12
    hp.Center = true
    hp.Outline = true
    hp.Color = Color3.fromRGB(0, 255, 0)

    local line = draw("Line")
    line.Thickness = 1
    line.Color = Color3.fromRGB(255, 215, 0)

    esp[plr] = { Box = box, Name = name, Health = hp, Line = line }
end

local function removeESP(plr)
    if esp[plr] then
        for _, v in pairs(esp[plr]) do v:Remove() end
        esp[plr] = nil
    end
end

for _, p in pairs(Players:GetPlayers()) do
    if p ~= LocalPlayer then createESP(p) end
end
Players.PlayerAdded:Connect(function(p) if p ~= LocalPlayer then createESP(p) end end)
Players.PlayerRemoving:Connect(removeESP)

RunService.RenderStepped:Connect(function()
    for plr, table in pairs(esp) do
        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            local root = plr.Character.HumanoidRootPart
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 and Config.ESP.Enabled then
                local pos, onScreen = Camera:WorldToViewportPoint(root.Position)
                local head = Camera:WorldToViewportPoint(root.Position + Vector3.new(0, 2.5, 0))
                local leg = Camera:WorldToViewportPoint(root.Position - Vector3.new(0, 2.5, 0))
                if onScreen then
                    local height = math.abs(head.Y - leg.Y)
                    local width = height / 1.8
                    local x = pos.X - width / 2
                    local y = pos.Y - height / 2

                    table.Box.Size = Vector2.new(width, height)
                    table.Box.Position = Vector2.new(x, y)
                    table.Box.Visible = Config.ESP.Box

                    table.Name.Text = plr.Name
                    table.Name.Position = Vector2.new(pos.X, y - 16)
                    table.Name.Visible = Config.ESP.Name

                    table.Health.Text = tostring(math.floor(hum.Health)) .. " HP"
                    table.Health.Position = Vector2.new(pos.X, y + height + 2)
                    table.Health.Visible = Config.ESP.Health

                    table.Line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                    table.Line.To = Vector2.new(pos.X, y + height / 2)
                    table.Line.Visible = Config.ESP.Line
                else
                    table.Box.Visible = false
                    table.Name.Visible = false
                    table.Health.Visible = false
                    table.Line.Visible = false
                end
            else
                table.Box.Visible = false
                table.Name.Visible = false
                table.Health.Visible = false
                table.Line.Visible = false
            end
        end
    end
end)

-----------------------------------------------------------
-- AIMBOT
-----------------------------------------------------------
local function getClosest()
    local mouse = UserInputService:GetMouseLocation()
    local closest, dist = nil, math.huge

    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            local head = plr.Character:FindFirstChild("Head")
            if hum and hum.Health > 0 and head then
                local screenPos, onScreen = Camera:WorldToViewportPoint(head.Position)
                if onScreen then
                    local d = (Vector2.new(screenPos.X, screenPos.Y) - mouse).Magnitude
                    if d < Config.Aimbot.FOV and d < dist then
                        closest, dist = head, d
                    end
                end
            end
        end
    end
    return closest
end

RunService.RenderStepped:Connect(function()
    if not Config.Aimbot.Enabled then return end
    local target = getClosest()
    if target then
        local mouse = UserInputService:GetMouseLocation()
        local screenPos = Camera:WorldToViewportPoint(target.Position)
        local delta = Vector2.new(screenPos.X, screenPos.Y) - mouse
        mousemoverel(delta.X * Config.Aimbot.Smooth, delta.Y * Config.Aimbot.Smooth)
    end
end)

-----------------------------------------------------------
-- MOVIMENTAÇÃO
-----------------------------------------------------------
RunService.Stepped:Connect(function()
    local char = LocalPlayer.Character
    if char and Config.Movement.Noclip then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    local hum = getHum()
    if hum and Config.Movement.InfJump then
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

local function updateMovement()
    local hum = getHum()
    if hum then
        hum.WalkSpeed = Config.Movement.Speed
        hum.JumpPower = Config.Movement.JumpPower
        hum.UseJumpPower = true
    end
end
LocalPlayer.CharacterAdded:Connect(function() task.wait(0.5); updateMovement() end)

-----------------------------------------------------------
-- UI CONTROLS
-----------------------------------------------------------
local function makeButton(text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 30)
    btn.Position = UDim2.new(0, 30, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 215, 0)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.Parent = Main
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
    local stroke = Instance.new("UIStroke", btn)
    stroke.Color = Color3.fromRGB(255, 215, 0)
    stroke.Thickness = 1
    stroke.Transparency = 0.6
    btn.MouseButton1Click:Connect(callback)
    return btn
end

-- Toggles
makeButton("Aimbot: OFF", 60, function(self)
    Config.Aimbot.Enabled = not Config.Aimbot.Enabled
    self.Text = "Aimbot: " .. (Config.Aimbot.Enabled and "ON" or "OFF")
end)

makeButton("ESP: ON", 100, function(self)
    Config.ESP.Enabled = not Config.ESP.Enabled
    self.Text = "ESP: " .. (Config.ESP.Enabled and "ON" or "OFF")
end)

makeButton("Noclip: OFF", 140, function(self)
    Config.Movement.Noclip = not Config.Movement.Noclip
    self.Text = "Noclip: " .. (Config.Movement.Noclip and "ON" or "OFF")
end)

makeButton("Pulo Infinito: OFF", 180, function(self)
    Config.Movement.InfJump = not Config.Movement.InfJump
    self.Text = "Pulo Infinito: " .. (Config.Movement.InfJump and "ON" or "OFF")
end)

-- Trocar cor
makeButton("Trocar Cor ESP", 220, function()
    local r = math.random(0, 255)
    local g = math.random(0, 255)
    local b = math.random(0, 255)
    Config.ESP.Color = Color3.fromRGB(r, g, b)
    for _, t in pairs(esp) do
        t.Box.Color = Config.ESP.Color
        t.Name.Color = Config.ESP.Color
        t.Line.Color = Config.ESP.Color
    end
end)

-----------------------------------------------------------
-- SLIDERS
-----------------------------------------------------------
local function makeSlider(name, y, min, max, default, callback)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 200, 0, 20)
    label.Position = UDim2.new(0, 260, 0, y)
    label.BackgroundTransparency = 1
    label.Text = name .. ": " .. default
    label.TextColor3 = Color3.fromRGB(255, 215, 0)
    label.Font = Enum.Font.Gotham
    label.TextSize = 14
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = Main

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(0, 180, 0, 6)
    bar.Position = UDim2.new(0, 260, 0, y + 22)
    bar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    bar.Parent = Main
    Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
    fill.Parent = bar
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)

    local dragging = false
    bar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = true end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then dragging = false end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local pos = math.clamp((input.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            fill.Size = UDim2.new(pos, 0, 1, 0)
            local val = math.floor(min + (max - min) * pos)
            label.Text = name .. ": " .. val
            callback(val)
        end
    end)
end

makeSlider("FOV Aimbot", 260, 50, 400, 120, function(v) Config.Aimbot.FOV = v end)
makeSlider("Smooth", 300, 0.05, 1, 0.25, function(v) Config.Aimbot.Smooth = v end)

-----------------------------------------------------------
-- INICIALIZAÇÃO
-----------------------------------------------------------
updateMovement()
print("[TETEU EXPLOITS] Carregado.")
