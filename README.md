local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

-- Configurações
local settings = {
    esp = false,
    aimbot = false,
    spinbot = false,
    fly = false,
    speed = false,
    noclip = false
}

-- Criar GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CheetahPanel"
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 400)
mainFrame.Position = UDim2.new(0, 10, 0, 10)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(0, 255, 0)
stroke.Thickness = 2
stroke.Parent = mainFrame

-- Header
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 40)
header.BackgroundColor3 = Color3.fromRGB(0, 100, 0)
header.BorderSizePixel = 0
header.Parent = mainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 8)
headerCorner.Parent = header

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 1, 0)
title.BackgroundTransparency = 1
title.Text = "CHEETAH PANEL"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextSize = 18
title.Font = Enum.Font.GothamBold
title.Parent = header

-- Container para os botões
local buttonsContainer = Instance.new("Frame")
buttonsContainer.Size = UDim2.new(1, -20, 1, -60)
buttonsContainer.Position = UDim2.new(0, 10, 0, 50)
buttonsContainer.BackgroundTransparency = 1
buttonsContainer.Parent = mainFrame

-- ESP
local espObjects = {}
local function toggleESP()
    settings.esp = not settings.esp
    
    if settings.esp then
        for _, otherPlayer in pairs(Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local highlight = Instance.new("Highlight")
                highlight.FillColor = Color3.fromRGB(255, 0, 0)
                highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                highlight.FillTransparency = 0.5
                highlight.Parent = otherPlayer.Character
                espObjects[otherPlayer] = highlight
            end
        end
        espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        espButton.Text = "ESP: ON"
    else
        for _, highlight in pairs(espObjects) do
            highlight:Destroy()
        end
        espObjects = {}
        espButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        espButton.Text = "ESP: OFF"
    end
end

-- Aimbot
local aimbotConnection
local function toggleAimbot()
    settings.aimbot = not settings.aimbot
    
    if settings.aimbot then
        aimbotConnection = RunService.Heartbeat:Connect(function()
            if not player.Character then return end
            
            local closestPlayer = nil
            local closestDistance = math.huge
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            
            if not root then return end
            
            for _, otherPlayer in pairs(Players:GetPlayers()) do
                if otherPlayer ~= player and otherPlayer.Character then
                    local otherRoot = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                    if otherRoot then
                        local distance = (root.Position - otherRoot.Position).Magnitude
                        if distance < closestDistance then
                            closestDistance = distance
                            closestPlayer = otherPlayer
                        end
                    end
                end
            end
            
            if closestPlayer and closestPlayer.Character then
                local targetRoot = closestPlayer.Character:FindFirstChild("HumanoidRootPart")
                if targetRoot then
                    local currentCFrame = root.CFrame
                    local targetPosition = targetRoot.Position + Vector3.new(0, 2, 0)
                    local newCFrame = CFrame.lookAt(currentCFrame.Position, targetPosition)
                    root.CFrame = newCFrame
                end
            end
        end)
        aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        aimbotButton.Text = "AIMBOT: ON"
    else
        if aimbotConnection then
            aimbotConnection:Disconnect()
        end
        aimbotButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        aimbotButton.Text = "AIMBOT: OFF"
    end
end

-- Spinbot
local spinbotConnection
local function toggleSpinbot()
    settings.spinbot = not settings.spinbot
    
    if settings.spinbot then
        spinbotConnection = RunService.Heartbeat:Connect(function()
            if player.Character then
                local root = player.Character:FindFirstChild("HumanoidRootPart")
                if root then
                    root.CFrame = root.CFrame * CFrame.Angles(0, math.rad(30), 0)
                end
            end
        end)
        spinbotButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        spinbotButton.Text = "SPINBOT: ON"
    else
        if spinbotConnection then
            spinbotConnection:Disconnect()
        end
        spinbotButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        spinbotButton.Text = "SPINBOT: OFF"
    end
end

-- Fly
local flyConnection
local bodyVelocity
local function toggleFly()
    settings.fly = not settings.fly
    
    if settings.fly then
        if player.Character then
            local root = player.Character:FindFirstChild("HumanoidRootPart")
            if root then
                bodyVelocity = Instance.new("BodyVelocity")
                bodyVelocity.Velocity = Vector3.new(0, 0, 0)
                bodyVelocity.MaxForce = Vector3.new(40000, 40000, 40000)
                bodyVelocity.Parent = root
            end
        end
        
        flyConnection = RunService.Heartbeat:Connect(function()
            if not bodyVelocity then return end
            
            local direction = Vector3.new()
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                direction = direction + Vector3.new(0, 0, -1)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                direction = direction + Vector3.new(0, 0, 1)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                direction = direction + Vector3.new(-1, 0, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                direction = direction + Vector3.new(1, 0, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                direction = direction + Vector3.new(0, 1, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                direction = direction + Vector3.new(0, -1, 0)
            end
            
            bodyVelocity.Velocity = direction * 50
        end)
        flyButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        flyButton.Text = "FLY: ON"
    else
        if bodyVelocity then
            bodyVelocity:Destroy()
            bodyVelocity = nil
        end
        if flyConnection then
            flyConnection:Disconnect()
        end
        flyButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        flyButton.Text = "FLY: OFF"
    end
end

-- Speed
local function toggleSpeed()
    settings.speed = not settings.speed
    
    if player.Character then
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            if settings.speed then
                humanoid.WalkSpeed = 50
                speedButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
                speedButton.Text = "SPEED: ON"
            else
                humanoid.WalkSpeed = 16
                speedButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
                speedButton.Text = "SPEED: OFF"
            end
        end
    end
end

-- Noclip
local noclipConnection
local function toggleNoclip()
    settings.noclip = not settings.noclip
    
    if settings.noclip then
        noclipConnection = RunService.Stepped:Connect(function()
            if player.Character then
                for _, part in pairs(player.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
        noclipButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        noclipButton.Text = "NOCLIP: ON"
    else
        if noclipConnection then
            noclipConnection:Disconnect()
        end
        if player.Character then
            for _, part in pairs(player.Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
        noclipButton.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        noclipButton.Text = "NOCLIP: OFF"
    end
end

-- Função para criar botões
local function createButton(text, yPos, toggleFunc)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, 0, 0, 35)
    button.Position = UDim2.new(0, 0, 0, yPos)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 14
    button.Font = Enum.Font.Gotham
    button.Parent = buttonsContainer
    
    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = button
    
    button.MouseButton1Click:Connect(toggleFunc)
    
    return button
end

-- Criar botões DENTRO do painel
espButton = createButton("ESP: OFF", 10, toggleESP)
aimbotButton = createButton("AIMBOT: OFF", 55, toggleAimbot)
spinbotButton = createButton("SPINBOT: OFF", 100, toggleSpinbot)
flyButton = createButton("FLY: OFF", 145, toggleFly)
speedButton = createButton("SPEED: OFF", 190, toggleSpeed)
noclipButton = createButton("NOCLIP: OFF", 235, toggleNoclip)

-- Sistema de arrastar
local dragging = false
local dragInput, dragStart, startPos

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

header.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Tecla para mostrar/esconder (F9)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.F9 then
        mainFrame.Visible = not mainFrame.Visible
    end
end)

print("Cheetah Panel Carregado!")
print("Pressione F9 para mostrar/esconder o painel")
print("Clique nos botões DENTRO do painel para ativar funções")
