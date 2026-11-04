--[[
    TETEU EXPLOITS v6 - FLAT MOBILE SAFE
    Testado: PlayerGui funciona. UI direta, sem pcall engolindo erro.
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local isMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled

local Theme = {
    BG = Color3.fromRGB(15, 15, 20),
    SideBG = Color3.fromRGB(10, 10, 15),
    Accent = Color3.fromRGB(155, 89, 182),
    AccentDark = Color3.fromRGB(80, 40, 100),
    Text = Color3.fromRGB(220, 220, 220)
}

local Config = {
    Aimbot = { Enabled = false, FOV = 150, Smooth = 0.25, Part = "Head" },
    ESP = { Enabled = true, Color = Theme.Accent, Name = true, Health = true },
    Movement = { Speed = 16, JumpPower = 50, InfJump = false, Noclip = false }
}

local espTable = {}

-----------------------------------------------------------
-- UI
-----------------------------------------------------------
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TETEU_EXPLOITS_V6"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
if isMobile then
    Main.Size = UDim2.new(0, 330, 0, 260)
    Main.Position = UDim2.new(0.5, -165, 0.5, -130)
else
    Main.Size = UDim2.new(0, 560, 0, 380)
    Main.Position = UDim2.new(0.5, -280, 0.5, -190)
end
Main.BackgroundColor3 = Theme.BG
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.Parent = ScreenGui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 10)
local stroke = Instance.new("UIStroke", Main)
stroke.Color = Theme.Accent
stroke.Thickness = 1.5

-- TitleBar
local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 38)
TitleBar.BackgroundColor3 = Theme.SideBG
TitleBar.BorderSizePixel = 0
TitleBar.Parent = Main
Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 10)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -80, 1, 0)
Title.Position = UDim2.new(0, 12, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "TETEU EXPLOITS"
Title.TextColor3 = Theme.Accent
Title.Font = Enum.Font.GothamBold
Title.TextSize = isMobile and 13 or 17
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TitleBar

local Gengar = Instance.new("TextLabel")
Gengar.Size = UDim2.new(0, 35, 0, 38)
Gengar.Position = UDim2.new(1, -80, 0, 0)
Gengar.BackgroundTransparency = 1
Gengar.Text = "👻"
Gengar.TextColor3 = Theme.Accent
Gengar.TextSize = isMobile and 22 or 28
Gengar.Parent = TitleBar

local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 28, 0, 28)
Close.Position = UDim2.new(1, -32, 0, 5)
Close.BackgroundColor3 = Theme.AccentDark
Close.Text = "X"
Close.TextColor3 = Theme.Text
Close.Font = Enum.Font.GothamBold
Close.TextSize = 14
Close.Parent = TitleBar
Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 6)
Close.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

-- Sidebar
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, isMobile and 85 or 130, 1, -38)
Sidebar.Position = UDim2.new(0, 0, 0, 38)
Sidebar.BackgroundColor3 = Theme.SideBG
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main
Instance.new("UICorner", Sidebar).CornerRadius = UDim.new(0, 10)

-- Content
local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, isMobile and -85 or -130, 1, -38)
Content.Position = UDim2.new(0, isMobile and 85 or 130, 0, 38)
Content.BackgroundTransparency = 1
Content.Parent = Main

-- Páginas
local function newPage()
    local p = Instance.new("Frame")
    p.Size = UDim2.new(1, 0, 1, 0)
    p.BackgroundTransparency = 1
    p.Visible = false
    p.Parent = Content
    return p
end
local AimbotPage = newPage()
local VisualsPage = newPage()
local MovementPage = newPage()

local function TabButton(text, page, y)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -10, 0, 28)
    b.Position = UDim2.new(0, 5, 0, y)
    b.BackgroundColor3 = Theme.BG
    b.Text = " " .. text
    b.TextColor3 = Theme.Text
    b.Font = Enum.Font.GothamSemibold
    b.TextSize = isMobile and 10 or 13
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.Parent = Sidebar
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
    b.MouseButton1Click:Connect(function()
        for _, p in pairs({AimbotPage, VisualsPage, MovementPage}) do p.Visible = false end
        page.Visible = true
        for _, c in pairs(Sidebar:GetChildren()) do
            if c:IsA("TextButton") then
                c.TextColor3 = Theme.Text
                c.BackgroundColor3 = Theme.BG
            end
        end
        b.TextColor3 = Theme.Accent
        b.BackgroundColor3 = Theme.AccentDark
    end)
end
TabButton("Aimbot", AimbotPage, 8)
TabButton("Visuals", VisualsPage, 40)
TabButton("Movement", MovementPage, 72)
AimbotPage.Visible = true

-- Helpers
local pageY = 8
local W = isMobile and 200 or 380
local H = isMobile and 26 or 32
local GAP = isMobile and 32 or 42

local function Toggle(page, text, def, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, W, 0, H)
    b.Position = UDim2.new(0, 8, 0, pageY)
    b.BackgroundColor3 = Theme.SideBG
    b.Text = "  " .. text .. ": " .. (def and "ON" or "OFF")
    b.TextColor3 = def and Theme.Accent or Theme.Text
    b.Font = Enum.Font.Gotham
    b.TextSize = isMobile and 10 or 13
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.Parent = page
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)
    local st = def
    b.MouseButton1Click:Connect(function()
        st = not st
        b.Text = "  " .. text .. ": " .. (st and "ON" or "OFF")
        b.TextColor3 = st and Theme.Accent or Theme.Text
        cb(st)
    end)
    pageY = pageY + GAP
end

local function Slider(page, text, mn, mx, def, cb)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0, W, 0, 16)
    lbl.Position = UDim2.new(0, 8, 0, pageY)
    lbl.BackgroundTransparency = 1
    lbl.Text = "  " .. text .. ": " .. def
    lbl.TextColor3 = Theme.Text
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = isMobile and 10 or 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = page

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(0, W - 10, 0, 6)
    bar.Position = UDim2.new(0, 15, 0, pageY + 18)
    bar.BackgroundColor3 = Theme.SideBG
    bar.Parent = page
    Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((def - mn) / (mx - mn), 0, 1, 0)
    fill.BackgroundColor3 = Theme.Accent
    fill.Parent = bar
    Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)

    local drag = false
    bar.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then drag = true end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then drag = false end
    end)
    UserInputService.InputChanged:Connect(function(i)
        if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            local pos = math.clamp((i.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
            fill.Size = UDim2.new(pos, 0, 1, 0)
            local v = math.floor(mn + (mx - mn) * pos)
            lbl.Text = "  " .. text .. ": " .. v
            cb(v)
        end
    end)
    pageY = pageY + GAP + 18
end

local function Dropdown(page, text, opts, def, cb)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0, W, 0, 16)
    lbl.Position = UDim2.new(0, 8, 0, pageY)
    lbl.BackgroundTransparency = 1
    lbl.Text = "  " .. text
    lbl.TextColor3 = Theme.Text
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = isMobile and 10 or 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = page

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, W - 10, 0, H)
    b.Position = UDim2.new(0, 15, 0, pageY + 18)
    b.BackgroundColor3 = Theme.SideBG
    b.Text = def
    b.TextColor3 = Theme.Accent
    b.Font = Enum.Font.Gotham
    b.TextSize = isMobile and 10 or 13
    b.Parent = page
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 6)

    local list = Instance.new("Frame")
    list.Size = UDim2.new(0, W - 10, 0, #opts * 26)
    list.Position = UDim2.new(0, 15, 0, pageY + 18 + H + 2)
    list.BackgroundColor3 = Theme.BG
    list.Visible = false
    list.ZIndex = 5
    list.Parent = page
    Instance.new("UICorner", list).CornerRadius = UDim.new(0, 6)
    Instance.new("UIListLayout", list).Padding = UDim.new(0, 2)

    b.MouseButton1Click:Connect(function() list.Visible = not list.Visible end)

    for _, opt in ipairs(opts) do
        local o = Instance.new("TextButton")
        o.Size = UDim2.new(1, 0, 0, 24)
        o.BackgroundColor3 = Theme.BG
        o.Text = opt
        o.TextColor3 = Theme.Text
        o.Font = Enum.Font.Gotham
        o.TextSize = isMobile and 10 or 13
        o.ZIndex = 6
        o.Parent = list
        o.MouseButton1Click:Connect(function()
            b.Text = opt
            list.Visible = false
            cb(opt)
        end)
    end
    pageY = pageY + GAP + H + 5
end

-- ABA AIMBOT
pageY = 8
Toggle(AimbotPage, "Ativar Aimbot", Config.Aimbot.Enabled, function(v) Config.Aimbot.Enabled = v end)
Slider(AimbotPage, "FOV", 50, 500, Config.Aimbot.FOV, function(v) Config.Aimbot.FOV = v end)
Slider(AimbotPage, "Smooth", 0.05, 1, Config.Aimbot.Smooth, function(v) Config.Aimbot.Smooth = v end)
Dropdown(AimbotPage, "Região", {"Head", "Torso", "HumanoidRootPart"}, Config.Aimbot.Part, function(v) Config.Aimbot.Part = v end)

-- ABA VISUALS
pageY = 8
Toggle(VisualsPage, "Ativar ESP", Config.ESP.Enabled, function(v) Config.ESP.Enabled = v end)
Toggle(VisualsPage, "Nome", Config.ESP.Name, function(v) Config.ESP.Name = v end)
Toggle(VisualsPage, "Vida", Config.ESP.Health, function(v) Config.ESP.Health = v end)

local colorLbl = Instance.new("TextLabel")
colorLbl.Size = UDim2.new(0, W, 0, 16)
colorLbl.Position = UDim2.new(0, 8, 0, pageY)
colorLbl.BackgroundTransparency = 1
colorLbl.Text = "  Cor do ESP"
colorLbl.TextColor3 = Theme.Text
colorLbl.Font = Enum.Font.Gotham
colorLbl.TextSize = isMobile and 10 or 13
colorLbl.TextXAlignment = Enum.TextXAlignment.Left
colorLbl.Parent = VisualsPage

local grid = Instance.new("Frame")
grid.Size = UDim2.new(0, W - 10, 0, isMobile and 50 or 60)
grid.Position = UDim2.new(0, 15, 0, pageY + 18)
grid.BackgroundColor3 = Theme.BG
grid.Parent = VisualsPage
Instance.new("UICorner", grid).CornerRadius = UDim.new(0, 6)
local gl = Instance.new("UIGridLayout", grid)
gl.CellSize = UDim2.new(0, isMobile and 22 or 28, 0, isMobile and 22 or 28)
gl.CellPadding = UDim2.new(0, 4, 0, 4)

local cores = {
    Color3.fromRGB(255, 0, 0), Color3.fromRGB(255, 100, 0), Color3.fromRGB(255, 255, 0),
    Color3.fromRGB(0, 255, 0), Color3.fromRGB(0, 255, 255), Color3.fromRGB(0, 100, 255),
    Color3.fromRGB(155, 89, 182), Color3.fromRGB(255, 0, 255), Color3.fromRGB(255, 255, 255),
    Color3.fromRGB(0, 0, 0), Color3.fromRGB(150, 150, 150), Color3.fromRGB(255, 215, 0)
}
for _, c in ipairs(cores) do
    local cb = Instance.new("TextButton")
    cb.BackgroundColor3 = c
    cb.Text = ""
    cb.Parent = grid
    Instance.new("UICorner", cb).CornerRadius = UDim.new(0, 4)
    cb.MouseButton1Click:Connect(function()
        Config.ESP.Color = c
        for _, t in pairs(espTable) do
            if t.Highlight then t.Highlight.FillColor = c; t.Highlight.OutlineColor = c end
            if t.Name then t.Name.TextColor3 = c end
        end
    end)
end

-- ABA MOVEMENT
pageY = 8
Toggle(MovementPage, "Noclip", Config.Movement.Noclip, function(v) Config.Movement.Noclip = v end)
Toggle(MovementPage, "Pulo Infinito", Config.Movement.InfJump, function(v) Config.Movement.InfJump = v end)
Slider(MovementPage, "Velocidade", 16, 200, Config.Movement.Speed, function(v) Config.Movement.Speed = v end)
Slider(MovementPage, "Força do Pulo", 50, 300, Config.Movement.JumpPower, function(v) Config.Movement.JumpPower = v end)

-----------------------------------------------------------
-- ESP (Highlight + Billboard)
-----------------------------------------------------------
local function createESP(plr)
    local hl = Instance.new("Highlight")
    hl.Name = "TETEU_HL"
    hl.FillColor = Config.ESP.Color
    hl.FillTransparency = 0.7
    hl.OutlineColor = Config.ESP.Color
    hl.OutlineTransparency = 0
    hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop

    local bb = Instance.new("BillboardGui")
    bb.Name = "TETEU_BB"
    bb.Size = UDim2.new(0, 120, 0, 34)
    bb.StudsOffset = Vector3.new(0, 3, 0)
    bb.AlwaysOnTop = true

    local nameL = Instance.new("TextLabel")
    nameL.Size = UDim2.new(1, 0, 0, 18)
    nameL.BackgroundTransparency = 1
    nameL.Text = plr.Name
    nameL.TextColor3 = Config.ESP.Color
    nameL.TextStrokeTransparency = 0
    nameL.Font = Enum.Font.GothamBold
    nameL.TextSize = 12
    nameL.Parent = bb

    local hpL = Instance.new("TextLabel")
    hpL.Size = UDim2.new(1, 0, 0, 14)
    hpL.Position = UDim2.new(0, 0, 0, 18)
    hpL.BackgroundTransparency = 1
    hpL.Text = "100 HP"
    hpL.TextColor3 = Color3.fromRGB(0, 255, 0)
    hpL.TextStrokeTransparency = 0
    hpL.Font = Enum.Font.Gotham
    hpL.TextSize = 10
    hpL.Parent = bb

    espTable[plr] = { Highlight = hl, Billboard = bb, Name = nameL, Health = hpL }
end

local function removeESP(plr)
    if espTable[plr] then
        if espTable[plr].Highlight then espTable[plr].Highlight:Destroy() end
        if espTable[plr].Billboard then espTable[plr].Billboard:Destroy() end
        espTable[plr] = nil
    end
end

for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer then createESP(p) end end
Players.PlayerAdded:Connect(function(p) if p ~= LocalPlayer then createESP(p) end end)
Players.PlayerRemoving:Connect(removeESP)

-----------------------------------------------------------
-- LOOPS
-----------------------------------------------------------
RunService.RenderStepped:Connect(function()
    for plr, t in pairs(espTable) do
        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            local hum = plr.Character:FindFirstChildOfClass("Humanoid")
            if Config.ESP.Enabled and hum and hum.Health > 0 then
                if t.Highlight.Parent ~= plr.Character then t.Highlight.Parent = plr.Character end
                if plr.Character:FindFirstChild("Head") and t.Billboard.Parent ~= plr.Character.Head then
                    t.Billboard.Parent = plr.Character.Head
                end
                t.Highlight.FillColor = Config.ESP.Color
                t.Highlight.OutlineColor = Config.ESP.Color
                t.Name.Visible = Config.ESP.Name
                t.Name.Text = plr.Name
                t.Name.TextColor3 = Config.ESP.Color
                t.Health.Visible = Config.ESP.Health
                t.Health.Text = math.floor(hum.Health) .. " HP"
            else
                t.Highlight.Parent = nil
                t.Billboard.Parent = nil
            end
        end
    end

    if Config.Aimbot.Enabled then
        local center = isMobile and Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2) or UserInputService:GetMouseLocation()
        local target, best = nil, math.huge
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                local part = plr.Character:FindFirstChild(Config.Aimbot.Part) or plr.Character:FindFirstChild("Head")
                if hum and hum.Health > 0 and part then
                    local sp, on = Camera:WorldToViewportPoint(part.Position)
                    if on then
                        local d = (Vector2.new(sp.X, sp.Y) - center).Magnitude
                        if d < Config.Aimbot.FOV and d < best then
                            best = d
                            target = part
                        end
                    end
                end
            end
        end
        if target then
            if isMobile then
                Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, target.Position), Config.Aimbot.Smooth)
            else
                local sp = Camera:WorldToViewportPoint(target.Position)
                local delta = Vector2.new(sp.X, sp.Y) - center
                pcall(function() mousemoverel(delta.X * Config.Aimbot.Smooth, delta.Y * Config.Aimbot.Smooth) end)
            end
        end
    end
end)

-----------------------------------------------------------
-- MOVIMENTAÇÃO
-----------------------------------------------------------
RunService.Stepped:Connect(function()
    local char = LocalPlayer.Character
    if char and Config.Movement.Noclip then
        for _, part in pairs(char:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum and Config.Movement.InfJump then
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

local function updateMove()
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.WalkSpeed = Config.Movement.Speed
        hum.JumpPower = Config.Movement.JumpPower
        hum.UseJumpPower = true
    end
end
LocalPlayer.CharacterAdded:Connect(function() task.wait(0.5); updateMove() end)
updateMove()

print("[TETEU EXPLOITS] v6 carregado. " .. (isMobile and "Mobile" or "PC"))
