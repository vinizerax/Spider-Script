-- Serviços necessários
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configurações iniciais
local NoclipActive = false
local EspActive = false
local KillAllActive = false
local XrayActive = false
local FullBrightActive = false
local NightVisionActive = false
local EspHighlights = {}
local CurrentTarget = nil
local XrayParts = {}
local OriginalLighting = {
    Ambient = Lighting.Ambient,
    Brightness = Lighting.Brightness,
    ColorShift_Top = Lighting.ColorShift_Top,
    OutdoorAmbient = Lighting.OutdoorAmbient
}

-- Cria a GUI principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "HackGUI"
ScreenGui.Parent = game:GetService("CoreGui")

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 200, 0, 340) -- Aumentado para novos botões
MainFrame.Position = UDim2.new(0, 10, 0.5, -170)
MainFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
MainFrame.BackgroundTransparency = 0.3
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

-- Título
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.Text = "Hack Menu v1.1"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18
Title.Parent = MainFrame

-- Função para criar botões
local function CreateButton(name, yPosition, parent)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.9, 0, 0, 40)
    button.Position = UDim2.new(0.05, 0, 0, yPosition)
    button.Text = name
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.Font = Enum.Font.SourceSansBold
    button.TextSize = 16
    button.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 5)
    corner.Parent = button
    
    return button
end

-- Criar botões
local NoclipButton = CreateButton("NoClip: OFF", 40, MainFrame)
local EspButton = CreateButton("ESP: OFF", 90, MainFrame)
local KillAllButton = CreateButton("KILL ALL", 140, MainFrame)
local XrayButton = CreateButton("XRAY: OFF", 190, MainFrame)
local FullBrightButton = CreateButton("FullBright: OFF", 240, MainFrame)
local NightVisionButton = CreateButton("Night Vision: OFF", 290, MainFrame)

-- Função FullBright
local function ToggleFullBright()
    FullBrightActive = not FullBrightActive
    FullBrightButton.Text = "FullBright: " .. (FullBrightActive and "ON" or "OFF")
    
    if FullBrightActive then
        Lighting.Ambient = Color3.new(1, 1, 1)
        Lighting.Brightness = 2
        Lighting.ColorShift_Top = Color3.new(1, 1, 1)
        Lighting.OutdoorAmbient = Color3.new(1, 1, 1)
    else
        Lighting.Ambient = OriginalLighting.Ambient
        Lighting.Brightness = OriginalLighting.Brightness
        Lighting.ColorShift_Top = OriginalLighting.ColorShift_Top
        Lighting.OutdoorAmbient = OriginalLighting.OutdoorAmbient
    end
end

-- Função Visão Noturna
local function ToggleNightVision()
    NightVisionActive = not NightVisionActive
    NightVisionButton.Text = "Night Vision: " .. (NightVisionActive and "ON" or "OFF")
    
    if NightVisionActive then
        -- Cria um efeito de visão noturna
        local nightVisionEffect = Instance.new("ColorCorrectionEffect")
        nightVisionEffect.Name = "NightVisionEffect"
        nightVisionEffect.Brightness = 0.1
        nightVisionEffect.Contrast = 0.5
        nightVisionEffect.Saturation = -1
        nightVisionEffect.TintColor = Color3.fromRGB(0, 255, 0)
        nightVisionEffect.Parent = Lighting
        
        -- Ajusta a iluminação para visão noturna
        Lighting.Ambient = Color3.fromRGB(0, 50, 0)
        Lighting.Brightness = 0.2
    else
        -- Remove o efeito
        local effect = Lighting:FindFirstChild("NightVisionEffect")
        if effect then
            effect:Destroy()
        end
        
        -- Restaura a iluminação original
        Lighting.Ambient = OriginalLighting.Ambient
        Lighting.Brightness = OriginalLighting.Brightness
    end
end

-- (As outras funções permanecem iguais: ToggleNoClip, ToggleESP, StartKillAll, ToggleXray)

-- Conectar botões às funções
NoclipButton.MouseButton1Click:Connect(ToggleNoClip)
EspButton.MouseButton1Click:Connect(ToggleESP)
KillAllButton.MouseButton1Click:Connect(StartKillAll)
XrayButton.MouseButton1Click:Connect(ToggleXray)
FullBrightButton.MouseButton1Click:Connect(ToggleFullBright)
NightVisionButton.MouseButton1Click:Connect(ToggleNightVision)

-- Fechar GUI com tecla (opcional)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.RightShift and not gameProcessed then
        MainFrame.Visible = not MainFrame.Visible
    end
end)

print("Script carregado com sucesso! GUI visível na tela.")
