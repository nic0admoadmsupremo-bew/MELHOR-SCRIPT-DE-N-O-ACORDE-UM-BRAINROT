-- LocalScript

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local teleportEnabled = true
local menuVisible = true

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TeleportGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Main Frame
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 320, 0, 180)
frame.Position = UDim2.new(0.5, -160, 0.5, -90)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.Parent = screenGui
frame.Active = true
frame.Draggable = true

-- Rounded corners
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 12)

-- Drop shadow
local shadow = Instance.new("ImageLabel")
shadow.Name = "Shadow"
shadow.Parent = frame
shadow.BackgroundTransparency = 1
shadow.Size = UDim2.new(1, 30, 1, 30)
shadow.Position = UDim2.new(0, -15, 0, -15)
shadow.Image = "rbxassetid://1316045217"
shadow.ImageTransparency = 0.6
shadow.ScaleType = Enum.ScaleType.Slice
shadow.SliceCenter = Rect.new(10, 10, 118, 118)

-- Title
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0.2, 0)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "Teleport Script [By: Lydaidugrau]"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.Parent = frame

-- Toggle Button
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0.8, 0, 0.25, 0)
toggleButton.Position = UDim2.new(0.1, 0, 0.35, 0)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Font = Enum.Font.Gotham
toggleButton.TextSize = 14
toggleButton.Text = "Desativar Teleporte"
toggleButton.Parent = frame
Instance.new("UICorner", toggleButton).CornerRadius = UDim.new(0, 8)

-- Close Button
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0.8, 0, 0.25, 0)
closeButton.Position = UDim2.new(0.1, 0, 0.65, 0)
closeButton.BackgroundColor3 = Color3.fromRGB(180, 50, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.Gotham
closeButton.TextSize = 14
closeButton.Text = "Fechar Menu"
closeButton.Parent = frame
Instance.new("UICorner", closeButton).CornerRadius = UDim.new(0, 8)

-- Função de teleporte
mouse.Button1Down:Connect(function()
	if teleportEnabled and not UserInputService:GetFocusedTextBox() then
		local target = mouse.Hit
		if target then
			local character = player.Character
			if character and character:FindFirstChild("HumanoidRootPart") then
				character:MoveTo(target.Position)
			end
		end
	end
end)

-- Alternar teleporte
toggleButton.MouseButton1Click:Connect(function()
	teleportEnabled = not teleportEnabled
	if teleportEnabled then
		toggleButton.Text = "Desativar Teleporte"
		toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
	else
		toggleButton.Text = "Ativar Teleporte"
		toggleButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
	end
end)

-- Função de animação para fechar o menu
local function animateClose()
	local tween = TweenService:Create(frame, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {
		Size = UDim2.new(0, 0, 0, 0),
		Position = UDim2.new(0.5, 0, 0.5, 0)
	})
	tween:Play()
	tween.Completed:Wait()
	frame.Visible = false
	menuVisible = false
end

-- Função de animação para abrir o menu
local function animateOpen()
	frame.Size = UDim2.new(0, 0, 0, 0)
	frame.Position = UDim2.new(0.5, 0, 0.5, 0)
	frame.Visible = true
	local tween = TweenService:Create(frame, TweenInfo.new(0.3, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {
		Size = UDim2.new(0, 320, 0, 180),
		Position = UDim2.new(0.5, -160, 0.5, -90)
	})
	tween:Play()
	menuVisible = true
end

-- Fechar menu
closeButton.MouseButton1Click:Connect(function()
	animateClose()
end)

-- Tecla "G" para reabrir
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.G then
		if not menuVisible then
			animateOpen()
		end
	end
end)
