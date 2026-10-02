-- Services
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer

-- Create Main ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TeleportGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-------------------------------------------------------------------
-- 1. THÔNG BÁO INTRO CỦA RAYFIELD
-------------------------------------------------------------------
local function showRayfieldNotify()
	local notifyFrame = Instance.new("Frame")
	notifyFrame.Name = "RayfieldNotification"
	notifyFrame.Size = UDim2.new(0, 260, 0, 75)
	notifyFrame.Position = UDim2.new(1, 300, 1, -95)
	notifyFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
	notifyFrame.BorderSizePixel = 0
	notifyFrame.ClipsDescendants = true
	notifyFrame.Parent = ScreenGui

	local notifyCorner = Instance.new("UICorner")
	notifyCorner.CornerRadius = UDim.new(0, 8)
	notifyCorner.Parent = notifyFrame

	local notifyStroke = Instance.new("UIStroke")
	notifyStroke.Color = Color3.fromRGB(60, 60, 60)
	notifyStroke.Thickness = 1
	notifyStroke.Parent = notifyFrame

	local titleLabel = Instance.new("TextLabel")
	titleLabel.Size = UDim2.new(1, -20, 0, 25)
	titleLabel.Position = UDim2.new(0, 12, 0, 8)
	titleLabel.BackgroundTransparency = 1
	titleLabel.Text = "cre by: Hailan1219"
	titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
	titleLabel.TextSize = 15
	titleLabel.Font = Enum.Font.SourceSansBold
	titleLabel.TextXAlignment = Enum.TextXAlignment.Left
	titleLabel.Parent = notifyFrame

	local descLabel = Instance.new("TextLabel")
	descLabel.Size = UDim2.new(1, -20, 0, 20)
	descLabel.Position = UDim2.new(0, 12, 0, 33)
	descLabel.BackgroundTransparency = 1
	descLabel.Text = "tiktok: @stormchasers_vn"
	descLabel.TextColor3 = Color3.fromRGB(180, 180, 180)
	descLabel.TextSize = 13
	descLabel.Font = Enum.Font.SourceSans
	descLabel.TextXAlignment = Enum.TextXAlignment.Left
	descLabel.Parent = notifyFrame

	local progressBar = Instance.new("Frame")
	progressBar.Size = UDim2.new(1, 0, 0, 3)
	progressBar.Position = UDim2.new(0, 0, 1, -3)
	progressBar.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
	progressBar.BorderSizePixel = 0
	progressBar.Parent = notifyFrame

	local tweenIn = TweenService:Create(notifyFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
		Position = UDim2.new(1, -270, 1, -95)
	})
	tweenIn:Play()

	local progressTween = TweenService:Create(progressBar, TweenInfo.new(4, Enum.EasingStyle.Linear), {
		Size = UDim2.new(0, 0, 0, 3)
	})
	progressTween:Play()

	task.delay(4, function()
		local tweenOut = TweenService:Create(notifyFrame, TweenInfo.new(0.5, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
			Position = UDim2.new(1, 300, 1, -95)
		})
		tweenOut:Play()
		tweenOut.Completed:Connect(function()
			notifyFrame:Destroy()
		end)
	end)
end

showRayfieldNotify()

-------------------------------------------------------------------
-- 2. TẠO NÚT BẤM (TP TO ALL & CRE 2 DÒNG)
-------------------------------------------------------------------
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 170, 0, 60)
ToggleButton.Position = UDim2.new(1, -180, 0, 10)
ToggleButton.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
ToggleButton.Text = ""
ToggleButton.BorderSizePixel = 0
ToggleButton.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = ToggleButton

-- Tiêu đề: "TP TO ALL"
local MainTitle = Instance.new("TextLabel")
MainTitle.Name = "MainTitle"
MainTitle.Size = UDim2.new(1, 0, 0, 22)
MainTitle.Position = UDim2.new(0, 0, 0, 5)
MainTitle.BackgroundTransparency = 1
MainTitle.Text = "TP TO ALL"
MainTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
MainTitle.TextSize = 15
MainTitle.Font = Enum.Font.SourceSansBold
MainTitle.Parent = ToggleButton

-- Cre Dòng 1: "cre by: Hailan1219"
local SubTitle1 = Instance.new("TextLabel")
SubTitle1.Name = "SubTitle1"
SubTitle1.Size = UDim2.new(1, 0, 0, 14)
SubTitle1.Position = UDim2.new(0, 0, 0, 27)
SubTitle1.BackgroundTransparency = 1
SubTitle1.Text = "cre by: Hailan1219"
SubTitle1.TextColor3 = Color3.fromRGB(220, 220, 220)
SubTitle1.TextTransparency = 0.25
SubTitle1.TextSize = 11
SubTitle1.Font = Enum.Font.SourceSans
SubTitle1.Parent = ToggleButton

-- Cre Dòng 2: "tiktok: @stormchasers_vn"
local SubTitle2 = Instance.new("TextLabel")
SubTitle2.Name = "SubTitle2"
SubTitle2.Size = UDim2.new(1, 0, 0, 14)
SubTitle2.Position = UDim2.new(0, 0, 0, 41)
SubTitle2.BackgroundTransparency = 1
SubTitle2.Text = "tiktok: @stormchasers_vn"
SubTitle2.TextColor3 = Color3.fromRGB(220, 220, 220)
SubTitle2.TextTransparency = 0.25
SubTitle2.TextSize = 10
SubTitle2.Font = Enum.Font.SourceSans
SubTitle2.Parent = ToggleButton

-------------------------------------------------------------------
-- 3. LOGIC TELEPORT
-------------------------------------------------------------------
local isRunning = false

local function teleportLoop()
	while isRunning do
		local currentCharacter = LocalPlayer.Character
		if not currentCharacter or not currentCharacter:FindFirstChild("HumanoidRootPart") then
			task.wait(0.03)
			continue
		end
		
		local myRootPart = currentCharacter.HumanoidRootPart
		local humanoid = currentCharacter:FindFirstChildOfClass("Humanoid")
		
		if humanoid and humanoid.Health > 0 then
			humanoid.PlatformStand = true
			humanoid.AutoRotate = false
		else
			task.wait(0.03)
			continue
		end
		
		local playersList = Players:GetPlayers()
		
		for _, player in ipairs(playersList) do
			if not isRunning then break end
			
			if player ~= LocalPlayer and player.Character then
				local targetChar = player.Character
				local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")
				local targetHumanoid = targetChar:FindFirstChildOfClass("Humanoid")
				
				if targetRoot and targetHumanoid and targetHumanoid.Health > 0 then
					local targetPos = targetRoot.Position - Vector3.new(0, 3, 0)
					
					myRootPart.CFrame = CFrame.lookAt(targetPos, targetPos + Vector3.new(0, 50, 0))
					myRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
					myRootPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
					
					task.wait(0.03)
				end
			end
		end
		
		task.wait(0.03)
	end
	
	if LocalPlayer.Character then
		local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
		if humanoid then
			humanoid.PlatformStand = false
			humanoid.AutoRotate = true
		end
	end
end

-------------------------------------------------------------------
-- 4. LOGIC KÉO THẢ NGAY LẬP TỨC (INSTANT DRAG & CLICK)
-------------------------------------------------------------------
local dragging = false
local dragStart, startPos
local isMoved = false

ToggleButton.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		isMoved = false
		dragStart = input.Position
		startPos = ToggleButton.Position
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		
		-- Nếu di chuyển chuột/tay quá 5 pixel thì xác định là đang kéo nút
		if (math.abs(delta.X) > 5 or math.abs(delta.Y) > 5) then
			isMoved = true
		end
		
		if isMoved then
			ToggleButton.Position = UDim2.new(
				startPos.X.Scale,
				startPos.X.Offset + delta.X,
				startPos.Y.Scale,
				startPos.Y.Offset + delta.Y
			)
		end
	end
end)

ToggleButton.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		if dragging then
			dragging = false
			
			-- Nếu không di chuyển (chỉ nhấp vào) thì mới bật/tắt script
			if not isMoved then
				isRunning = not isRunning
				
				if isRunning then
					ToggleButton.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
					task.spawn(teleportLoop)
				else
					ToggleButton.BackgroundColor3 = Color3.fromRGB(46, 204, 113)
					
					if LocalPlayer.Character then
						local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
						if humanoid then
							humanoid.PlatformStand = false
							humanoid.AutoRotate = true
						end
					end
				end
			end
		end
	end
end)
