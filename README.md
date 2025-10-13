--📍 LocalScript em StarterPlayer > StarterPlayerScripts

-- Serviços
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")
local player = Players.LocalPlayer
local mouse = player:GetMouse()

-- Variáveis
local teleportEnabled = false
local guiVisible = false

-- Criar GUI
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "TeleportMenu"
screenGui.ResetOnSpawn = false

-- Criar Frame principal
local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 200, 0, 120)
frame.Position = UDim2.new(0.5, -100, 0.5, -60)
frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
frame.Visible = false
frame.AnchorPoint = Vector2.new(0.5, 0.5)
frame.ClipsDescendants = true

-- Canto Arredondado
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 12)

-- Botões
local toggleButton = Instance.new("TextButton", frame)
toggleButton.Size = UDim2.new(1, -20, 0, 40)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Text = "Ativar Teleporte"

local closeButton = Instance.new("TextButton", frame)
closeButton.Size = UDim2.new(1, -20, 0, 30)
closeButton.Position = UDim2.new(0, 10, 0, 60)
closeButton.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Text = "Fechar"

-- Estilo botões
for _, btn in pairs({toggleButton, closeButton}) do
	local corner = Instance.new("UICorner", btn)
	corner.CornerRadius = UDim.new(0, 8)
end

-- Função de animação do menu
local function animateFrame(open)
	local goal = {}
	goal.Size = open and UDim2.new(0, 200, 0, 120) or UDim2.new(0, 0, 0, 0)
	local goalTransparency = open and 1 or 0
	local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

	local tween = TweenService:Create(frame, tweenInfo, goal)
	tween:Play()
end

-- Mostrar ou esconder o menu
local function toggleMenu()
	guiVisible = not guiVisible
	frame.Visible = true
	animateFrame(guiVisible)

	if not guiVisible then
		wait(0.3)
		frame.Visible = false
	end
end

-- Evento de tecla G
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if input.KeyCode == Enum.KeyCode.G and not gameProcessed then
		toggleMenu()
	end
end)

-- Evento botão de ativar/desativar
toggleButton.MouseButton1Click:Connect(function()
	teleportEnabled = not teleportEnabled
	toggleButton.Text = teleportEnabled and "Desativar Teleporte" or "Ativar Teleporte"

	-- Animação botão
	local tween = TweenService:Create(toggleButton, TweenInfo.new(0.15), {
		BackgroundColor3 = teleportEnabled and Color3.fromRGB(0, 100, 0) or Color3.fromRGB(50, 50, 50)
	})
	tween:Play()

	-- Fecha o menu após clicar
	wait(0.3)
	toggleMenu()
end)

-- Botão fechar
closeButton.MouseButton1Click:Connect(function()
	toggleMenu()
end)

-- Teleporte ao clicar
mouse.Button1Down:Connect(function()
	if teleportEnabled then
		local targetPos = mouse.Hit.Position
		local char = player.Character
		if char and char:FindFirstChild("HumanoidRootPart") then
			char:MoveTo(targetPos + Vector3.new(0, 3, 0))
		end
	end
end)

-- Anti-AFK
player.Idled:Connect(function()
	VirtualUser:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
	wait(1)
	VirtualUser:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
end)
