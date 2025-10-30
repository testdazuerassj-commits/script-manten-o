-- Forsaken Hub Loading Screen Script for Arceus X
-- Professional Loading Screen - Full Screen Mode

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local StarterGui = game:GetService("StarterGui")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Set Roblox to Fullscreen
StarterGui:SetCore("TopbarEnabled", false)
UserInputService.MouseIconEnabled = false

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ForsakenHubLoader"
screenGui.Parent = playerGui
screenGui.IgnoreGuiInset = true
screenGui.ResetOnSpawn = false

-- Background Frame - Full Screen
local background = Instance.new("Frame")
background.Name = "Background"
background.Parent = screenGui
background.Size = UDim2.new(1, 0, 1, 0)
background.Position = UDim2.new(0, 0, 0, 0)
background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
background.BorderSizePixel = 0

-- Loading Bar Frame
local loadingBarFrame = Instance.new("Frame")
loadingBarFrame.Name = "LoadingBarFrame"
loadingBarFrame.Parent = background
loadingBarFrame.Size = UDim2.new(0.3, 0, 0.02, 0)
loadingBarFrame.Position = UDim2.new(0.35, 0, 0.5, 0)
loadingBarFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
loadingBarFrame.BorderSizePixel = 0

-- Loading Bar
local loadingBar = Instance.new("Frame")
loadingBar.Name = "LoadingBar"
loadingBar.Parent = loadingBarFrame
loadingBar.Size = UDim2.new(0, 0, 1, 0)
loadingBar.Position = UDim2.new(0, 0, 0, 0)
loadingBar.BackgroundColor3 = Color3.fromRGB(0, 162, 255)
loadingBar.BorderSizePixel = 0

-- Corner for Loading Bar
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 5)
corner.Parent = loadingBarFrame

local corner2 = Instance.new("UICorner")
corner2.CornerRadius = UDim.new(0, 5)
corner2.Parent = loadingBar

-- Title Text
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Parent = background
title.Size = UDim2.new(0.5, 0, 0.1, 0)
title.Position = UDim2.new(0.25, 0, 0.2, 0)
title.BackgroundTransparency = 1
title.Text = "Forsaken Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.TextStrokeTransparency = 0
title.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

-- Subtitle Text
local subtitle = Instance.new("TextLabel")
subtitle.Name = "Subtitle"
subtitle.Parent = background
subtitle.Size = UDim2.new(0.5, 0, 0.05, 0)
subtitle.Position = UDim2.new(0.25, 0, 0.32, 0)
subtitle.BackgroundTransparency = 1
subtitle.Text = "Loading..."
subtitle.TextColor3 = Color3.fromRGB(150, 150, 150)
subtitle.TextScaled = true
subtitle.Font = Enum.Font.Gotham

-- Dots Animation
local dots = "..."
local function animateDots()
    local i = 0
    spawn(function()
        while title.Parent do
            i = i + 1
            if i > 3 then i = 1 end
            subtitle.Text = "Loading" .. string.rep(".", i)
            wait(0.5)
        end
    end)
end
animateDots()

-- Loading Animation
local tweenInfo = TweenInfo.new(2, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, -1, true)
local tween = TweenService:Create(loadingBar, tweenInfo, {Size = UDim2.new(1, 0, 1, 0)})
tween:Play()

-- Fade Out After Loading (Simulate 5 seconds load)
wait(5)
local fadeInfo = TweenInfo.new(1, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
local fadeTween = TweenService:Create(background, fadeInfo, {BackgroundTransparency = 1})
fadeTween:Play()

fadeTween.Completed:Connect(function()
    -- Optionally load the actual hub here
    -- For now, just clean up
    screenGui:Destroy()
    -- Re-enable topbar if needed
    StarterGui:SetCore("TopbarEnabled", true)
    UserInputService.MouseIconEnabled = true
end)
