-- Forsaken Hub Script for Arceus X (Roblox)
-- Features: Animated opening menu, Draggable, Player Spectate with Toggle, Large Code Executor with Clear

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Create ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ForsakenHub"
ScreenGui.Parent = PlayerGui
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Main Frame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
MainFrame.Size = UDim2.new(0, 400, 0, 300)
MainFrame.Visible = false

-- Corner for MainFrame
local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 8)
Corner.Parent = MainFrame

-- Title Label
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

-- Draggable Functionality
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

-- Spectate Section
local SpectateFrame = Instance.new("Frame")
SpectateFrame.Name = "SpectateFrame"
SpectateFrame.Parent = MainFrame
SpectateFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
SpectateFrame.BorderSizePixel = 0
SpectateFrame.Position = UDim2.new(0, 10, 0, 50)
SpectateFrame.Size = UDim2.new(1, -20, 0, 60)

local SpectateCorner = Instance.new("UICorner")
SpectateCorner.CornerRadius = UDim.new(0, 4)
SpectateCorner.Parent = SpectateFrame

local SpectateLabel = Instance.new("TextLabel")
SpectateLabel.Name = "SpectateLabel"
SpectateLabel.Parent = SpectateFrame
SpectateLabel.BackgroundTransparency = 1
SpectateLabel.Position = UDim2.new(0, 10, 0, 5)
SpectateLabel.Size = UDim2.new(0.6, 0, 0, 20)
SpectateLabel.Font = Enum.Font.Gotham
SpectateLabel.Text = "Spectate Player:"
SpectateLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
SpectateLabel.TextSize = 14
SpectateLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Player Dropdown
local PlayerDropdown = Instance.new("TextButton")
PlayerDropdown.Name = "PlayerDropdown"
PlayerDropdown.Parent = SpectateFrame
PlayerDropdown.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
PlayerDropdown.BorderSizePixel = 0
PlayerDropdown.Position = UDim2.new(0, 10, 0, 30)
PlayerDropdown.Size = UDim2.new(0.6, 0, 0, 25)
PlayerDropdown.Font = Enum.Font.Gotham
PlayerDropdown.Text = "Select Player"
PlayerDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
PlayerDropdown.TextSize = 12

local DropdownCorner = Instance.new("UICorner")
DropdownCorner.CornerRadius = UDim.new(0, 4)
DropdownCorner.Parent = PlayerDropdown

local selectedPlayer = nil

local function updatePlayerList()
    local playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(playerList, player.Name)
        end
    end
    table.sort(playerList)
    -- For simplicity, we'll set text to current selection; in full impl, use a scrolling frame for dropdown
    PlayerDropdown.Text = selectedPlayer and selectedPlayer.Name or "Select Player"
end

PlayerDropdown.MouseButton1Click:Connect(function()
    -- Simple toggle for demo; in full, open dropdown list
    local players = Players:GetPlayers()
    if #players > 1 then
        selectedPlayer = players[math.random(2, #players)]  -- Random for demo; replace with actual selection
        updatePlayerList()
    end
end)

Players.PlayerAdded:Connect(updatePlayerList)
Players.PlayerRemoving:Connect(updatePlayerList)

-- Toggle Switch for Spectate
local ToggleFrame = Instance.new("Frame")
ToggleFrame.Name = "ToggleFrame"
ToggleFrame.Parent = SpectateFrame
ToggleFrame.BackgroundColor3 = Color3.fromRGB(55, 55, 55)
ToggleFrame.BorderSizePixel = 0
ToggleFrame.Position = UDim2.new(0.7, 0, 0, 30)
ToggleFrame.Size = UDim2.new(0, 50, 0, 25)

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 12)
ToggleCorner.Parent = ToggleFrame

local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Parent = ToggleFrame
ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
ToggleButton.BorderSizePixel = 0
ToggleButton.Position = UDim2.new(0, 2, 0, 2)
ToggleButton.Size = UDim2.new(0.48, 0, 1, 0)
ToggleButton.Font = Enum.Font.Gotham
ToggleButton.Text = ""
ToggleButton.TextColor3 = Color3.fromRGB(0, 0, 0)

local ToggleButtonCorner = Instance.new("UICorner")
ToggleButtonCorner.CornerRadius = UDim.new(0, 10)
ToggleButtonCorner.Parent = ToggleButton

local isSpectating = false

ToggleButton.MouseButton1Click:Connect(function()
    isSpectating = not isSpectating
    if isSpectating then
        ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        if selectedPlayer and selectedPlayer.Character then
            Camera.CameraSubject = selectedPlayer.Character:FindFirstChild("Humanoid")
        end
    else
        ToggleButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        Camera.CameraSubject = LocalPlayer.Character:FindFirstChild("Humanoid")
    end
end)

-- Executor Section (Large TextBox)
local ExecutorFrame = Instance.new("Frame")
ExecutorFrame.Name = "ExecutorFrame"
ExecutorFrame.Parent = MainFrame
ExecutorFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
ExecutorFrame.BorderSizePixel = 0
ExecutorFrame.Position = UDim2.new(0, 10, 0, 120)
ExecutorFrame.Size = UDim2.new(1, -20, 0, 140)

local ExecutorCorner = Instance.new("UICorner")
ExecutorCorner.CornerRadius = UDim.new(0, 4)
ExecutorCorner.Parent = ExecutorFrame

local CodeBox = Instance.new("TextBox")
CodeBox.Name = "CodeBox"
CodeBox.Parent = ExecutorFrame
CodeBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
CodeBox.BorderSizePixel = 0
CodeBox.Position = UDim2.new(0, 5, 0, 5)
CodeBox.Size = UDim2.new(1, -10, 1, -10)
CodeBox.Font = Enum.Font.Code
CodeBox.Text = "-- Enter your code here\nprint('Hello from Forsaken Hub!')"
CodeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
CodeBox.TextSize = 12
CodeBox.TextXAlignment = Enum.TextXAlignment.Left
CodeBox.TextYAlignment = Enum.TextYAlignment.Top
CodeBox.MultiLine = true
CodeBox.ClearTextOnFocus = false

local CodeBoxCorner = Instance.new("UICorner")
CodeBoxCorner.CornerRadius = UDim.new(0, 4)
CodeBoxCorner.Parent = CodeBox

-- Execute Button (Assumed needed for executor)
local ExecuteButton = Instance.new("TextButton")
ExecuteButton.Name = "ExecuteButton"
ExecuteButton.Parent = ExecutorFrame
ExecuteButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
ExecuteButton.BorderSizePixel = 0
ExecuteButton.Position = UDim2.new(0, 5, 1, -30)
ExecuteButton.Size = UDim2.new(0.48, 0, 0, 25)
ExecuteButton.Font = Enum.Font.GothamBold
ExecuteButton.Text = "Execute"
ExecuteButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ExecuteButton.TextSize = 12

local ExecuteCorner = Instance.new("UICorner")
ExecuteCorner.CornerRadius = UDim.new(0, 4)
ExecuteCorner.Parent = ExecuteButton

ExecuteButton.MouseButton1Click:Connect(function()
    local success, err = pcall(function()
        loadstring(CodeBox.Text)()
    end)
    if not success then
        warn("Execution Error: " .. tostring(err))
    end
end)

-- Clear Button
local ClearButton = Instance.new("TextButton")
ClearButton.Name = "ClearButton"
ClearButton.Parent = ExecutorFrame
ClearButton.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
ClearButton.BorderSizePixel = 0
ClearButton.Position = UDim2.new(0.52, 0, 1, -30)
ClearButton.Size = UDim2.new(0.48, 0, 0, 25)
ClearButton.Font = Enum.Font.GothamBold
ClearButton.Text = "Clear"
ClearButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ClearButton.TextSize = 12

local ClearCorner = Instance.new("UICorner")
ClearCorner.CornerRadius = UDim.new(0, 4)
ClearCorner.Parent = ClearButton

ClearButton.MouseButton1Click:Connect(function()
    CodeBox.Text = ""
end)

-- Opening Animation
local openTween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Position = UDim2.new(0.5, -200, 0.5, -150),
    Size = UDim2.new(0, 400, 0, 300),
    BackgroundTransparency = 0
})

local closeTween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
    Position = UDim2.new(0.5, -200, -1, 0),
    BackgroundTransparency = 1
})

MainFrame.BackgroundTransparency = 1
MainFrame.Position = UDim2.new(0.5, -200, -1, 0)
MainFrame.Size = UDim2.new(0, 0, 0, 0)
MainFrame.Visible = true

-- Start Animation
openTween:Play()

-- Close Button (Optional, add to title bar)
local CloseButton = Instance.new("TextButton")
CloseButton.Name = "CloseButton"
CloseButton.Parent = Title
CloseButton.BackgroundTransparency = 1
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.Size = UDim2.new(0, 30, 1, 0)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseButton.TextSize = 16

CloseButton.MouseButton1Click:Connect(function()
    closeTween:Play()
    closeTween.Completed:Connect(function()
        ScreenGui:Destroy()
    end)
end)

-- Update player list initially
updatePlayerList()

print("Forsaken Hub loaded successfully!")
