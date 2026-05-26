-- Configuração da GUI (Aimbot + ESP)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Variáveis
local aimbotEnabled = false
local espEnabled = false
local aimbotCircle = nil
local espLines = {}
local target = nil
local circleRadius = 150  -- Raio do círculo em pixels

-- Criar GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ProfessionalGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Estilo preto e branco
local function applyStyle(button)
    button.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
    button.BorderColor3 = Color3.new(0.3, 0.3, 0.3)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.GothamBold
    button.TextSize = 14
end

-- Frame principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 350, 0, 450)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -225)
mainFrame.BackgroundColor3 = Color3.new(0.05, 0.05, 0.05)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 2
mainFrame.BorderColor3 = Color3.new(0.3, 0.3, 0.3)
mainFrame.Parent = screenGui

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.new(0.08, 0.08, 0.08)
title.Text = "TACTICAL SYSTEM v1.0"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = mainFrame

-- Abas
local tabMain = Instance.new("TextButton")
tabMain.Size = UDim2.new(0.5, 0, 0, 35)
tabMain.Position = UDim2.new(0, 0, 0, 40)
tabMain.Text = "MAIN"
tabMain.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
applyStyle(tabMain)
tabMain.Parent = mainFrame

local tabESP = Instance.new("TextButton")
tabESP.Size = UDim2.new(0.5, 0, 0, 35)
tabESP.Position = UDim2.new(0.5, 0, 0, 40)
tabESP.Text = "ESP"
applyStyle(tabESP)
tabESP.Parent = mainFrame

-- Conteúdo das abas
local mainContent = Instance.new("Frame")
mainContent.Size = UDim2.new(1, 0, 1, -75)
mainContent.Position = UDim2.new(0, 0, 0, 75)
mainContent.BackgroundTransparency = 1
mainContent.Parent = mainFrame

local espContent = Instance.new("Frame")
espContent.Size = UDim2.new(1, 0, 1, -75)
espContent.Position = UDim2.new(0, 0, 0, 75)
espContent.BackgroundTransparency = 1
espContent.Visible = false
espContent.Parent = mainFrame

-- Botão Aimbot (Main)
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0.8, 0, 0, 50)
aimbotButton.Position = UDim2.new(0.1, 0, 0.2, 0)
aimbotButton.Text = "AIMBOT: OFF"
applyStyle(aimbotButton)
aimbotButton.BackgroundColor3 = Color3.new(0.05, 0.05, 0.05)
aimbotButton.Parent = mainContent

-- Status do Aimbot
local aimbotStatus = Instance.new("TextLabel")
aimbotStatus.Size = UDim2.new(0.8, 0, 0, 30)
aimbotStatus.Position = UDim2.new(0.1, 0, 0.4, 0)
aimbotStatus.Text = "Status: Desativado"
aimbotStatus.TextColor3 = Color3.new(0.5, 0.5, 0.5)
aimbotStatus.BackgroundTransparency = 1
aimbotStatus.Font = Enum.Font.Gotham
aimbotStatus.TextSize = 12
aimbotStatus.Parent = mainContent

-- Slider de sensibilidade (visual)
local sensText = Instance.new("TextLabel)
sensText.Size = UDim2.new(0.8, 0, 0, 20)
sensText.Position = UDim2.new(0.1, 0, 0.55, 0)
sensText.Text = "Sensibilidade: Alta"
sensText.TextColor3 = Color3.new(0.7, 0.7, 0.7)
sensText.BackgroundTransparency = 1
sensText.Font = Enum.Font.Gotham
sensText.TextSize = 12
sensText.Parent = mainContent

-- Botão ESP (ESP)
local espButton = Instance.new("TextButton")
espButton.Size = UDim2.new(0.8, 0, 0, 50)
espButton.Position = UDim2.new(0.1, 0, 0.2, 0)
espButton.Text = "ESP: OFF"
applyStyle(espButton)
espButton.BackgroundColor3 = Color3.new(0.05, 0.05, 0.05)
espButton.Parent = espContent

local espStatus = Instance.new("TextLabel")
espStatus.Size = UDim2.new(0.8, 0, 0, 30)
espStatus.Position = UDim2.new(0.1, 0, 0.4, 0)
espStatus.Text = "Status: Desativado"
espStatus.TextColor3 = Color3.new(0.5, 0.5, 0.5)
espStatus.BackgroundTransparency = 1
espStatus.Font = Enum.Font.Gotham
espStatus.TextSize = 12
espStatus.Parent = espContent

-- Botão Fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
closeButton.BorderSizePixel = 0
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 18
closeButton.Parent = mainFrame

-- Função para criar círculo do aimbot
local function createAimbotCircle()
    if aimbotCircle then aimbotCircle:Destroy() end
    
    local circle = Instance.new("ImageLabel")
    circle.Size = UDim2.new(0, circleRadius * 2, 0, circleRadius * 2)
    circle.Position = UDim2.new(0.5, -circleRadius, 0.5, -circleRadius)
    circle.BackgroundTransparency = 1
    circle.Image = "rbxassetid://266036533"  -- Círculo transparente
    circle.ImageColor3 = Color3.new(1, 1, 1)
    circle.ImageTransparency = 0.6
    circle.ZIndex = 999
    circle.Parent = screenGui
    
    -- Borda do círculo
    local border = Instance.new("ImageLabel")
    border.Size = UDim2.new(1, 10, 1, 10)
    border.Position = UDim2.new(0, -5, 0, -5)
    border.BackgroundTransparency = 1
    border.Image = "rbxassetid://266036533"
    border.ImageColor3 = Color3.new(0, 0, 0)
    border.ImageTransparency = 0.3
    border.ZIndex = 998
    border.Parent = circle
    
    -- Linhas de mira (cruz)
    local crosshair = Instance.new("Frame")
    crosshair.Size = UDim2.new(0, 2, 0, 30)
    crosshair.Position = UDim2.new(0.5, -1, 0.5, -15)
    crosshair.BackgroundColor3 = Color3.new(1, 1, 1)
    crosshair.BorderSizePixel = 0
    crosshair.Parent = circle
    
    local crosshair2 = Instance.new("Frame")
    crosshair2.Size = UDim2.new(0, 30, 0, 2)
    crosshair2.Position = UDim2.new(0.5, -15, 0.5, -1)
    crosshair2.BackgroundColor3 = Color3.new(1, 1, 1)
    crosshair2.BorderSizePixel = 0
    crosshair2.Parent = circle
    
    return circle
end

-- Função para desenhar ESP (caixa ao redor do inimigo)
local function updateESP()
    if not espEnabled then
        for _, line in pairs(espLines) do
            line:Destroy()
        end
        espLines = {}
        return
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character.Humanoid.Health > 0 then
            local rootPart = player.Character.HumanoidRootPart
            local pos, onScreen = Camera:WorldToScreenPoint(rootPart.Position)
            
            if onScreen then
                if not espLines[player] then
                    -- Criar frame da ESP
                    local box = Instance.new("Frame")
                    box.Size = UDim2.new(0, 60, 0, 100)
                    box.BackgroundTransparency = 0.7
                    box.BackgroundColor3 = Color3.new(0, 0, 0)
                    box.BorderSizePixel = 2
                    box.BorderColor3 = Color3.new(1, 1, 1)
                    box.Parent = screenGui
                    
                    local nameLabel = Instance.new("TextLabel")
                    nameLabel.Size = UDim2.new(1, 0, 0, 20)
                    nameLabel.Position = UDim2.new(0, 0, 1, 0)
                    nameLabel.Text = player.Name
                    nameLabel.TextColor3 = Color3.new(1, 1, 1)
                    nameLabel.BackgroundTransparency = 1
                    nameLabel.Font = Enum.Font.GothamBold
                    nameLabel.TextSize = 12
                    nameLabel.Parent = box
                    
                    espLines[player] = box
                end
                
                -- Atualizar posição da caixa
                local distance = (Camera.CFrame.Position - rootPart.Position).Magnitude
                local boxSize = math.clamp(300 / distance, 30, 100)
                local boxWidth = math.clamp(200 / distance, 20, 60)
                
                espLines[player].Size = UDim2.new(0, boxWidth, 0, boxSize)
                espLines[player].Position = UDim2.new(0, pos.X - boxWidth/2, 0, pos.Y - boxSize/2)
            else
                if espLines[player] then
                    espLines[player]:Destroy()
                    espLines[player] = nil
                end
            end
        end
    end
end

-- Função do aimbot (cravar na cabeça)
local function aimbot()
    if not aimbotEnabled then return end
    
    local closestPlayer = nil
    local closestDistance = circleRadius
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") and player.Character.Humanoid.Health > 0 then
            local headPos = player.Character.Head.Position
            local screenPos, onScreen = Camera:WorldToScreenPoint(headPos)
            
            if onScreen then
                local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
                local distance = (Vector2.new(screenPos.X, screenPos.Y) - screenCenter).Magnitude
                
                if distance < closestDistance then
                    closestDistance = distance
                    closestPlayer = player
                end
            end
        end
    end
    
    if closestPlayer and closestPlayer.Character and closestPlayer.Character:FindFirstChild("Head") then
        local headPos = closestPlayer.Character.Head.Position
        -- Cravar câmera na cabeça
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, headPos)
    end
end

-- Eventos dos botões
aimbotButton.MouseButton1Click:Connect(function()
    aimbotEnabled = not aimbotEnabled
    aimbotButton.Text = aimbotEnabled and "AIMBOT: ON" or "AIMBOT: OFF"
    aimbotButton.BackgroundColor3 = aimbotEnabled and Color3.new(0.2, 0.2, 0.2) or Color3.new(0.05, 0.05, 0.05)
    aimbotStatus.Text = aimbotEnabled and "Status: Ativado" or "Status: Desativado"
    aimbotStatus.TextColor3 = aimbotEnabled and Color3.new(1, 1, 1) or Color3.new(0.5, 0.5, 0.5)
    
    if aimbotEnabled then
        aimbotCircle = createAimbotCircle()
    else
        if aimbotCircle then aimbotCircle:Destroy() end
        target = nil
    end
end)

espButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espButton.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    espButton.BackgroundColor3 = espEnabled and Color3.new(0.2, 0.2, 0.2) or Color3.new(0.05, 0.05, 0.05)
    espStatus.Text = espEnabled and "Status: Ativado" or "Status: Desativado"
    espStatus.TextColor3 = espEnabled and Color3.new(1, 1, 1) or Color3.new(0.5, 0.5, 0.5)
end)

-- Troca de abas
tabMain.MouseButton1Click:Connect(function()
    mainContent.Visible = true
    espContent.Visible = false
    tabMain.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
    tabESP.BackgroundColor3 = Color3.new(0.08, 0.08, 0.08)
end)

tabESP.MouseButton1Click:Connect(function()
    mainContent.Visible = false
    espContent.Visible = true
    tabESP.BackgroundColor3 = Color3.new(0.15, 0.15, 0.15)
    tabMain.BackgroundColor3 = Color3.new(0.08, 0.08, 0.08)
end)

closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Tornar GUI arrastável
local dragging = false
local dragStart
local startPos

mainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Loop principal
RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        aimbot()
    end
    
    if espEnabled then
        updateESP()
    end
end)
