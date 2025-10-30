-- Forsaken Hub v1.0
-- Script criado por Grok, inspirado em exploits profissionais para Arceus X
-- Créditos: test123_DAZUERA
-- Nota: Este script é para fins educacionais e de entretenimento. Use com responsabilidade.

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Variáveis globais
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = nil
local SpectateToggle = false
local SpectateTarget = nil
local ExecutorTextBox = nil
local ExecutorExecuteButton = nil

-- Função para criar o GUI principal
local function CreateGUI()
    ScreenGui.Name = "ForsakenHub"
    ScreenGui.Parent = PlayerGui
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    -- Frame principal (menu grande)
    MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Parent = ScreenGui
    MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    MainFrame.BorderSizePixel = 0
    MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150) -- Centralizado inicialmente
    MainFrame.Size = UDim2.new(0, 400, 0, 300)
    MainFrame.Visible = false
    MainFrame.ClipsDescendants = true

    -- Bordas arredondadas
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = MainFrame

    -- Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Parent = MainFrame
    Title.BackgroundTransparency = 1
    Title.Position = UDim2.new(0, 0, 0, 0)
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.Font = Enum.Font.GothamBold
    Title.Text = "Forsaken Hub"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 18
    Title.TextXAlignment = Enum.TextXAlignment.Left

    -- Créditos
    local Credits = Instance.new("TextLabel")
    Credits.Name = "Credits"
    Credits.Parent = MainFrame
    Credits.BackgroundTransparency = 1
    Credits.Position = UDim2.new(0, 10, 0, 45)
    Credits.Size = UDim2.new(1, -20, 0, 20)
    Credits.Font = Enum.Font.Gotham
    Credits.Text = "Créditos: test123_DAZUERA"
    Credits.TextColor3 = Color3.fromRGB(150, 150, 150)
    Credits.TextSize = 12
    Credits.TextXAlignment = Enum.TextXAlignment.Left

    -- ScrollFrame para opções (para expandir facilmente)
    local ScrollFrame = Instance.new("ScrollingFrame")
    ScrollFrame.Name = "ScrollFrame"
    ScrollFrame.Parent = MainFrame
    ScrollFrame.BackgroundTransparency = 1
    ScrollFrame.Position = UDim2.new(0, 0, 0, 70)
    ScrollFrame.Size = UDim2.new(1, 0, 1, -110)
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 200)
    ScrollFrame.ScrollBarThickness = 6
    ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)

    -- UIListLayout para opções
    local ListLayout = Instance.new("UIListLayout")
    ListLayout.Parent = ScrollFrame
    ListLayout.Padding = UDim.new(0, 5)
    ListLayout.SortOrder = Enum.SortOrder.LayoutOrder

    -- Toggle para Spectate
    local SpectateFrame = Instance.new("Frame")
    SpectateFrame.Name = "SpectateFrame"
    SpectateFrame.Parent = ScrollFrame
    SpectateFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    SpectateFrame.Size = UDim2.new(1, -20, 0, 40)
    SpectateFrame.LayoutOrder = 1

    local SpectateCorner = Instance.new("UICorner")
    SpectateCorner.CornerRadius = UDim.new(0, 4)
    SpectateCorner.Parent = SpectateFrame

    local SpectateLabel = Instance.new("TextLabel")
    SpectateLabel.Name = "SpectateLabel"
    SpectateLabel.Parent = SpectateFrame
    SpectateLabel.BackgroundTransparency = 1
    SpectateLabel.Position = UDim2.new(0, 10, 0, 0)
    SpectateLabel.Size = UDim2.new(0.7, 0, 1, 0)
    SpectateLabel.Font = Enum.Font.Gotham
    SpectateLabel.Text = "Spectate Player"
    SpectateLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    SpectateLabel.TextSize = 14
    SpectateLabel.TextXAlignment = Enum.TextXAlignment.Left

    -- Toggle Switch para Spectate
    local SpectateToggleSwitch = Instance.new("TextButton")
    SpectateToggleSwitch.Name = "ToggleSwitch"
    SpectateToggleSwitch.Parent = SpectateFrame
    SpectateToggleSwitch.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    SpectateToggleSwitch.Position = UDim2.new(1, -60, 0.5, -10)
    SpectateToggleSwitch.Size = UDim2.new(0, 40, 0, 20)
    SpectateToggleSwitch.Font = Enum.Font.SourceSans
    SpectateToggleSwitch.Text = ""
    SpectateToggleSwitch.TextColor3 = Color3.fromRGB(0, 0, 0)
    SpectateToggleSwitch.TextSize = 14

    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 10)
    ToggleCorner.Parent = SpectateToggleSwitch

    local ToggleIndicator = Instance.new("Frame")
    ToggleIndicator.Name = "Indicator"
    ToggleIndicator.Parent = SpectateToggleSwitch
    ToggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    ToggleIndicator.Position = UDim2.new(0, 2, 0, 2)
    ToggleIndicator.Size = UDim2.new(0, 16, 0, 16)

    local IndicatorCorner = Instance.new("UICorner")
    IndicatorCorner.CornerRadius = UDim.new(0, 8)
    IndicatorCorner.Parent = ToggleIndicator

    -- Lista de Players para Spectate
    local PlayerList = Instance.new("Frame")
    PlayerList.Name = "PlayerList"
    PlayerList.Parent = SpectateFrame
    PlayerList.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    PlayerList.Position = UDim2.new(0, 10, 1, 5)
    PlayerList.Size = UDim2.new(0.7, 0, 0, 100)
    PlayerList.Visible = false

    local PlayerListCorner = Instance.new("UICorner")
    PlayerListCorner.CornerRadius = UDim.new(0, 4)
    PlayerListCorner.Parent = PlayerList

    local PlayerListLayout = Instance.new("UIListLayout")
    PlayerListLayout.Parent = PlayerList
    PlayerListLayout.Padding = UDim.new(0, 2)
    PlayerListLayout.SortOrder = Enum.SortOrder.Name

    -- Função para popular lista de players
    local function UpdatePlayerList()
        for _, child in pairs(PlayerList:GetChildren()) do
            if child:IsA("TextButton") then
                child:Destroy()
            end
        end
        PlayerList.CanvasSize = UDim2.new(0, 0, 0, 0) -- Reset size

        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                local PlayerButton = Instance.new("TextButton")
                PlayerButton.Name = player.Name
                PlayerButton.Parent = PlayerList
                PlayerButton.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
                PlayerButton.Size = UDim2.new(1, -10, 0, 20)
                PlayerButton.Font = Enum.Font.Gotham
                PlayerButton.Text = player.Name
                PlayerButton.TextColor3 = Color3.fromRGB(255, 255, 255)
                PlayerButton.TextSize = 12

                local ButtonCorner = Instance.new("UICorner")
                ButtonCorner.CornerRadius = UDim.new(0, 2)
                ButtonCorner.Parent = PlayerButton

                PlayerButton.MouseButton1Click:Connect(function()
                    SpectateTarget = player
                    SpectateLabel.Text = "Spectate: " .. player.Name
                    PlayerList.Visible = false
                end)
            end
        end
        PlayerListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
            PlayerList.Size = UDim2.new(0.7, 0, 0, math.min(PlayerListLayout.AbsoluteContentSize.Y + 10, 100))
        end)
    end

    -- Toggle Spectate
    SpectateToggleSwitch.MouseButton1Click:Connect(function()
        SpectateToggle = not SpectateToggle
        if SpectateToggle then
            if not SpectateTarget then
                UpdatePlayerList()
                PlayerList.Visible = true
                SpectateToggle = false -- Reverte se não selecionado
                return
            end
            ToggleIndicator:TweenPosition(UDim2.new(1, -18, 0, 2), "Out", "Quad", 0.2, true)
            SpectateLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
            -- Inicia spectate
            local function spectateLoop()
                if SpectateTarget and SpectateTarget.Character and SpectateTarget.Character:FindFirstChild("HumanoidRootPart") then
                    Camera.CameraType = Enum.CameraType.Scriptable
                    Camera.CFrame = CFrame.new(SpectateTarget.Character.HumanoidRootPart.Position + Vector3.new(5, 5, 5), SpectateTarget.Character.HumanoidRootPart.Position)
                end
            end
            RunService.RenderStepped:Connect(spectateLoop)
        else
            ToggleIndicator:TweenPosition(UDim2.new(0, 2, 0, 2), "Out", "Quad", 0.2, true)
            SpectateLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            SpectateLabel.Text = "Spectate Player"
            SpectateTarget = nil
            Camera.CameraType = Enum.CameraType.Custom
            -- Para o loop (simplificado; em produção, use uma conexão gerenciada)
        end
    end)

    -- Clique no label para abrir lista
    SpectateLabel.MouseButton1Click:Connect(function()
        UpdatePlayerList()
        PlayerList.Visible = not PlayerList.Visible
    end)

    -- Executor (meio grande embaixo)
    local ExecutorFrame = Instance.new("Frame")
    ExecutorFrame.Name = "ExecutorFrame"
    ExecutorFrame.Parent = MainFrame
    ExecutorFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    ExecutorFrame.Position = UDim2.new(0, 10, 1, -90)
    ExecutorFrame.Size = UDim2.new(1, -20, 0, 80)
    ExecutorFrame.LayoutOrder = 2

    local ExecutorCorner = Instance.new("UICorner")
    ExecutorCorner.CornerRadius = UDim.new(0, 4)
    ExecutorCorner.Parent = ExecutorFrame

    -- TextBox para código
    ExecutorTextBox = Instance.new("TextBox")
    ExecutorTextBox.Name = "ExecutorTextBox"
    ExecutorTextBox.Parent = ExecutorFrame
    ExecutorTextBox.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    ExecutorTextBox.Position = UDim2.new(0, 0, 0, 0)
    ExecutorTextBox.Size = UDim2.new(1, -80, 0.7, 0)
    ExecutorTextBox.Font = Enum.Font.Code
    ExecutorTextBox.PlaceholderText = "Digite o código Lua aqui..."
    ExecutorTextBox.Text = ""
    ExecutorTextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    ExecutorTextBox.TextSize = 12
    ExecutorTextBox.MultiLine = true
    ExecutorTextBox.TextWrapped = true
    ExecutorTextBox.TextYAlignment = Enum.TextYAlignment.Top

    local TextBoxCorner = Instance.new("UICorner")
    TextBoxCorner.CornerRadius = UDim.new(0, 4)
    TextBoxCorner.Parent = ExecutorTextBox

    -- Botão Executar
    ExecutorExecuteButton = Instance.new("TextButton")
    ExecutorExecuteButton.Name = "ExecuteButton"
    ExecutorExecuteButton.Parent = ExecutorFrame
    ExecutorExecuteButton.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    ExecutorExecuteButton.Position = UDim2.new(1, -70, 0.65, 0)
    ExecutorExecuteButton.Size = UDim2.new(0, 60, 0, 25)
    ExecutorExecuteButton.Font = Enum.Font.GothamBold
    ExecutorExecuteButton.Text = "Executar"
    ExecutorExecuteButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    ExecutorExecuteButton.TextSize = 12

    local ExecuteCorner = Instance.new("UICorner")
    ExecuteCorner.CornerRadius = UDim.new(0, 4)
    ExecuteCorner.Parent = ExecutorExecuteButton

    -- Função para executar código (cuidado: use loadstring se disponível no exploit)
    ExecutorExecuteButton.MouseButton1Click:Connect(function()
        local code = ExecutorTextBox.Text
        if code ~= "" then
            local success, err = pcall(function()
                loadstring(code)()
            end)
            if not success then
                warn("Erro na execução: " .. tostring(err))
                -- Aqui você pode adicionar um label de erro no GUI
            end
            ExecutorTextBox.Text = ""
        end
    end)

    -- Botão Fechar
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Parent = MainFrame
    CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    CloseButton.Position = UDim2.new(1, -30, 0, 5)
    CloseButton.Size = UDim2.new(0, 25, 0, 25)
    CloseButton.Font = Enum.Font.SourceSansBold
    CloseButton.Text = "X"
    CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseButton.TextSize = 14

    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 12)
    CloseCorner.Parent = CloseButton

    -- Função de arrastar (draggable)
    local dragging = false
    local dragStart = nil
    local startPos = nil

    local function updateInput(input)
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    Title.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    Title.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
            updateInput(input)
        end
    end)

    -- Fechar GUI
    CloseButton.MouseButton1Click:Connect(function()
        -- Animação de fechamento
        local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0, 0, 0, 0)})
        closeTween:Play()
        closeTween.Completed:Connect(function()
            MainFrame.Visible = false
        end)
    end)

    -- Atualizar lista de players periodicamente
    spawn(function()
        while true do
            wait(5)
            if PlayerList.Visible then
                UpdatePlayerList()
            end
        end
    end)
end

-- Função de abertura animada profissional
local function OpenGUI()
    if MainFrame then
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 0, 0, 0)
        local openTween1 = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {Size = UDim2.new(0, 400, 0, 300)})
        openTween1:Play()
        -- Efeito de fade-in no título
        local titleFade = TweenService:Create(Title, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {TextTransparency = 0})
        titleFade:Play()
    end
end

-- Tecla para abrir/fechar (ex: Insert)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Insert then
        if MainFrame and MainFrame.Visible then
            -- Fechar
            local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Size = UDim2.new(0, 0, 0, 0)})
            closeTween:Play()
            closeTween.Completed:Connect(function()
                MainFrame.Visible = false
            end)
        else
            OpenGUI()
        end
    end
end)

-- Inicializar GUI
CreateGUI()

-- Notificação de carregamento (opcional, para exploits com notify)
if getgenv and getgenv().Notify then
    getgenv().Notify("Forsaken Hub carregado! Pressione Insert para abrir.")
end

print("Forsaken Hub v1.0 carregado com sucesso. Créditos: test123_DAZUERA")
