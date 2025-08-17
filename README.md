--[[ 
  Roblox UI Menu estilizado igual imagem 5, com ESP, Aimbot Silent (FOV) e GodMode.
  Bolinha preta para abrir/fechar.
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui

-- Bolinha preta para abrir/fechar o menu
local CircleBtn = Instance.new("TextButton")
CircleBtn.Size = UDim2.new(0, 36, 0, 36)
CircleBtn.Position = UDim2.new(0, 10, 0, 80)
CircleBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
CircleBtn.BorderSizePixel = 0
CircleBtn.Text = ""
CircleBtn.Parent = ScreenGui
CircleBtn.Name = "OpenMenuCircle"
CircleBtn.AutoButtonColor = true
CircleBtn.ZIndex = 3
local uicorner = Instance.new("UICorner")
uicorner.CornerRadius = UDim.new(1, 0)
uicorner.Parent = CircleBtn

-- Menu (começa invisível)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 230, 0, 285)
Frame.Position = UDim2.new(0, 50, 0, 70)
Frame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Frame.BorderSizePixel = 0
Frame.Name = "StyledCheatMenu"
Frame.Parent = ScreenGui
Frame.Visible = false
Frame.ZIndex = 2

local FrameCorner = Instance.new("UICorner")
FrameCorner.CornerRadius = UDim.new(0, 10)
FrameCorner.Parent = Frame

-- Botão X
local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 30, 0, 28)
CloseBtn.Position = UDim2.new(1, -35, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(175, 41, 41)
CloseBtn.Text = "X"
CloseBtn.Font = Enum.Font.SourceSansBold
CloseBtn.TextColor3 = Color3.fromRGB(255,255,255)
CloseBtn.TextSize = 20
CloseBtn.Parent = Frame
CloseBtn.ZIndex = 4
local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 6)
CloseCorner.Parent = CloseBtn
CloseBtn.MouseButton1Click:Connect(function() Frame.Visible = false end)

-- Abrir/fechar via bolinha
CircleBtn.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- Slider FOV (estilo barra fina)
local Regulamento = Instance.new("TextLabel")
Regulamento.Size = UDim2.new(0, 140, 0, 22)
Regulamento.Position = UDim2.new(0, 15, 0, 12)
Regulamento.BackgroundTransparency = 1
Regulamento.TextColor3 = Color3.fromRGB(220,220,220)
Regulamento.Text = "Silent Fov Regulamento"
Regulamento.Font = Enum.Font.SourceSansBold
Regulamento.TextXAlignment = Enum.TextXAlignment.Left
Regulamento.TextSize = 15
Regulamento.Parent = Frame

local SliderFrame = Instance.new("Frame")
SliderFrame.Size = UDim2.new(0, 140, 0, 6)
SliderFrame.Position = UDim2.new(0, 15, 0, 35)
SliderFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
SliderFrame.BorderSizePixel = 0
SliderFrame.Parent = Frame
local SliderFrameCorner = Instance.new("UICorner")
SliderFrameCorner.CornerRadius = UDim.new(0, 4)
SliderFrameCorner.Parent = SliderFrame

local SliderBtn = Instance.new("TextButton")
SliderBtn.Size = UDim2.new(0, 15, 0, 13)
SliderBtn.Position = UDim2.new(0, 0, 0, -4)
SliderBtn.BackgroundColor3 = Color3.fromRGB(100,100,100)
SliderBtn.Text = ""
SliderBtn.Parent = SliderFrame
local SliderBtnCorner = Instance.new("UICorner")
SliderBtnCorner.CornerRadius = UDim.new(1, 0)
SliderBtnCorner.Parent = SliderBtn

local FovValueLabel = Instance.new("TextLabel")
FovValueLabel.Size = UDim2.new(0, 30, 0, 18)
FovValueLabel.Position = UDim2.new(1, 10, 0, -6)
FovValueLabel.Text = "0"
FovValueLabel.TextColor3 = Color3.fromRGB(230,230,230)
FovValueLabel.BackgroundTransparency = 1
FovValueLabel.Font = Enum.Font.SourceSans
FovValueLabel.TextSize = 15
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
        SliderBtn.Position = UDim2.new(0, posX, 0, -4)
        FovValue = math.floor((posX/max)*360)
        FovValueLabel.Text = tostring(FovValue)
    end
end)
Mouse.Button1Up:Connect(function()
    dragging = false
end)

-- Função para criar botões no estilo da imagem
local function createStyledBtn(txt, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 35)
    btn.Position = UDim2.new(0, 15, 0, yPos)
    btn.BackgroundColor3 = Color3.fromRGB(58, 58, 58)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.fromRGB(230,230,230)
    btn.Text = txt .. " [OFF]"
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    btn.Parent = Frame
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn
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
local function godMode(state)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        if state then
            LocalPlayer.Character.Humanoid.Name = "1"
        else
            LocalPlayer.Character.Humanoid.Name = "Humanoid"
        end
    end
end

-- Botões (posições ajustadas para estilo da imagem 5)
local y = 65
createStyledBtn("ESP Vermelho", y, toggleESP)
createStyledBtn("Aimbot Silent Head", y+45, function(state) AimbotOn = state end)
createStyledBtn("God Mode", y+90, godMode)

-- Se quiser adicionar mais opções, use o padrão acima!
