
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Create ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ForsakenHub"
ScreenGui.Parent = PlayerGui
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Colors Theme (Dark Modern)
local Colors = {
    Primary = Color3.fromRGB(25, 25, 35),
    Secondary = Color3.fromRGB(45, 45, 55),
    Accent = Color3.fromRGB(100, 150, 255),
    Text = Color3.fromRGB(255, 255, 255),
    Danger = Color3.fromRGB(255, 100, 100),
    Success = Color3.fromRGB(100, 255, 100),
    Warning = Color3.fromRGB(255, 200, 100)
}

-- Loading Screen
local LoadingFrame = Instance.new("Frame")
LoadingFrame.Name = "LoadingFrame"
LoadingFrame.Size = UDim2.new(1, 0, 1, 0)
LoadingFrame.Position = UDim2.new(0, 0, 0, 0)
LoadingFrame.BackgroundColor3 = Colors.Primary
LoadingFrame.BorderSizePixel = 0
LoadingFrame.Parent = ScreenGui

local LoadingTitle = Instance.new("TextLabel")
LoadingTitle.Name = "LoadingTitle"
LoadingTitle.Size = UDim2.new(0.3, 0, 0.1, 0)
LoadingTitle.Position = UDim2.new(0.35, 0, 0.3, 0)
LoadingTitle.BackgroundTransparency = 1
LoadingTitle.Text = "Forsaken Hub - Loading..."
LoadingTitle.TextColor3 = Colors.Text
LoadingTitle.TextScaled = true
LoadingTitle.Font = Enum.Font.GothamBold
LoadingTitle.Parent = LoadingFrame

local ProgressBarFrame = Instance.new("Frame")
ProgressBarFrame.Name = "ProgressBarFrame"
ProgressBarFrame.Size = UDim2.new(0.4, 0, 0.05, 0)
ProgressBarFrame.Position = UDim2.new(0.3, 0, 0.45, 0)
ProgressBarFrame.BackgroundColor3 = Colors.Secondary
ProgressBarFrame.BorderSizePixel = 0
ProgressBarFrame.Parent = LoadingFrame

local ProgressBar = Instance.new("Frame")
ProgressBar.Name = "ProgressBar"
ProgressBar.Size = UDim2.new(0, 0, 1, 0)
ProgressBar.Position = UDim2.new(0, 0, 0, 0)
ProgressBar.BackgroundColor3 = Colors.Accent
ProgressBar.BorderSizePixel = 0
ProgressBar.Parent = ProgressBarFrame

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 8)
Corner.Parent = ProgressBarFrame
Corner.Parent = ProgressBar

-- Simulate Loading
local loadingTween = TweenService:Create(ProgressBar, TweenInfo.new(2.5, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {Size = UDim2.new(1, 0, 1, 0)})
loadingTween:Play()
loadingTween.Completed:Connect(function()
    wait(0.5)
    local fadeOut = TweenService:Create(LoadingFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quad), {BackgroundTransparency = 1})
    fadeOut:Play()
    fadeOut.Completed:Connect(function()
        LoadingFrame:Destroy()
        -- Open Main Menu
        CreateMainGUI()
    end)
end)
