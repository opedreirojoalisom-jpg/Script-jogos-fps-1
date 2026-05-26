--[[ 
    SISTEMA AIMBOT PROFISSIONAL
    Coloque este script em StarterPlayerScripts
]]

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Variáveis do sistema
local aimbotActive = false
local currentTarget = nil
local aimCircle = nil
local aimbotUI = nil

-- Função para criar UI moderna
local function createUI()
    -- Criar ScreenGui
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "AimbotUI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = player:WaitForChild("PlayerGui")
    
    -- Frame principal com design moderno
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 280, 0, 380)
    mainFrame.Position = UDim2.new(0, 20, 0.5, -190)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
    mainFrame.BackgroundTransparency = 0.08
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = screenGui
    
    -- Blur effect
    local uiCorner = Instance.new("UICorner")
    uiCorner.CornerRadius = UDim.new(0, 12)
    uiCorner.Parent = mainFrame
    
    -- Sombra
    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 0, 1, 0)
    shadow.Position = UDim2.new(0, 5, 0, 5)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    shadow.BackgroundTransparency = 0.7
    shadow.BorderSizePixel = 0
    shadow.ZIndex = -1
    shadow.Parent = mainFrame
    
    local shadowCorner = Instance.new("UICorner")
    shadowCorner.CornerRadius = UDim.new(0, 12)
    shadowCorner.Parent = shadow
    
    -- Título
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 50)
    title.Position = UDim2.new(0, 0, 0, 0)
    title.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    title.BackgroundTransparency = 0.05
    title.Text = "⚡ AIMBOT PRO ⚡"
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BorderSizePixel = 0
    title.Parent = mainFrame
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 12)
    titleCorner.Parent = title
    
    -- Botão Aimbot (Toggle)
    local aimbotButton = Instance.new("TextButton")
    aimbotButton.Size = UDim2.new(0.8, 0, 0, 50)
    aimbotButton.Position = UDim2.new(0.1, 0, 0, 70)
    aimbotButton.BackgroundColor3 = Color3.fromRGB(66, 133, 244)
    aimbotButton.Text = "🔴 AIMBOT: OFF"
    aimbotButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    aimbotButton.TextSize = 16
    aimbotButton.Font = Enum.Font.GothamSemibold
    aimbotButton.BorderSizePixel = 0
    aimbotButton.Parent = mainFrame
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 8)
    buttonCorner.Parent = aimbotButton
    
    -- TextBox para ajustar raio
    local radiusLabel = Instance.new("TextLabel")
    radiusLabel.Size = UDim2.new(0.8, 0, 0, 30)
    radiusLabel.Position = UDim2.new(0.1, 0, 0, 140)
    radiusLabel.BackgroundTransparency = 1
    radiusLabel.Text = "Raio do Circulo:"
    radiusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    radiusLabel.TextSize = 14
    radiusLabel.Font = Enum.Font.Gotham
    radiusLabel.TextXAlignment = Enum.TextXAlignment.Left
    radiusLabel.Parent = mainFrame
    
    local radiusBox = Instance.new("TextBox")
    radiusBox.Size = UDim2.new(0.8, 0, 0, 35)
    radiusBox.Position = UDim2.new(0.1, 0, 0, 170)
    radiusBox.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
    radiusBox.Text = "150"
    radiusBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    radiusBox.TextSize = 14
    radiusBox.Font = Enum.Font.Gotham
    radiusBox.PlaceholderText = "Raio em pixels"
    radiusBox.BorderSizePixel = 0
    radiusBox.Parent = mainFrame
    
    local radiusCorner = Instance.new("UICorner")
    radiusCorner.CornerRadius = UDim.new(0, 6)
    radiusCorner.Parent = radiusBox
    
    -- Slider para suavização
    local smoothLabel = Instance.new("TextLabel")
    smoothLabel.Size = UDim2.new(0.8, 0, 0, 30)
    smoothLabel.Position = UDim2.new(0.1, 0, 0, 220)
    smoothLabel.BackgroundTransparency = 1
    smoothLabel.Text = "Suavização:"
    smoothLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    smoothLabel.TextSize = 14
    smoothLabel.Font = Enum.Font.Gotham
    smoothLabel.TextXAlignment = Enum.TextXAlignment.Left
    smoothLabel.Parent = mainFrame
    
    local smoothValue = Instance.new("TextLabel")
    smoothValue.Size = UDim2.new(0.2, 0, 0, 30)
    smoothValue.Position = UDim2.new(0.7, 0, 0, 220)
    smoothValue.BackgroundTransparency = 1
    smoothValue.Text = "0.3"
    smoothValue.TextColor3 = Color3.fromRGB(100, 200, 255)
    smoothValue.TextSize = 14
    smoothValue.Font = Enum.Font.GothamBold
    smoothValue.TextXAlignment = Enum.TextXAlignment.Right
    smoothValue.Parent = mainFrame
    
    local smoothSlider = Instance.new("Frame")
    smoothSlider.Size = UDim2.new(0.8, 0, 0, 4)
    smoothSlider.Position = UDim2.new(0.1, 0, 0, 255)
    smoothSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    smoothSlider.BorderSizePixel = 0
    smoothSlider.Parent = mainFrame
    
    local smoothFill = Instance.new("Frame")
    smoothFill.Size = UDim2.new(0.3, 0, 1, 0)
    smoothFill.BackgroundColor3 = Color3.fromRGB(66, 133, 244)
    smoothFill.BorderSizePixel = 0
    smoothFill.Parent = smoothSlider
    
    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(1, 0)
    sliderCorner.Parent = smoothSlider
    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = smoothFill
    
    -- Status
    local statusLabel = Instance.new("TextLabel")
    statusLabel.Size = UDim2.new(0.8, 0, 0, 40)
    statusLabel.Position = UDim2.new(0.1, 0, 0, 280)
    statusLabel.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    statusLabel.BackgroundTransparency = 0.3
    statusLabel.Text = "Status: Aguardando"
    statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    statusLabel.TextSize = 12
    statusLabel.Font = Enum.Font.Gotham
    statusLabel.BorderSizePixel = 0
    statusLabel.Parent = mainFrame
    
    local statusCorner = Instance.new("UICorner")
    statusCorner.CornerRadius = UDim.new(0, 6)
    statusCorner.Parent = statusLabel
    
    -- Criar círculo central
    local aimCircle = Instance.new("Frame")
    aimCircle.Size = UDim2.new(0, 150, 0, 150)
    aimCircle.Position = UDim2.new(0.5, -75, 0.5, -75)
    aimCircle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    aimCircle.BackgroundTransparency = 0.8
    aimCircle.BorderSizePixel = 2
    aimCircle.BorderColor3 = Color3.fromRGB(255, 0, 0)
    aimCircle.Visible = false
    aimCircle.Parent = screenGui
    
    local circleCorner = Instance.new("UICorner")
    circleCorner.CornerRadius = UDim.new(1, 0)
    circleCorner.Parent = aimCircle
    
    -- Centro do círculo
    local centerDot = Instance.new("Frame")
    centerDot.Size = UDim2.new(0, 4, 0, 4)
    centerDot.Position = UDim2.new(0.5, -2, 0.5, -2)
    centerDot.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    centerDot.BorderSizePixel = 0
    centerDot.Parent = aimCircle
    
    local centerCorner = Instance.new("UICorner")
    centerCorner.CornerRadius = UDim.new(1, 0)
    centerCorner.Parent = centerDot
    
    -- Funções de callback
    local function updateRadius()
        local newRadius = tonumber(radiusBox.Text)
        if newRadius and newRadius > 0 and newRadius <= 500 then
            aimCircle.Size = UDim2.new(0, newRadius * 2, 0, newRadius * 2)
            aimCircle.Position = UDim2.new(0.5, -newRadius, 0.5, -newRadius)
        end
    end
    
    local function updateSmooth()
        local smooth = smoothFill.Size.X.Scale
        smoothValue.Text = string.format("%.2f", smooth)
    end
    
    -- Eventos UI
    aimbotButton.MouseButton1Click:Connect(function()
        aimbotActive = not aimbotActive
        if aimbotActive then
            aimbotButton.BackgroundColor3 = Color3.fromRGB(220, 53, 69)
            aimbotButton.Text = "🟢 AIMBOT: ON"
            aimCircle.Visible = true
            statusLabel.Text = "Status: AIMBOT ATIVO"
            statusLabel.TextColor3 = Color3.fromRGB(100, 255, 100)
        else
            aimbotButton.BackgroundColor3 = Color3.fromRGB(66, 133, 244)
            aimbotButton.Text = "🔴 AIMBOT: OFF"
            aimCircle.Visible = false
            statusLabel.Text = "Status: AIMBOT DESATIVADO"
            statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
            currentTarget = nil
        end
    end)
    
    radiusBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            updateRadius()
        end
    end)
    
    -- Slider interativo
    local dragging = false
    smoothSlider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local mousePos = UserInputService:GetMouseLocation()
            local sliderPos = smoothSlider.AbsolutePosition
            local relativeX = math.clamp((mousePos.X - sliderPos.X) / smoothSlider.AbsoluteSize.X, 0, 1)
            smoothFill.Size = UDim2.new(relativeX, 0, 1, 0)
            updateSmooth()
        end
    end)
    
    return aimCircle, radiusBox, smoothFill, statusLabel
end

-- Função para encontrar melhor alvo dentro do círculo
local function findTargetInCircle(circleRadius)
    local closestTarget = nil
    local closestDistance = circleRadius
    
    local centerScreen = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    
    for _, targetPlayer in ipairs(Players:GetPlayers()) do
        if targetPlayer ~= player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("Head") then
            local headPos = targetPlayer.Character.Head.Position
            local screenPos, onScreen = Camera:WorldToViewportPoint(headPos)
            
            if onScreen then
                local screenVector = Vector2.new(screenPos.X, screenPos.Y)
                local distance = (screenVector - centerScreen).Magnitude
                
                if distance < closestDistance then
                    closestDistance = distance
                    closestTarget = targetPlayer
                end
            end
        end
    end
    
    return closestTarget
end

-- Sistema de aimbot com suavização
local function smoothAimbot(target, smoothFactor)
    if not target or not target.Character or not target.Character:FindFirstChild("Head") then
        return
    end
    
    local headPos = target.Character.Head.Position
    local currentCFrame = Camera.CFrame
    local targetCFrame = CFrame.new(Camera.CFrame.Position, headPos)
    
    -- Aplicar suavização
    local newCFrame = currentCFrame:Lerp(targetCFrame, smoothFactor)
    Camera.CFrame = newCFrame
end

-- Loop principal
local circle, radiusBox, smoothFill, statusLabel = createUI()
local aimbotRadius = 150
local smoothValue = 0.3

RunService.RenderStepped:Connect(function()
    if not aimbotActive then return end
    
    -- Atualizar valores
    aimbotRadius = tonumber(radiusBox.Text) or 150
    smoothValue = smoothFill.Size.X.Scale
    
    -- Encontrar alvo
    local target = findTargetInCircle(aimbotRadius)
    
    if target then
        currentTarget = target
        statusLabel.Text = "Status: ✅ Alvo adquirido - " .. target.Name
        smoothAimbot(target, smoothValue)
        
        -- Efeito visual no círculo
        circle.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        circle.BorderColor3 = Color3.fromRGB(0, 255, 0)
        for _, child in ipairs(circle:GetChildren()) do
            if child:IsA("Frame") then
                child.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
            end
        end
    else
        currentTarget = nil
        statusLabel.Text = "Status: 🔍 Procurando alvos..."
        
        -- Resetar cor do círculo
        circle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        circle.BorderColor3 = Color3.fromRGB(255, 0, 0)
        for _, child in ipairs(circle:GetChildren()) do
            if child:IsA("Frame") then
                child.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            end
        end
    end
end)

print("Sistema Aimbot Profissional carregado com sucesso!")
