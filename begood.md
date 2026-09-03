local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- ==========================================
-- CONFIGURAÇÕES INICIAIS
-- ==========================================
local jumpPowerDesejado = 24.3
local quickJumpPower = 100
local toggleKey = Enum.KeyCode.J
local quickJumpKey = Enum.KeyCode.Q

local guiVisible = true
local isQuickJumpActive = false
local listeningForKey = false
-- ==========================================

local function getTargetJumpPower()
    return isQuickJumpActive and quickJumpPower or jumpPowerDesejado
end

-- Criar GUI
local screen = Instance.new("ScreenGui")
screen.Name = "JumpPanel"
screen.ResetOnSpawn = false
screen.Parent = player:WaitForChild("PlayerGui")

-- Frame principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 350, 0, 340)
frame.Position = UDim2.new(0.5, -175, 0.5, -170)
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

-- Título
local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, -50, 0, 30)
titulo.Position = UDim2.new(0, 15, 0, 10)
titulo.BackgroundTransparency = 1
titulo.Text = "⚡ JumpPanel"
titulo.TextColor3 = Color3.fromRGB(70, 150, 200)
titulo.Font = Enum.Font.GothamBold
titulo.TextSize = 22
titulo.TextXAlignment = Enum.TextXAlignment.Left
titulo.Parent = frame

-- Botão Fechar
local fechar = Instance.new("TextButton")
fechar.Size = UDim2.new(0, 30, 0, 30)
fechar.Position = UDim2.new(1, -40, 0, 10)
fechar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
fechar.Text = "✕"
fechar.TextColor3 = Color3.fromRGB(255, 255, 255)
fechar.Font = Enum.Font.GothamBold
fechar.TextSize = 16
fechar.BorderSizePixel = 0
fechar.Parent = frame
Instance.new("UICorner", fechar).CornerRadius = UDim.new(0, 8)

fechar.MouseButton1Click:Connect(function()
    guiVisible = false
    frame.Visible = false
end)

-- Labels auxiliares
local function criarLabel(texto, posY)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -30, 0, 15)
    lbl.Position = UDim2.new(0, 15, 0, posY)
    lbl.BackgroundTransparency = 1
    lbl.Text = texto
