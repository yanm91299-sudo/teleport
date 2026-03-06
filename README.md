local player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local enabled = false
local mode = "Normal"

-- GUI
local gui = Instance.new("ScreenGui")
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- BOTAO CENTRAL
local main = Instance.new("TextButton")
main.Size = UDim2.new(0,120,0,35)
main.Position = UDim2.new(0.5,-60,0.5,-17)
main.BackgroundColor3 = Color3.fromRGB(30,30,30)
main.TextColor3 = Color3.new(1,1,1)
main.Text = "UI MENU"
main.Active = true
main.Draggable = true
main.Parent = gui

-- FRAME 1 (ativar)
local frame1 = Instance.new("Frame")
frame1.Size = UDim2.new(0,160,0,50)
frame1.Position = UDim2.new(0.4,-80,0.7,0)
frame1.BackgroundColor3 = Color3.fromRGB(0,0,0)
frame1.BackgroundTransparency = 0.3
frame1.Visible = true
frame1.Active = true
frame1.Draggable = true
frame1.Parent = gui

local toggle = Instance.new("TextButton")
toggle.Size = UDim2.new(1,0,1,0)
toggle.Text = "Teleport Dodge: OFF"
toggle.BackgroundTransparency = 1
toggle.TextColor3 = Color3.new(1,1,1)
toggle.Parent = frame1

-- FRAME 2 (modo)
local frame2 = Instance.new("Frame")
frame2.Size = UDim2.new(0,160,0,50)
frame2.Position = UDim2.new(0.6,-80,0.7,0)
frame2.BackgroundColor3 = Color3.fromRGB(0,0,0)
frame2.BackgroundTransparency = 0.3
frame2.Visible = true
frame2.Active = true
frame2.Draggable = true
frame2.Parent = gui

local modeBtn = Instance.new("TextButton")
modeBtn.Size = UDim2.new(1,0,1,0)
modeBtn.Text = "Mode: Normal"
modeBtn.BackgroundTransparency = 1
modeBtn.TextColor3 = Color3.new(1,1,1)
modeBtn.Parent = frame2

-- animação
local function animate(frame, open)
	local goal = {}
	if open then
		goal.Size = UDim2.new(0,160,0,50)
	else
		goal.Size = UDim2.new(0,0,0,0)
	end
	
	local tween = TweenService:Create(frame,TweenInfo.new(0.3),goal)
	tween:Play()
end

local open = true
main.MouseButton1Click:Connect(function()
	open = not open
	animate(frame1,open)
	animate(frame2,open)
end)

-- ativar script
toggle.MouseButton1Click:Connect(function()
	enabled = not enabled
	
	if enabled then
		toggle.Text = "Teleport Dodge: ON"
	else
		toggle.Text = "Teleport Dodge: OFF"
	end
end)

-- mudar modo
modeBtn.MouseButton1Click:Connect(function()
	if mode == "Normal" then
		mode = "Medium"
		modeBtn.Text = "Mode: Medium"
	else
		mode = "Normal"
		modeBtn.Text = "Mode: Normal"
	end
end)

-- sistema de personagem
local function setupCharacter(char)
	local humanoid = char:WaitForChild("Humanoid")
	local root = char:WaitForChild("HumanoidRootPart")
	
	local lastHealth = humanoid.Health
	
	humanoid.HealthChanged:Connect(function(h)
		if enabled and h < lastHealth then
			
			local dist = 8
			if mode == "Medium" then
				dist = 16
			end
			
			local directions = {
				Vector3.new(dist,0,0),
				Vector3.new(-dist,0,0),
				Vector3.new(0,0,dist),
				Vector3.new(0,0,-dist),
				Vector3.new(dist,0,dist),
				Vector3.new(-dist,0,-dist),
				Vector3.new(-dist,0,dist),
				Vector3.new(dist,0,-dist)
			}
			
			local dir = directions[math.random(1,#directions)]
			root.CFrame = root.CFrame + dir
		end
		
		lastHealth = h
	end)
end

if player.Character then
	setupCharacter(player.Character)
end

player.CharacterAdded:Connect(setupCharacter)
