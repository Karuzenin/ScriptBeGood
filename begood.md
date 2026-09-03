local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

local jumpPowerDesejado = 24.3
local guiVisible = true
local toggleKey = Enum.KeyCode.J

-- Criar GUI
local screen = Instance.new("ScreenGui")
screen.Name = "JumpPanel"
screen.ResetOnSpawn = false
screen.Parent = player:WaitForChild("PlayerGui")

-- Frame principal com gradiente
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 350, 0, 280)
frame.Position = UDim2.new(0.5, -175, 0.5, -140)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screen

local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 15)

local stroke = Instance.new("UIStroke", frame)
stroke.Color = Color3.fromRGB(70, 150, 200)
stroke.Thickness = 2

-- Título principal
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, -20, 0, 40)
titulo.Position = UDim2.new(0, 10, 0, 10)
titulo.BackgroundTransparency = 1
titulo.Text = "⚡ JumpPower"
titulo.TextColor3 = Color3.fromRGB(70, 150, 200)
titulo.Font = Enum.Font.GothamBold
titulo.TextSize = 24
titulo.TextXAlignment = Enum.TextXAlignment.Left
titulo.Parent = frame

-- Botão fechar
local fechar = Instance.new("TextButton")
fechar.Size = UDim2.new(0, 30, 0, 30)
fechar.Position = UDim2.new(1, -40, 0, 10)
fechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
fechar.Text = "✕"
fechar.TextColor3 = Color3.fromRGB(255, 255, 255)
fechar.Font = Enum.Font.GothamBold
fechar.TextSize = 18
fechar.BorderSizePixel = 0
fechar.Parent = frame

Instance.new("UICorner", fechar).CornerRadius = UDim.new(0, 8)

fechar.MouseButton1Click:Connect(function()
    guiVisible = false
    frame.Visible = false
end)

-- Label do valor
local valorLabel = Instance.new("TextLabel")
valorLabel.Size = UDim2.new(1, -20, 0, 30)
valorLabel.Position = UDim2.new(0, 10, 0, 60)
valorLabel.BackgroundTransparency = 1
valorLabel.Text = "Valor Atual: 24.3"
valorLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
valorLabel.Font = Enum.Font.Gotham
valorLabel.TextSize = 14
valorLabel.Parent = frame

-- Input
local input = Instance.new("TextBox")
input.Size = UDim2.new(1, -20, 0, 40)
input.Position = UDim2.new(0, 10, 0, 100)
input.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
input.Text = "24.3"
input.TextColor3 = Color3.fromRGB(255, 255, 255)
input.Font = Enum.Font.Gotham
input.TextSize = 16
input.PlaceholderText = "Digite um valor (0-500)"
input.BorderSizePixel = 0
input.Parent = frame

Instance.new("UICorner", input).CornerRadius = UDim.new(0, 8)
Instance.new("UIStroke", input).Color = Color3.fromRGB(70, 150, 200)

-- Botão aplicar
local aplicar = Instance.new("TextButton")
aplicar.Size = UDim2.new(1, -20, 0, 40)
aplicar.Position = UDim2.new(0, 10, 0, 155)
aplicar.BackgroundColor3 = Color3.fromRGB(70, 150, 70)
aplicar.Text = "✓ Aplicar"
aplicar.TextColor3 = Color3.fromRGB(255, 255, 255)
aplicar.Font = Enum.Font.GothamBold
aplicar.TextSize = 16
aplicar.BorderSizePixel = 0
aplicar.Parent = frame

Instance.new("UICorner", aplicar).CornerRadius = UDim.new(0, 8)

-- Info keybind
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, -20, 0, 25)
infoLabel.Position = UDim2.new(0, 10, 0, 205)
infoLabel.BackgroundTransparency = 1
infoLabel.Text = "Pressione 'J' para abrir/fechar"
infoLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextSize = 12
infoLabel.Parent = frame

-- Status indicator
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -20, 0, 30)
statusLabel.Position = UDim2.new(0, 10, 0, 240)
statusLabel.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
statusLabel.Text = "● Ativo"
statusLabel.TextColor3 = Color3.fromRGB(70, 200, 100)
statusLabel.Font = Enum.Font.GothamBold
statusLabel.TextSize = 14
statusLabel.BorderSizePixel = 0
statusLabel.Parent = frame

Instance.new("UICorner", statusLabel).CornerRadius = UDim.new(0, 8)

-- Função atualizar valor
local function atualizarValor(valor)
    valor = math.clamp(valor, 0, 500)
    jumpPowerDesejado = valor
    valorLabel.Text = "Valor Atual: " .. tostring(valor)
    input.Text = tostring(valor)
end

-- Botão aplicar
aplicar.MouseButton1Click:Connect(function()
    local valor = tonumber(input.Text)
    if valor then
        atualizarValor(valor)
    end
end)

input.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local valor = tonumber(input.Text)
        if valor then
            atualizarValor(valor)
        end
    end
end)

-- Keybind
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == toggleKey then
        guiVisible = not guiVisible
        frame.Visible = guiVisible
    end
end)

-- Sistema de proteção
local function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid")

    humanoid:GetPropertyChangedSignal("JumpPower"):Connect(function()
        if humanoid.JumpPower ~= 0 then
            humanoid.JumpPower = jumpPowerDesejado
        end
    end)

    local connection
    connection = RunService.Heartbeat:Connect(function()
        if humanoid.Parent then
            if humanoid.JumpPower ~= 0 and humanoid.JumpPower ~= jumpPowerDesejado then
                humanoid.JumpPower = jumpPowerDesejado
            end
        else
            connection:Disconnect()
        end
    end)
end

if player.Character then
    onCharacterAdded(player.Character)
end

player.CharacterAdded:Connect(onCharacterAdded)
atualizarValor(24.3)
