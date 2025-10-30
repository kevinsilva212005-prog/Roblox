local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false
gui.Name = "ModernHub"

-- Crear el marco principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 180)
frame.Position = UDim2.new(0.3, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
frame.Active = true
frame.Draggable = true

-- Bordes redondeados
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 10)

-- Borde arcoiris (animado)
local gradient = Instance.new("UIStroke", frame)
gradient.Thickness = 3

local rainbow = Instance.new("UIGradient")
rainbow.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 0)),
	ColorSequenceKeypoint.new(0.2, Color3.fromRGB(255, 255, 0)),
	ColorSequenceKeypoint.new(0.4, Color3.fromRGB(0, 255, 0)),
	ColorSequenceKeypoint.new(0.6, Color3.fromRGB(0, 255, 255)),
	ColorSequenceKeypoint.new(0.8, Color3.fromRGB(0, 0, 255)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 255))
}
rainbow.Parent = gradient

-- Animación del borde arcoíris
task.spawn(function()
	while true do
		rainbow.Rotation = rainbow.Rotation + 5
		task.wait(0.05)
	end
end)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.Text = "🌈 HUB Moderno"
title.TextColor3 = Color3.new(1,1,1)
title.TextSize = 22

-- Botón de minimizar
local minimize = Instance.new("TextButton", frame)
minimize.Size = UDim2.new(0, 30, 0, 30)
minimize.Position = UDim2.new(1, -35, 0, 5)
minimize.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
minimize.Font = Enum.Font.SourceSansBold
minimize.Text = "-"
minimize.TextSize = 24
minimize.TextColor3 = Color3.new(1,1,1)
local minimized = false

-- Función para crear botones
local function newButton(name, text, y)
	local b = Instance.new("TextButton", frame)
	b.Name = name
	b.Size = UDim2.new(0, 220, 0, 40)
	b.Position = UDim2.new(0, 15, 0, y)
	b.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
	b.Font = Enum.Font.SourceSansBold
	b.Text = text
	b.TextColor3 = Color3.new(1,1,1)
	b.TextSize = 20
	local c = Instance.new("UICorner", b)
	c.CornerRadius = UDim.new(0,6)
	return b
end

local TP = newButton("TP", "Guardar Posición", 40)
local TP2 = newButton("TP2", "Teletransportar", 90)
local TRAS = newButton("TRAS", "Traspasar Paredes", 140)

-- Variables de funciones
local savedPosition
local noclip = false

-- Guardar posición
TP.MouseButton1Click:Connect(function()
	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:FindFirstChild("HumanoidRootPart")
	if root then
		savedPosition = root.Position
		TP.Text = "Posición Guardada ✔"
		task.wait(1)
		TP.Text = "Guardar Posición"
	end
end)

-- Teletransportar
TP2.MouseButton1Click:Connect(function()
	local char = player.Character or player.CharacterAdded:Wait()
	local root = char:FindFirstChild("HumanoidRootPart")
	if root and savedPosition then
		root.CFrame = CFrame.new(savedPosition)
	end
end)

-- NoClip (traspasar paredes)
TRAS.MouseButton1Click:Connect(function()
	noclip = not noclip
	TRAS.Text = noclip and "NoClip: ON" or "NoClip: OFF"
end)

game:GetService("RunService").Stepped:Connect(function()
	if noclip then
		local char = player.Character
		if char then
			for _, part in ipairs(char:GetDescendants()) do
				if part:IsA("BasePart") then
					part.CanCollide = false
				end
			end
		end
	end
end)

-- Minimizar
minimize.MouseButton1Click:Connect(function()
	minimized = not minimized
	for _, child in ipairs(frame:GetChildren()) do
		if child:IsA("TextButton") and child ~= minimize then
			child.Visible = not minimized
		end
	end
	frame.Size = minimized and UDim2.new(0,250,0,40) or UDim2.new(0,250,0,180)
end)
