--[[
██╗░░██╗███████╗██╗░░░░░██████╗░
██║░░██║██╔════╝██║░░░░░██╔══██╗
███████║█████╗░░██║░░░░░██████╦╝
██╔══██║██╔══╝░░██║░░░░░██╔══██╗
██║░░██║██████╗░███████╗██████╦╝
╚═╝░░╚═╝╚═════╝░╚══════╝╚═════╝░

          ARSENAL HUB v1.0
       Estilo: Sci-Fi / Neon
]]

-- Serviços
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local SoundService = game:GetService("SoundService")

local Player = Players.LocalPlayer
local Mouse = Player:GetMouse()
local Camera = workspace.CurrentCamera

-- Variáveis Principais
local ESP_Enabled = false
local FOV_Enabled = false
local FOV_Size = 150
local FOV_Transparency = 0.2
local UI_Color = Color3.new(0.3, 0, 0.6) -- Roxo padrão

-- == CRIAÇÃO DA INTERFACE == --

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ArsenalHub"
ScreenGui.Parent = Player.PlayerGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.ResetOnSpawn = false

-- Função Auxiliar: Gradiente
local function UIGradient(color, rotation)
    local g = Instance.new("UIGradient")
    g.Color = color
    g.Rotation = rotation or 90
    g.Transparency = NumberSequence.new({
        NumberSequenceKeypoint.new(0, 0),
        NumberSequenceKeypoint.new(1, 0.4)
    })
    return g
end

-- Função Auxiliar: Som
local function PlaySound(id)
    local s = Instance.new("Sound")
    s.SoundId = "rbxassetid://"..id
    s.Volume = 0.5
    s.Parent = SoundService
    s:Play()
    game.Debris:AddItem(s, 2)
end

-- == MAIN FRAME (VIDRO FUMÊ / GLASSMORPHISM) == --
local MainFrame = Instance.new("Frame")
local SideBar = Instance.new("Frame")
local Container = Instance.new("Frame")
local UICorner_Main = Instance.new("UICorner")
local UIStroke_Main = Instance.new("UIStroke")
local UIGradient_Main = UIGradient(ColorSequence.new{
    ColorSequenceKeypoint.new(0, UI_Color:Lerp(Color3.new(1,1,1), 0.1)),
    ColorSequenceKeypoint.new(1, UI_Color:Lerp(Color3.new(0,0,0), 0.4))
})

-- Propriedades
MainFrame.Size = UDim2.new(0, 550, 0, 380)
MainFrame.Position = UDim2.new(0.5, -275, 0.5, -190)
MainFrame.BackgroundColor3 = Color3.new(0.08, 0.08, 0.1)
MainFrame.BackgroundTransparency = 0.35
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui

UICorner_Main.CornerRadius = UDim.new(0, 20)
UICorner_Main.Parent = MainFrame

UIStroke_Main.Thickness = 1.5
UIStroke_Main.Color = UI_Color
UIStroke_Main.Transparency = 0.5
UIStroke_Main.Parent = MainFrame

UIGradient_Main.Parent = MainFrame

-- Barra Lateral
SideBar.Size = UDim2.new(0, 60, 1, 0)
SideBar.Position = UDim2.new(0,0,0,0)
SideBar.BackgroundColor3 = Color3.new(0.05,0.05,0.08)
SideBar.BackgroundTransparency = 0.2
SideBar.Parent = MainFrame
local sb_corner = Instance.new("UICorner", SideBar)
sb_corner.CornerRadius = UDim.new(0,20)

-- Container Conteudo
Container.Size = UDim2.new(1, -70, 1, -20)
Container.Position = UDim2.new(0, 65, 0, 10)
Container.BackgroundTransparency = 1
Container.Parent = MainFrame

-- == TITULO == --
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Position = UDim2.new(0,0,0,5)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "⦿ SYSTEM ARSENAL"
Title.TextColor3 = Color3.new(1,1,1)
Title.TextSize = 18
Title.Parent = MainFrame

-- == BOTÕES MODERNOS == --
local function CreateButton(name, pos, callback)
    local BtnFrame = Instance.new("TextButton")
    local BtnStroke = Instance.new("UIStroke")
    local BtnCorner = Instance.new("UICorner")
    
    BtnFrame.Name = name.."Btn"
    BtnFrame.Size = UDim2.new(0, 220, 0, 45)
    BtnFrame.Position = pos
    BtnFrame.BackgroundColor3 = Color3.new(0.12, 0.12, 0.15)
    BtnFrame.BackgroundTransparency = 0.3
    BtnFrame.Font = Enum.Font.GothamSemibold
    BtnFrame.Text = "▶ "..name
    BtnFrame.TextColor3 = Color3.new(1,1,1)
    BtnFrame.TextSize = 14
    BtnFrame.Parent = Container
    
    BtnCorner.CornerRadius = UDim.new(0,12)
    BtnCorner.Parent = BtnFrame
    
    BtnStroke.Thickness = 1
    BtnStroke.Color = UI_Color
    BtnStroke.Transparency = 0.6
    BtnStroke.Parent = BtnFrame

    -- Animação Hover
    BtnFrame.MouseEnter:Connect(function()
        TweenService:Create(BtnFrame, TweenInfo.new(0.2), {
            BackgroundTransparency = 0,
            Size = UDim2.new(0, 230, 0, 45)
        }):Play()
    end)
    BtnFrame.MouseLeave:Connect(function()
        TweenService:Create(BtnFrame, TweenInfo.new(0.2), {
            BackgroundTransparency = 0.3,
            Size = UDim2.new(0, 220, 0, 45)
        }):Play()
    end)
    
    BtnFrame.MouseButton1Click:Connect(function()
        PlaySound(152228962)
        callback()
    end)
    
    return BtnFrame
end

-- == CRIAR BOTÕES == --
local AimBtn = CreateButton("Aim Trainer", UDim2.new(0,10,0,10), function()
    FOV_Enabled = not FOV_Enabled
    AimBtn.Text = FOV_Enabled and "⏸ Aim Trainer" or "▶ Aim Trainer"
    AimBtn.BackgroundColor3 = FOV_Enabled and Color3.new(0.1,0.25,0.15) or Color3.new(0.12, 0.12, 0.15)
end)

local ESPBtn = CreateButton("ESP Training", UDim2.new(0,10,0,65), function()
    ESP_Enabled = not ESP_Enabled
    ESPBtn.Text = ESP_Enabled and "⏸ ESP Training" or "▶ ESP Training"
    ESPBtn.BackgroundColor3 = ESP_Enabled and Color3.new(0.1,0.25,0.15) or Color3.new(0.12, 0.12, 0.15)
end)

-- == INPUTS DE CONFIGURAÇÃO == --
local ConfigLabel = Instance.new("TextLabel")
ConfigLabel.Size = UDim2.new(0, 200, 0, 20)
ConfigLabel.Position = UDim2.new(0,10,0,120)
ConfigLabel.BackgroundTransparency = 1
ConfigLabel.Text = "⚙️ CONFIGURAÇÕES"
ConfigLabel.Font = Enum.Font.GothamBold
ConfigLabel.TextColor3 = Color3.new(0.8,0.8,1)
ConfigLabel.TextSize = 12
ConfigLabel.Parent = Container

-- Input Tamanho FOV
local InputSize = Instance.new("TextBox")
InputSize.Size = UDim2.new(0, 100, 0, 30)
InputSize.Position = UDim2.new(0,10,0,145)
InputSize.PlaceholderText = "FOV Size (100-300)"
InputSize.Text = "150"
InputSize.BackgroundColor3 = Color3.new(0.1,0.1,0.1)
InputSize.TextColor3 = Color3.new(1,1,1)
InputSize.Font = Enum.Font.Gotham
InputSize.Parent = Container
Instance.new("UICorner", InputSize).CornerRadius = UDim.new(0,6)

-- Input Transparencia
local InputTrans = Instance.new("TextBox")
InputTrans.Size = UDim2.new(0, 100, 0, 30)
InputTrans.Position = UDim2.new(0,120,0,145)
InputTrans.PlaceholderText = "Transp. (0-1)"
InputTrans.Text = "0.2"
InputTrans.BackgroundColor3 = Color3.new(0.1,0.1,0.1)
InputTrans.TextColor3 = Color3.new(1,1,1)
InputTrans.Font = Enum.Font.Gotham
InputTrans.Parent = Container
Instance.new("UICorner", InputTrans).CornerRadius = UDim.new(0,6)

-- Input Cor
local InputColor = Instance.new("TextBox")
InputColor.Size = UDim2.new(0, 100, 0, 30)
InputColor.Position = UDim2.new(0,10,0,185)
InputColor.PlaceholderText = "Cor R,G,B"
InputColor.Text = "0.3,0,0.6"
InputColor.BackgroundColor3 = Color3.new(0.1,0.1,0.1)
InputColor.TextColor3 = Color3.new(1,1,1)
InputColor.Font = Enum.Font.Gotham
InputColor.Parent = Container
Instance.new("UICorner", InputColor).CornerRadius = UDim.new(0,6)

-- Botão Aplicar
local ApplyBtn = Instance.new("TextButton")
ApplyBtn.Size = UDim2.new(0, 100, 0, 30)
ApplyBtn.Position = UDim2.new(0,120,0,185)
ApplyBtn.Text = "APLICAR"
ApplyBtn.BackgroundColor3 = UI_Color
ApplyBtn.TextColor3 = Color3.new(1,1,1)
ApplyBtn.Font = Enum.Font.GothamBold
ApplyBtn.Parent = Container
Instance.new("UICorner", ApplyBtn).CornerRadius = UDim.new(0,6)

ApplyBtn.MouseButton1Click:Connect(function()
    PlaySound(152228962)
    
    local s = tonumber(InputSize.Text)
    if s then FOV_Size = math.clamp(s, 50, 500) end
    
    local t = tonumber(InputTrans.Text)
    if t then FOV_Transparency = math.clamp(t, 0, 1) end
    
    local r, g, b = string.match(InputColor.Text, "(%d+.?%d*),(%d+.?%d*),(%d+.?%d*)")
    if r and g and b then
        UI_Color = Color3.new(tonumber(r), tonumber(g), tonumber(b))
        UIStroke_Main.Color = UI_Color
    end
end)

-- == SISTEMA DRAGGABLE == --
local Dragging, DragStart, StartPos = false, nil, nil
UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and Dragging then
        local Delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(
            StartPos.X.Scale, StartPos.X.Offset + Delta.X,
            StartPos.Y.Scale, StartPos.Y.Offset + Delta.Y
        )
    end
end)

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
        TweenService:Create(MainFrame, TweenInfo.new(0.1), {BackgroundTransparency = 0.1}):Play()
    end
end)

MainFrame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = false
        TweenService:Create(MainFrame, TweenInfo.new(0.1), {BackgroundTransparency = 0.35}):Play()
    end
end)

--[[
██████╗ ███████╗███████╗███╗   ███╗██╗███╗   ██╗██████╗ 
██╔══██╗██╔════╝██╔════╝████╗ ████║██║████╗  ██║██╔══██╗
██████╔╝█████╗  █████╗  ██╔████╔██║██║██╔██╗ ██║██║  ██║
██╔══██╗██╔══╝  ██╔══╝  ██║╚██╔╝██║██║██║╚██╗██║██║  ██║
██║  ██║███████╗███████╗██║ ╚═╝ ██║██║██║ ╚████║██████╔╝
╚═╝  ╚═╝╚══════╝╚══════╝╚═╝     ╚═╝╚═╝╚═╝  ╚═══╝╚═════╝
]]

-- == CIRCULO DE FOV (Drawing API) == --
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 2
FOVCircle.Color = Color3.new(0, 255, 255)
FOVCircle.Filled = false
FOVCircle.Visible = false
FOVCircle.ZIndex = 10
FOVCircle.Transparency = 1

-- == SISTEMA DE HIGHLIGHT E ESP == --
local ESPObjects = {}

local function IsTrainingNPC(model)
    return model:FindFirstChild("Humanoid") and (model.Name:lower():find("training") or model:FindFirstChild("IsTrainer"))
end

local function GetESP(model)
    if ESPObjects[model] then return ESPObjects[model] end
    
    local esp = {
        Box = Drawing.new("Square"),
        Name = Drawing.new("Text"),
        Dist = Drawing.new("Text")
    }
    
    esp.Box.Thickness = 1.5
    esp.Box.Filled = false
    esp.Box.ZIndex = 1
    esp.Name.Size = 14
    esp.Name.Font = 2
    esp.Name.ZIndex = 2
    esp.Dist.Size = 12
    esp.Dist.Font = 2
    esp.Dist.ZIndex = 2
    
    ESPObjects[model] = esp
    return esp
end

local function ClearESP()
    for model, esp in pairs(ESPObjects) do
        esp.Box:Remove()
        esp.Name:Remove()
        esp.Dist:Remove()
    end
    ESPObjects = {}
end

-- == LOOP PRINCIPAL == --
RunService.RenderStepped:Connect(function()
    local Center = Vector2.new(game.Workspace.CurrentCamera.ViewportSize.X / 2, game.Workspace.CurrentCamera.ViewportSize.Y / 2)
    
    -- Atualizar Circulo F
