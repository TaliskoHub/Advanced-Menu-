--[[ 
  Script Roblox - Menu Preto (abrir/fechar por bolinha preta)
  Inclui ESP Vermelho, Silent Aimbot (FOV ajustável) e GodMode.
  Para estudos/teste de sistemas UI e lógica em Roblox.
--]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui

-- Bolinha preta para abrir/fechar o menu
local CircleBtn = Instance.new("TextButton")
CircleBtn.Size = UDim2.new(0, 36, 0, 36)
CircleBtn.Position = UDim2.new(0, 10, 0, 80)
CircleBtn.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
CircleBtn.BorderSizePixel = 0
CircleBtn.Text = ""
CircleBtn.Parent = ScreenGui
CircleBtn.Name = "OpenMenuCircle"
CircleBtn.AutoButtonColor = true
CircleBtn.ZIndex = 3
CircleBtn.ClipsDescendants = true
local uicorner = Instance.new("UICorner")
uicorner.CornerRadius = UDim.new(1, 0)
uicorner.Parent = CircleBtn

-- Menu principal (começa invisível)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 250, 0, 340)
Frame.Position = UDim2.new(0, 50, 0, 70)
Frame.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
Frame.BorderSizePixel = 0
Frame.Name = "BlackMenuFrame"
Frame.Parent = ScreenGui
Frame.Visible = false
Frame.ZIndex = 2

-- Botão de fechar dentro do menu
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 30)
CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(175, 41, 41)
CloseBtn.Text = "X"
CloseBtn.Font = Enum.Font.SourceSansBold
CloseBtn.TextColor3 = Color3.fromRGB(255,255,255)
CloseBtn.TextSize = 20
CloseBtn.Parent = Frame
CloseBtn.MouseButton1Click:Connect(function() Frame.Visible = false end)

-- Clicar na bolinha abre/fecha menu
CircleBtn.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- LABEL "REGULAMENTO" + SLIDER FOV
local Regulamento = Instance.new("TextLabel")
Regulamento.Size = UDim2.new(0, 220, 0, 24)
Regulamento.Position = UDim2.new(0, 15, 0, 8)
Regulamento.BackgroundTransparency = 1
Regulamento.TextColor3 = Color3.fromRGB(200,200,200)
Regulamento.Text = "Silent Fov Regulamento"
Regulamento.Font = Enum.Font.SourceSansBold
Regulamento.TextXAlignment = Enum.TextXAlignment.Left
Regulamento.TextSize = 16
Regulamento.Parent = Frame

-- Fov Slider
local SliderFrame = Instance.new("Frame")
SliderFrame.Size = UDim2.new(0, 185, 0, 8)
SliderFrame.Position = UDim2.new(0, 22, 0, 35)
SliderFrame.BackgroundColor3 = Color3.fromRGB(44,44,44)
SliderFrame.BorderSizePixel = 0
SliderFrame.Parent = Frame

local SliderBtn = Instance.new("TextButton")
SliderBtn.Size = UDim2.new(0, 14, 0, 18)
SliderBtn.Position = UDim2.new(0, 0, 0, -5)
SliderBtn.BackgroundColor3 = Color3.fromRGB(90,90,90)
SliderBtn.Text = ""
SliderBtn.Parent = SliderFrame

local FovValueLabel = Instance.new("TextLabel")
FovValueLabel.Size = UDim2.new(0, 30, 0, 18)
FovValueLabel.Position = UDim2.new(1, 7, 0, -6)
FovValueLabel.Text = "0"
FovValueLabel.TextColor3 = Color3.fromRGB(230,230,230)
FovValueLabel.BackgroundTransparency = 1
FovValueLabel.Font = Enum.Font.SourceSans
FovValueLabel.TextSize = 16
FovValueLabel.Parent = SliderFrame

local FovValue = 0

local dragging = false
SliderBtn.MouseButton1Down:Connect(function()
    dragging = true
end)
Mouse.Move:Connect(function()
    if dragging then
        local mouseX = Mouse.X
        local frameX = SliderFrame.AbsolutePosition.X
        local min = 0
        local max = SliderFrame.AbsoluteSize.X - SliderBtn.AbsoluteSize.X
        local posX = math.clamp(mouseX - frameX, min, max)
        SliderBtn.Position = UDim2.new(0, posX, 0, -5)
        FovValue = math.floor((posX/max)*360)
        FovValueLabel.Text = tostring(FovValue)
    end
end)
Mouse.Button1Up:Connect(function()
    dragging = false
end)

-- Função de botão ON/OFF
local function createToggleBtn(txt, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 220, 0, 34)
    btn.Position = UDim2.new(0, 15, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(32,32,32)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.fromRGB(230,230,230)
    btn.Text = txt .. " [OFF]"
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 19
    btn.Parent = Frame
    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = txt .. (state and " [ON]" or " [OFF]")
        callback(state)
    end)
    return btn
end

-- ESP
local function toggleESP(state)
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            if state then
                if not plr.Character:FindFirstChild("ESPBox") then
                    local box = Instance.new("BoxHandleAdornment")
                    box.Name = "ESPBox"
                    box.Adornee = plr.Character.HumanoidRootPart
                    box.Size = Vector3.new(3,6,3)
                    box.Color3 = Color3.new(1,0,0)
                    box.AlwaysOnTop = true
                    box.Transparency = 0.6
                    box.Parent = plr.Character
                end
            else
                if plr.Character:FindFirstChild("ESPBox") then
                    plr.Character.ESPBox:Destroy()
                end
            end
        end
    end
end

-- AIMBOT SILENT HEADSHOT
local AimbotOn = false
local function getClosestToMouse()
    local closest, dist = nil, math.huge
    for _,plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(plr.Character.Head.Position)
            if onScreen then
                local mag = (Vector2.new(pos.X,pos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                if mag < dist and mag < FovValue then
                    dist = mag
                    closest = plr.Character.Head
                end
            end
        end
    end
    return closest
end

Mouse.Button1Down:Connect(function()
    if AimbotOn then
        local head = getClosestToMouse()
        if head then
            Mouse.Target = head
        end
    end
end)

-- GOD MODE
local GodModeOn = false
local function godMode(state)
    GodModeOn = state
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        if state then
            LocalPlayer.Character.Humanoid.Name = "1"
        else
            LocalPlayer.Character.Humanoid.Name = "Humanoid"
        end
    end
end

-- BOTÕES
local y = 60
createToggleBtn("ESP Vermelho", y, toggleESP)
createToggleBtn("Aimbot Silent Head", y+40, function(state) AimbotOn = state end)
createToggleBtn("God Mode", y+80, godMode)

-- Outras funções podem ser adicionadas abaixo usando o mesmo padrão de createToggleBtn
