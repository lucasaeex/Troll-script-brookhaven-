--lucas local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 160, 0, 180)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.Active = true
frame.Draggable = true

local function criarBotao(texto, cor, y)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(1, 0, 0, 40)
	btn.Position = UDim2.new(0, 0, 0, y)
	btn.Text = texto
	btn.BackgroundColor3 = cor
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.SourceSansBold
	btn.TextSize = 18
	return btn
end

-- Botões
local killBtn = criarBotao("KILL", Color3.fromRGB(200, 0, 0), 0)
local flyBtn = criarBotao("FLY", Color3.fromRGB(0, 180, 0), 50)
local disableBtn = criarBotao("DESABILITAR", Color3.fromRGB(180, 0, 0), 100)

-- Função de KILL
killBtn.MouseButton1Click:Connect(function()
	for _, p in pairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Humanoid") then
			p.Character.Humanoid.Health = 0
		end
	end
end)

-- Funções de FLY
local flying = false
local flyspeed = 50
local vel = nil
local bodyGyro = nil

function startFly()
	local char = LocalPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return end

	flying = true

	vel = Instance.new("BodyVelocity", char.HumanoidRootPart)
	vel.Velocity = Vector3.new(0,0,0)
	vel.MaxForce = Vector3.new(1e5, 1e5, 1e5)

	bodyGyro = Instance.new("BodyGyro", char.HumanoidRootPart)
	bodyGyro.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
	bodyGyro.CFrame = char.HumanoidRootPart.CFrame

	RunService.RenderStepped:Connect(function()
		if flying then
			local cam = workspace.CurrentCamera
			local moveVec = Vector3.new()

			if UIS:IsKeyDown(Enum.KeyCode.W) then moveVec = moveVec + cam.CFrame.LookVector end
			if UIS:IsKeyDown(Enum.KeyCode.S) then moveVec = moveVec - cam.CFrame.LookVector end
			if UIS:IsKeyDown(Enum.KeyCode.A) then moveVec = moveVec - cam.CFrame.RightVector end
			if UIS:IsKeyDown(Enum.KeyCode.D) then moveVec = moveVec + cam.CFrame.RightVector end
			if UIS:IsKeyDown(Enum.KeyCode.Space) then moveVec = moveVec + Vector3.new(0,1,0) end
			if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then moveVec = moveVec - Vector3.new(0,1,0) end

			vel.Velocity = moveVec.Unit * flyspeed
			bodyGyro.CFrame = cam.CFrame
		end
	end)
end

function stopFly()
	flying = false
	if vel then vel:Destroy() end
	if bodyGyro then bodyGyro:Destroy() end
end

flyBtn.MouseButton1Click:Connect(startFly)
disableBtn.MouseButton1Click:Connect(stopFly)

print("script deu certo!!")
