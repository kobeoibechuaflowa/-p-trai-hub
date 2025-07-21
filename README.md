-- ĐỂ ĐẸP TRAI HUB | Grow a Garden v1.0
-- Script tổng hợp by ChatGPT

-- 📦 Setup
local player = game.Players.LocalPlayer
local rs = game:GetService("ReplicatedStorage")
local garden = workspace:WaitForChild("Garden")

local plantEvent = rs:WaitForChild("Plant")
local waterEvent = rs:WaitForChild("Water")
local harvestEvent = rs:WaitForChild("Harvest")
local sellEvent = rs:WaitForChild("Sell")
local upgradeEvent = rs:WaitForChild("Upgrade")

-- 🖼️ GUI Setup
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "Để Đẹp Trai Hub"
gui.ResetOnSpawn = false

-- Hàm tạo nút
local function createButton(name, posY, text, color)
	local btn = Instance.new("TextButton", gui)
	btn.Name = name
	btn.Size = UDim2.new(0, 180, 0, 40)
	btn.Position = UDim2.new(0, 10, 0, posY)
	btn.Text = text
	btn.BackgroundColor3 = color
	btn.TextColor3 = Color3.new(1, 1, 1)
	btn.Font = Enum.Font.GothamBold
	btn.TextScaled = true
	return btn
end

-- 🔄 Auto Farm Toggle
local toggle = false
local autoBtn = createButton("AutoBtn", 0.05, "🌱 Bật Auto Farm", Color3.fromRGB(60, 180, 75))
autoBtn.MouseButton1Click:Connect(function()
	toggle = not toggle
	autoBtn.Text = toggle and "🛑 Tắt Auto Farm" or "🌱 Bật Auto Farm"
	autoBtn.BackgroundColor3 = toggle and Color3.fromRGB(200, 80, 60) or Color3.fromRGB(60, 180, 75)
end)

-- ⬆️ Nâng cấp
local upgradeBtn = createButton("UpgradeBtn", 0.15, "⬆️ Nâng cấp Hạt & Nước", Color3.fromRGB(90, 90, 250))
upgradeBtn.MouseButton1Click:Connect(function()
	upgradeEvent:FireServer("Seed")
	wait(0.1)
	upgradeEvent:FireServer("Water")
end)

-- 🧱 Mở rộng ô đất
local unlockBtn = createButton("UnlockBtn", 0.25, "🧱 Mở rộng Ô đất", Color3.fromRGB(120, 180, 250))
unlockBtn.MouseButton1Click:Connect(function()
	upgradeEvent:FireServer("UnlockPlot")
end)

-- 🌾 Dùng phân bón
local fertBtn = createButton("FertBtn", 0.35, "🌾 Dùng Phân Bón", Color3.fromRGB(255, 150, 200))
fertBtn.MouseButton1Click:Connect(function()
	upgradeEvent:FireServer("Fertilize")
end)

-- 🌦️ Hiển thị thời tiết
local weather = Instance.new("TextLabel", gui)
weather.Size = UDim2.new(0, 180, 0, 30)
weather.Position = UDim2.new(0, 10, 0, 0.45)
weather.TextColor3 = Color3.new(1, 1, 1)
weather.BackgroundTransparency = 1
weather.Font = Enum.Font.Gotham
weather.TextScaled = true

local weatherTypes = {"☀️ Nắng", "🌧️ Mưa", "⛈️ Giông"}
spawn(function()
	while true do
		local w = weatherTypes[math.random(1, #weatherTypes)]
		weather.Text = "Thời tiết: " .. w
		wait(60)
	end
end)

-- 🏆 Thành tựu hiển thị
local function unlockAchievement(name)
	local achv = Instance.new("TextLabel", gui)
	achv.Size = UDim2.new(0, 200, 0, 40)
	achv.Position = UDim2.new(0.5, -100, 0.8, 0)
	achv.BackgroundColor3 = Color3.fromRGB(255, 215, 0)
	achv.Text = "🏆 Thành tựu: " .. name
	achv.TextColor3 = Color3.new(0, 0, 0)
	achv.Font = Enum.Font.GothamBold
	achv.TextScaled = true
	wait(3)
	achv:Destroy()
end

spawn(function()
	wait(10)
	unlockAchievement("Trồng 10 cây")
	wait(20)
	unlockAchievement("Thu hoạch chuyên nghiệp")
end)

-- 🌱 Auto Farm Loop
spawn(function()
	while true do
		if toggle then
			for _, plot in pairs(garden:GetChildren()) do
				if plot:FindFirstChild("Plantable") and plot.Plantable.Value then
					plantEvent:FireServer(plot)
				end
				if plot:FindFirstChild("WaterNeeded") and plot.WaterNeeded.Value then
					waterEvent:FireServer(plot)
				end
				if plot:FindFirstChild("ReadyToHarvest") and plot.ReadyToHarvest.Value then
					harvestEvent:FireServer(plot)
				end
			end
			sellEvent:FireServer()
		end
		wait(3)
	end
end)
