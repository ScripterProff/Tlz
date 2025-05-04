local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Interface
local gui = Instance.new("ScreenGui")
gui.Name = "ESP_UI"
gui.ResetOnSpawn = false
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 120, 0, 40)
button.Position = UDim2.new(0, 20, 0, 100)
button.Text = "Ativar ESP"
button.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
button.TextColor3 = Color3.new(1, 1, 1)
button.Parent = gui

-- Controle
local ESPEnabled = false
local ESPTrackers = {}

-- Criar ESP visual
local function AddESP(player)
	if player == LocalPlayer then return end
	local esp = Instance.new("BillboardGui")
	esp.Name = "ESP_" .. player.Name
	esp.Adornee = nil
	esp.Size = UDim2.new(4, 0, 5.5, 0)
	esp.StudsOffset = Vector3.new(0, 2.5, 0)
	esp.AlwaysOnTop = true
	esp.Enabled = false
	esp.Parent = game.CoreGui

	local frame = Instance.new("Frame")
	frame.Size = UDim2.new(1, 0, 1, 0)
	frame.BackgroundTransparency = 1
	frame.BorderSizePixel = 0
	frame.Parent = esp

	local box = Instance.new("Frame")
	box.AnchorPoint = Vector2.new(0.5, 0.5)
	box.Position = UDim2.new(0.5, 0, 0.5, 0)
	box.Size = UDim2.new(1, 0, 1, 0)
	box.BackgroundTransparency = 1
	box.BorderSizePixel = 2
	box.BorderColor3 = Color3.fromRGB(255, 0, 0)
	box.Parent = frame

	local nameLabel = Instance.new("TextLabel")
	nameLabel.Size = UDim2.new(1, 0, 0, 20)
	nameLabel.Position = UDim2.new(0, 0, -0.25, 0)
	nameLabel.BackgroundTransparency = 1
	nameLabel.Text = player.Name
	nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
	nameLabel.TextStrokeTransparency = 0.5
	nameLabel.Font = Enum.Font.SourceSansBold
	nameLabel.TextSize = 14
	nameLabel.Parent = frame

	ESPTrackers[player] = esp

	local function Update()
		while player.Parent and esp do
			task.wait(0.1)
			local char = player.Character
			if ESPEnabled and char and char:FindFirstChild("HumanoidRootPart") and player.Team ~= LocalPlayer.Team then
				esp.Adornee = char:FindFirstChild("HumanoidRootPart")
				esp.Enabled = true
			else
				esp.Enabled = false
			end
		end
	end

	coroutine.wrap(Update)()
end

-- Remover ESP
local function RemoveESP(player)
	if ESPTrackers[player] then
		ESPTrackers[player]:Destroy()
		ESPTrackers[player] = nil
	end
end

-- Botão toggle
button.MouseButton1Click:Connect(function()
	ESPEnabled = not ESPEnabled
	button.Text = ESPEnabled and "Desativar ESP" or "Ativar ESP"
end)

-- Monitorar jogadores
for _, player in ipairs(Players:GetPlayers()) do
	AddESP(player)
end

Players.PlayerAdded:Connect(AddESP)
Players.PlayerRemoving:Connect(RemoveESP)
