-- F O R S A K E N   H U B  (Mobile)
-- By test123_DAZUERA

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

----------------------------------------------------
-- TELA DE GUI
----------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ForsakenHubLoader"
screenGui.Parent = gethui and gethui() or playerGui
screenGui.IgnoreGuiInset = true
screenGui.ResetOnSpawn = false

local loadingFrame = Instance.new("Frame")
loadingFrame.Size = UDim2.new(1, 0, 1, 0)
loadingFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
loadingFrame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.1, 0)
title.Position = UDim2.new(0, 0, 0.3, 0)
title.BackgroundTransparency = 1
title.Text = "Forsaken Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = loadingFrame

local bar = Instance.new("Frame")
bar.Size = UDim2.new(0.6, 0, 0.02, 0)
bar.Position = UDim2.new(0.2, 0, 0.5, 0)
bar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
bar.BorderSizePixel = 0
bar.Parent = loadingFrame

local fill = Instance.new("Frame")
fill.Size = UDim2.new(0, 0, 1, 0)
fill.BackgroundColor3 = Color3.fromRGB(0, 162, 255)
fill.Parent = bar

local progress = Instance.new("TextLabel")
progress.Size = UDim2.new(1, 0, 0.05, 0)
progress.Position = UDim2.new(0, 0, 0.55, 0)
progress.BackgroundTransparency = 1
progress.Text = "0%"
progress.TextColor3 = Color3.fromRGB(255, 255, 255)
progress.TextScaled = true
progress.Font = Enum.Font.Gotham
progress.Parent = loadingFrame

local startTime = tick()
local duration = 5
local conn

conn = RunService.Heartbeat:Connect(function()
	local elapsed = tick() - startTime
	local p = math.min(elapsed / duration, 1)
	fill.Size = UDim2.new(p, 0, 1, 0)
	progress.Text = math.floor(p * 100) .. "%"
	if p >= 1 then
		conn:Disconnect()
		local fade = TweenService:Create(loadingFrame, TweenInfo.new(1), {BackgroundTransparency = 1})
		fade:Play()
		fade.Completed:Connect(function()
			screenGui:Destroy()
			_G.ForsakenLoaded = true
		end)
	end
end)

----------------------------------------------------
-- UI signficamento de gui obvio
----------------------------------------------------
local gui = Instance.new("ScreenGui")
gui.Name = "ForsakenHub"
gui.Parent = playerGui
gui.Enabled = false
gui.ResetOnSpawn = false

local main = Instance.new("Frame")
main.Size = UDim2.new(0, 400, 0, 300)
main.Position = UDim2.new(0.5, -200, 0.5, -150)
main.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
main.BorderSizePixel = 0
main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = main

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, 0, 0, 40)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "Forsaken Hub"
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 18
titleLabel.Parent = main

----------------------------------------------------
-- DRAG MODE (WORKS ON TOUCH AND MOUSE)
----------------------------------------------------
local dragging = false
local dragStart, startPos

local function updateDrag(input)
	local delta = input.Position - dragStart
	main.Position = UDim2.new(
		startPos.X.Scale,
		startPos.X.Offset + delta.X,
		startPos.Y.Scale,
		startPos.Y.Offset + delta.Y
	)
end

local function startDragging(input)
	dragging = true
	dragStart = input.Position
	startPos = main.Position

	local conn1, conn2
	conn1 = input.Changed:Connect(function()
		if input.UserInputState == Enum.UserInputState.End then
			dragging = false
			conn1:Disconnect()
			if conn2 then conn2:Disconnect() end
		end
	end)

	conn2 = UserInputService.InputChanged:Connect(function(changed)
		if dragging and (changed.UserInputType == Enum.UserInputType.MouseMovement or changed.UserInputType == Enum.UserInputType.Touch) then
			updateDrag(changed)
		end
	end)
end

main.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		startDragging(input)
	end
end)

----------------------------------------------------
-- EXECUTOR
----------------------------------------------------
local execFrame = Instance.new("Frame")
execFrame.Size = UDim2.new(1, -20, 1, -60)
execFrame.Position = UDim2.new(0, 10, 0, 50)
execFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
execFrame.BorderSizePixel = 0
execFrame.Parent = main

local execCorner = Instance.new("UICorner")
execCorner.CornerRadius = UDim.new(0, 8)
execCorner.Parent = execFrame

local box = Instance.new("TextBox")
box.Size = UDim2.new(1, -10, 1, -40)
box.Position = UDim2.new(0, 5, 0, 5)
box.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
box.TextColor3 = Color3.fromRGB(255, 255, 255)
box.TextSize = 12
box.Font = Enum.Font.Code
box.MultiLine = true
box.ClearTextOnFocus = false
box.Text = "-- Enter your code here\nprint('Hello from Forsaken Hub!')"
box.Parent = execFrame

local runBtn = Instance.new("TextButton")
runBtn.Size = UDim2.new(0.48, 0, 0, 25)
runBtn.Position = UDim2.new(0, 5, 1, -30)
runBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
runBtn.Text = "Execute"
runBtn.Font = Enum.Font.GothamBold
runBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
runBtn.TextSize = 12
runBtn.Parent = execFrame

runBtn.MouseButton1Click:Connect(function()
	local s, e = pcall(function()
		loadstring(box.Text)()
	end)
	if not s then warn(e) end
end)

local clearBtn = Instance.new("TextButton")
clearBtn.Size = UDim2.new(0.48, 0, 0, 25)
clearBtn.Position = UDim2.new(0.52, 0, 1, -30)
clearBtn.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
clearBtn.Text = "Clear"
clearBtn.Font = Enum.Font.GothamBold
clearBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
clearBtn.TextSize = 12
clearBtn.Parent = execFrame

clearBtn.MouseButton1Click:Connect(function()
	box.Text = ""
end)

----------------------------------------------------
-- PRA FINALIZAR um acesso de função
----------------------------------------------------
task.spawn(function()
	repeat task.wait() until _G.ForsakenLoaded
	gui.Enabled = true
end)

print("Forsaken hub esta executado, aproveite feito pelo usuario test123_DAZUERA")
