--// Forsaken Hub v2.0 | Arceus X | test123_DAZUERA
--// Animação profissional, UI arrastável, toggle switch, spectate + executor
--// Pressione INSERT para abrir/fechar

repeat wait() until game:IsLoaded()
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

--// Variáveis globais
local GUI_OPEN = false
local SPECTATE_ACTIVE = false
local SPECTATE_TARGET = nil
local SPECTATE_CONNECTION = nil
local EXECUTOR_HISTORY = {}

--// Criação da GUI com animação profissional
local function CreateForsakenHub()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "ForsakenHub"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 500, 0, 380)
    MainFrame.Position = UDim2.new(0.5, -250, 0.5, -190)
    MainFrame.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
    MainFrame.BorderSizePixel = 0
    MainFrame.Visible = false
    MainFrame.ClipsDescendants = true
    MainFrame.Parent = ScreenGui

    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, 14)
    UICorner.Parent = MainFrame

    local Glow = Instance.new("ImageLabel")
    Glow.Name = "Glow"
    Glow.Size = UDim2.new(1, 40, 1, 40)
    Glow.Position = UDim2.new(0, -20, 0, -20)
    Glow.BackgroundTransparency = 1
    Glow.Image = "rbxassetid://4996891970"
    Glow.ImageColor3 = Color3.fromRGB(100, 50, 255)
    Glow.ImageTransparency = 0.7
    Glow.ScaleType = Enum.ScaleType.Slice
    Glow.SliceCenter = Rect.new(20, 20, 280, 280)
    Glow.Parent = MainFrame

    --// Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.new(1, -60, 0, 40)
    Title.Position = UDim2.new(0, 20, 0, 10)
    Title.BackgroundTransparency = 1
    Title.Text = "FORSAKEN HUB"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.Font = Enum.Font.GothamBlack
    Title.TextSize = 20
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = MainFrame

    --// Créditos
    local Credits = Instance.new("TextLabel")
    Credits.Name = "Credits"
    Credits.Size = UDim2.new(1, -40, 0, 20)
    Credits.Position = UDim2.new(0, 20, 0, 45)
    Credits.BackgroundTransparency = 1
    Credits.Text = "Créditos: test123_DAZUERA"
    Credits.TextColor3 = Color3.fromRGB(180, 180, 180)
    Credits.Font = Enum.Font.Gotham
    Credits.TextSize = 12
    Credits.TextXAlignment = Enum.TextXAlignment.Left
    Credits.Parent = MainFrame

    --// Botão Fechar
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Name = "CloseBtn"
    CloseBtn.Size = UDim2.new(0, 30, 0, 30)
    CloseBtn.Position = UDim2.new(1, -40, 0, 10)
    CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    CloseBtn.Text = "×"
    CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.TextSize = 18
    CloseBtn.Parent = MainFrame

    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 8)
    CloseCorner.Parent = CloseBtn

    --// Drag System
    local dragging = false
    local dragInput, mousePos, framePos

    Title.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            mousePos = input.Position
            framePos = MainFrame.Position

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)

    Title.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
            local delta = input.Position - mousePos
            MainFrame.Position = UDim2.new(framePos.X.Scale, framePos.X.Offset + delta.X, framePos.Y.Scale, framePos.Y.Offset + delta.Y)
        end
    end)

    --// Spectate Section
    local SpectateFrame = Instance.new("Frame")
    SpectateFrame.Name = "SpectateFrame"
    SpectateFrame.Size = UDim2.new(1, -40, 0, 50)
    SpectateFrame.Position = UDim2.new(0, 20, 0, 80)
    SpectateFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 38)
    SpectateFrame.Parent = MainFrame

    local SpecCorner = Instance.new("UICorner")
    SpecCorner.CornerRadius = UDim.new(0, 8)
    SpecCorner.Parent = SpectateFrame

    local SpecLabel = Instance.new("TextLabel")
    SpecLabel.Size = UDim2.new(0.6, 0, 1, 0)
    SpecLabel.Position = UDim2.new(0, 15, 0, 0)
    SpecLabel.BackgroundTransparency = 1
    SpecLabel.Text = "Spectate Player"
    SpecLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    SpecLabel.Font = Enum.Font.Gotham
    SpecLabel.TextSize = 14
    SpecLabel.TextXAlignment = Enum.TextXAlignment.Left
    SpecLabel.Parent = SpectateFrame

    --// Toggle Switch
    local ToggleBtn = Instance.new("TextButton")
    ToggleBtn.Name = "Toggle"
    ToggleBtn.Size = UDim2.new(0, 50, 0, 26)
    ToggleBtn.Position = UDim2.new(1, -65, 0.5, -13)
    ToggleBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    ToggleBtn.Text = ""
    ToggleBtn.Parent = SpectateFrame

    local ToggleCorner = Instance.new("UICorner")
    ToggleCorner.CornerRadius = UDim.new(0, 13)
    ToggleCorner.Parent = ToggleBtn

    local Indicator = Instance.new("Frame")
    Indicator.Name = "Indicator"
    Indicator.Size = UDim2.new(0, 20, 0, 20)
    Indicator.Position = UDim2.new(0, 3, 0, 3)
    Indicator.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
    Indicator.Parent = ToggleBtn

    local IndCorner = Instance.new("UICorner")
    IndCorner.CornerRadius = UDim.new(0, 10)
    IndCorner.Parent = Indicator

    --// Player List (Dropdown)
    local PlayerList = Instance.new("ScrollingFrame")
    PlayerList.Name = "PlayerList"
    PlayerList.Size = UDim2.new(0.6, 0, 0, 120)
    PlayerList.Position = UDim2.new(0, 15, 1, 5)
    PlayerList.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    PlayerList.BorderSizePixel = 0
    PlayerList.ScrollBarThickness = 4
    PlayerList.Visible = false
    PlayerList.Parent = SpectateFrame

    local ListCorner = Instance.new("UICorner")
    ListCorner.CornerRadius = UDim.new(0, 6)
    ListCorner.Parent = PlayerList

    local ListLayout = Instance.new("UIListLayout")
    ListLayout.Padding = UDim.new(0, 2)
    ListLayout.Parent = PlayerList

    --// Atualiza lista de jogadores
    local function UpdatePlayers()
        for _, v in pairs(PlayerList:GetChildren()) do
            if v:IsA("TextButton") then v:Destroy() end
        end
        for _, plr in pairs(Players:GetPlayers()) do
            if plr ~= LocalPlayer and plr.Character then
                local btn = Instance.new("TextButton")
                btn.Size = UDim2.new(1, -10, 0, 26)
                btn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
                btn.Text = plr.DisplayName .. " (@" .. plr.Name .. ")"
                btn.TextColor3 = Color3.fromRGB(255, 255, 255)
                btn.Font = Enum.Font.Gotham
                btn.TextSize = 12
                btn.Parent = PlayerList

                local btnCorner = Instance.new("UICorner")
                btnCorner.CornerRadius = UDim.new(0, 4)
                btnCorner.Parent = btn

                btn.MouseButton1Click:Connect(function()
                    SPECTATE_TARGET = plr
                    SpecLabel.Text = "Spectate: " .. plr.DisplayName
                    PlayerList.Visible = false
                end)
            end
        end
        PlayerList.CanvasSize = UDim2.new(0, 0, 0, ListLayout.AbsoluteContentSize.Y + 10)
    end

    --// Clique no label abre lista
    SpecLabel.MouseButton1Click:Connect(function()
        UpdatePlayers()
        PlayerList.Visible = not PlayerList.Visible
    end)

    --// Toggle Spectate
    ToggleBtn.MouseButton1Click:Connect(function()
        SPECTATE_ACTIVE = not SPECTATE_ACTIVE

        if SPECTATE_ACTIVE then
            if not SPECTATE_TARGET then
                SPECTATE_ACTIVE = false
                ToggleBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
                Indicator:TweenPosition(UDim2.new(0, 3, 0, 3), "Out", "Quad", 0.2)
                return
            end

            ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
            Indicator:TweenPosition(UDim2.new(1, -23, 0, 3), "Out", "Quad", 0.2)

            Camera.CameraType = Enum.CameraType.Scriptable
            SPECTATE_CONNECTION = RunService.RenderStepped:Connect(function()
                if SPECTATE_TARGET and SPECTATE_TARGET.Character and SPECTATE_TARGET.Character:FindFirstChild("HumanoidRootPart") then
                    local hrp = SPECTATE_TARGET.Character.HumanoidRootPart
                    Camera.CFrame = CFrame.new(hrp.Position + Vector3.new(6, 4, 6), hrp.Position)
                end
            end)
        else
            ToggleBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
            Indicator:TweenPosition(UDim2.new(0, 3, 0, 3), "Out", "Quad", 0.2)
            SpecLabel.Text = "Spectate Player"
            SPECTATE_TARGET = nil

            if SPECTATE_CONNECTION then
                SPECTATE_CONNECTION:Disconnect()
                SPECTATE_CONNECTION = nil
            end
            Camera.CameraType = Enum.CameraType.Custom
        end
    end)

    --// Executor Section
    local ExecutorFrame = Instance.new("Frame")
    ExecutorFrame.Name = "Executor"
    ExecutorFrame.Size = UDim2.new(1, -40, 0, 180)
    ExecutorFrame.Position = UDim2.new(0, 20, 0, 140)
    ExecutorFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 38)
    ExecutorFrame.Parent = MainFrame

    local ExecCorner = Instance.new("UICorner")
    ExecCorner.CornerRadius = UDim.new(0, 10)
    ExecCorner.Parent = ExecutorFrame

    local CodeBox = Instance.new("TextBox")
    CodeBox.Name = "CodeBox"
    CodeBox.Size = UDim2.new(1, -80, 1, -40)
    CodeBox.Position = UDim2.new(0, 10, 0, 10)
    CodeBox.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    CodeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    CodeBox.PlaceholderText = "-- Digite seu código Lua aqui..."
    CodeBox.Text = ""
    CodeBox.MultiLine = true
    CodeBox.TextWrapped = true
    CodeBox.Font = Enum.Font.Code
    CodeBox.TextSize = 13
    CodeBox.TextXAlignment = Enum.TextXAlignment.Left
    CodeBox.TextYAlignment = Enum.TextYAlignment.Top
    CodeBox.Parent = ExecutorFrame

    local BoxCorner = Instance.new("UICorner")
    BoxCorner.CornerRadius = UDim.new(0, 6)
    BoxCorner.Parent = CodeBox

    local ExecBtn = Instance.new("TextButton")
    ExecBtn.Name = "ExecBtn"
    ExecBtn.Size = UDim2.new(0, 60, 0, 28)
    ExecBtn.Position = UDim2.new(1, -70, 1, -35)
    ExecBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 80)
    ExecBtn.Text = "EXEC"
    ExecBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ExecBtn.Font = Enum.Font.GothamBold
    ExecBtn.TextSize = 12
    ExecBtn.Parent = ExecutorFrame

    local ExecBtnCorner = Instance.new("UICorner")
    ExecBtnCorner.CornerRadius = UDim.new(0, 6)
    ExecBtnCorner.Parent = ExecBtn

    --// Executar código
    ExecBtn.MouseButton1Click:Connect(function()
        local code = CodeBox.Text
        if code ~= "" then
            local func, err = loadstring(code)
            if func then
                spawn(function()
                    local success, result = pcall(func)
                    if not success then
                        warn("Erro: " .. tostring(result))
                    end
                end)
                table.insert(EXECUTOR_HISTORY, code)
            else
                warn("Erro de compilação: " .. err)
            end
        end
    end)

    --// Fechar GUI
    CloseBtn.MouseButton1Click:Connect(function()
        GUI_OPEN = false
        local tween = TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.In), {
            Size = UDim2.new(0, 0, 0, 0),
            Position = UDim2.new(0.5, 0, 0.5, 0)
        })
        tween:Play()
        tween.Completed:Wait()
        MainFrame.Visible = false
    end)

    --// Abrir GUI com animação
    local function OpenGUI()
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 0, 0, 0)
        MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)

        local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 500, 0, 380),
            Position = UDim2.new(0.5, -250, 0.5, -190)
        })
        openTween:Play()

        Glow.ImageTransparency = 0.7
        TweenService:Create(Glow, TweenInfo.new(1, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {ImageTransparency = 0.9}):Play()
    end

    --// Tecla INSERT
    UserInputService.InputBegan:Connect(function(input, gp)
        if gp then return end
        if input.KeyCode == Enum.KeyCode.Insert then
            GUI_OPEN = not GUI_OPEN
            if GUI_OPEN then
                OpenGUI()
            else
                CloseBtn.MouseButton1Click:Fire()
            end
        end
    end)

    --// Iniciar
    OpenGUI()
    print("[Forsaken Hub] Carregado com sucesso! Pressione INSERT para abrir/fechar.")
end

--// Iniciar Hub
spawn(function()
    pcall(CreateForsakenHub)
end)
