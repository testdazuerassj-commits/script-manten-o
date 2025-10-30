-- Forsaken Hub Loading Screen Script for Arceus X (Roblox)
-- Professional loading screen with fullscreen overlay

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Create ScreenGui (use CoreGui if exploit allows, otherwise PlayerGui)
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ForsakenHubLoader"
screenGui.Parent = gethui and gethui() or playerGui  -- Arceus X compatibility: use gethui() if available
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

-- Fullscreen background frame
local loadingFrame = Instance.new("Frame")
loadingFrame.Name = "LoadingFrame"
loadingFrame.Size = UDim2.new(1, 0, 1, 0)
loadingFrame.Position = UDim2.new(0, 0, 0, 0)
loadingFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
loadingFrame.BackgroundTransparency = 0
loadingFrame.BorderSizePixel = 0
loadingFrame.Parent = screenGui

-- Add subtle gradient for professional look
local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(10, 10, 20)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 0, 0))
}
gradient.Rotation = 90
gradient.Parent = loadingFrame

-- Loading title text
local titleText = Instance.new("TextLabel")
titleText.Name = "TitleText"
titleText.Size = UDim2.new(0.8, 0, 0.1, 0)
titleText.Position = UDim2.new(0.1, 0, 0.2, 0)
titleText.BackgroundTransparency = 1
titleText.Text = "Forsaken Hub"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextScaled = true
titleText.Font = Enum.Font.GothamBold
titleText.Parent = loadingFrame

-- Subtitle text
local subtitleText = Instance.new("TextLabel")
subtitleText.Name = "SubtitleText"
subtitleText.Size = UDim2.new(0.8, 0, 0.05, 0)
subtitleText.Position = UDim2.new(0.1, 0, 0.3, 0)
subtitleText.BackgroundTransparency = 1
subtitleText.Text = "Loading Professional Scripts..."
subtitleText.TextColor3 = Color3.fromRGB(200, 200, 200)
subtitleText.TextScaled = true
subtitleText.Font = Enum.Font.Gotham
subtitleText.Parent = loadingFrame

-- Loading bar background
local barBackground = Instance.new("Frame")
barBackground.Name = "BarBackground"
barBackground.Size = UDim2.new(0.6, 0, 0.02, 0)
barBackground.Position = UDim2.new(0.2, 0, 0.5, 0)
barBackground.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
barBackground.BorderSizePixel = 0
barBackground.Parent = loadingFrame

local barCorner = Instance.new("UICorner")
barCorner.CornerRadius = UDim.new(0, 4)
barCorner.Parent = barBackground

-- Loading bar fill
local loadingBar = Instance.new("Frame")
loadingBar.Name = "LoadingBar"
loadingBar.Size = UDim2.new(0, 0, 1, 0)
loadingBar.Position = UDim2.new(0, 0, 0, 0)
loadingBar.BackgroundColor3 = Color3.fromRGB(0, 162, 255)  -- Blue accent for professional feel
loadingBar.BorderSizePixel = 0
loadingBar.Parent = barBackground

local barFillCorner = Instance.new("UICorner")
barFillCorner.CornerRadius = UDim.new(0, 4)
barFillCorner.Parent = loadingBar

-- Progress text below bar
local progressText = Instance.new("TextLabel")
progressText.Name = "ProgressText"
progressText.Size = UDim2.new(0.6, 0, 0.05, 0)
progressText.Position = UDim2.new(0.2, 0, 0.53, 0)
progressText.BackgroundTransparency = 1
progressText.Text = "0%"
progressText.TextColor3 = Color3.fromRGB(255, 255, 255)
progressText.TextScaled = true
progressText.Font = Enum.Font.Gotham
progressText.Parent = loadingFrame

-- Animate loading bar with progress update
local duration = 5  -- 5 seconds loading
local startTime = tick()

local connection
connection = RunService.Heartbeat:Connect(function()
    local elapsed = tick() - startTime
    local progress = math.min(elapsed / duration, 1)
    
    loadingBar.Size = UDim2.new(progress, 0, 1, 0)
    progressText.Text = math.floor(progress * 100) .. "%"
    
    if progress >= 1 then
        connection:Disconnect()
        
        -- Fade out after completion
        local fadeTween = TweenService:Create(loadingFrame, TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency = 1})
        fadeTween:Play()
        
        fadeTween.Completed:Connect(function()
            -- Here you can load the actual hub scripts
            -- For now, just destroy the loader
            screenGui:Destroy()
            print("Forsaken Hub loaded successfully!")  -- Console message
        end)
    end
end)

-- Optional: Pulse animation on title for flair
local pulseTween = TweenService:Create(titleText, TweenInfo.new(2, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {TextTransparency = 0.2})
pulseTween:Play()

-- Force fullscreen if possible (Roblox client setting, may require exploit features)
if setfpscap then setfpscap(60) end  -- Optional: Cap FPS for smoothness
