-- Isaque Hub - Completo (Redz Style) 🔥

-- Services & Vars
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ws = workspace
local UIS = game:GetService("UserInputService")

-- GUI Base
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "IsaqueHub"

local Main = Instance.new("Frame", ScreenGui)
Main.Name = "Main"
Main.Size = UDim2.new(0, 550, 0, 350)
Main.Position = UDim2.new(0.5, -275, 0.5, -175)
Main.BackgroundColor3 = Color3.fromRGB(25,25,25)
Main.Active = true; Main.Draggable = true

local Top = Instance.new("TextLabel", Main)
Top.Size = UDim2.new(1,0,0,40)
Top.BackgroundColor3 = Color3.fromRGB(35,35,35)
Top.Text = "Isaque Hub - Blox Fruits"
Top.TextColor3 = Color3.new(1,1,1)
Top.Font = Enum.Font.SourceSansBold; Top.TextSize = 20

local Toggle = Instance.new("TextButton", Main)
Toggle.Name = "Toggle"
Toggle.Size = UDim2.new(0, 80,0,30)
Toggle.Position = UDim2.new(1,-90,0,5)
Toggle.Text = "-"
Toggle.BackgroundColor3 = Color3.fromRGB(70,70,70)
Toggle.TextColor3 = Color3.new(1,1,1)
Toggle.MouseButton1Click:Connect(function()
    Main.Visible = not Main.Visible
    Toggle.Text = Main.Visible and "-" or "+"
end)

-- Tab setup
local TabFrame = Instance.new("Frame", Main)
TabFrame.Size = UDim2.new(0,150,1,-40)
TabFrame.Position = UDim2.new(0,0,0,40)
TabFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)

local Content = Instance.new("Frame", Main)
Content.Size = UDim2.new(1, -150,1,-40)
Content.Position = UDim2.new(0,150,0,40)
Content.BackgroundColor3 = Color3.fromRGB(45,45,45)

local tabs = {"AutoFarm","Frutas","ESP","Teleport","Combat","Misc"}
local pages = {}
for i, name in ipairs(tabs) do
    local btn = Instance.new("TextButton",TabFrame)
    btn.Size = UDim2.new(1,0,0,30)
    btn.Position = UDim2.new(0,0,0,(i-1)*35)
    btn.Text = name; btn.BackgroundColor3 = Color3.fromRGB(40,40,40); btn.TextColor3 = Color3.new(1,1,1)

    local page = Instance.new("ScrollingFrame",Content)
    page.Name = name; page.Size = UDim2.new(1,0,1,0); page.CanvasSize = UDim2.new(0,0,3,0)
    page.BackgroundTransparency = 1; page.Visible = (i==1)
    pages[name] = page

    btn.MouseButton1Click:Connect(function()
        for _, p in pairs(pages) do p.Visible = false end
        page.Visible = true
    end)
end

-- Utility function: create toggle
local function createToggle(parent, label, callback)
    local txt = Instance.new("TextLabel", parent)
    txt.Text = label; txt.TextColor3 = Color3.new(1,1,1); txt.Size = UDim2.new(0,1,0,30); txt.Position = UDim2.new(0,10,0, (#parent:GetChildren())*35)
    local btn = Instance.new("TextButton", parent)
    btn.Text = "Off"; btn.TextColor3 = Color3.new(1,1,1)
    btn.Size = UDim2.new(0,80,0,30); btn.Position = UDim2.new(1,-90,0, txt.Position.Y.Offset)
    btn.BackgroundColor3 = Color3.fromRGB(60,60,60)

    local state=false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = state and "On" or "Off"
        callback(state)
    end)
    return btn
end

-- MODULES

-- 1. AutoFarm
local function autofarm(state)
    if state then
        spawn(function()
            while pages.AutoFarm do
                task.wait(0.5)
                for _, mob in ipairs(ws.Enemies:GetChildren()) do
                    if mob:FindFirstChild("HumanoidRootPart") then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = mob.HumanoidRootPart.CFrame * CFrame.new(0,3,0)
                        -- attack loop
                        for i=1,5 do
                            fireclickdetector(mob.Head.ClickDetector) -- example, adapt to actual attack
                        end
                    end
                end
            end
        end)
    end
end
createToggle(pages.AutoFarm, "AutoFarm Inimigos", autofarm)

-- 2. Farm Frutas
local function autofruit(state)
    if state then
        spawn(function()
            while pages.Frutas do
                task.wait(1)
                for _, fruit in ipairs(ws.Fruits:GetChildren()) do
                    if fruit:IsA("Tool") or fruit:IsA("Part") then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = fruit.Handle.CFrame
                    end
                end
            end
        end)
    end
end
createToggle(pages.Frutas, "Coletar Frutas", autofruit)

-- 3. ESP
local esps = {}
local function toggleESP(state)
    if state then
        for _, v in ipairs(ws.Fruits:GetChildren()) do
            if not esps[v] then
                local b = Instance.new("BillboardGui", v)
                b.Size = UDim2.new(0, 100, 0, 30)
                b.AlwaysOnTop = true
                local l = Instance.new("TextLabel", b)
                l.Size = UDim2.new(1,0,1,0)
                l.BackgroundTransparency = 1
                l.Text = v.Name
                l.TextColor3 = Color3.new(1,1,0)
                esps[v] = b
            end
        end
    else
        for _, b in pairs(esps) do b:Destroy() end
        esps = {}
    end
end
createToggle(pages.ESP, "ESP Frutas", toggleESP)
-- Repita para jogadores e inimigos se quiser

-- 4. Teleportes
local mapTP = {
    ["First Sea"] = CFrame.new(100,5,200),
    ["Second Sea"] = CFrame.new(500,5,900),
    ["Third Sea"] = CFrame.new(1200,5,1500)
}
for name, cframe in pairs(mapTP) do
    local btn = Instance.new("TextButton", pages.Teleport)
    btn.Text = "TP "..name
    btn.Size = UDim2.new(0,200,0,30)
    btn.Position = UDim2.new(0,10,0, (#pages.Teleport:GetChildren())*35)
    btn.MouseButton1Click:Connect(function()
        LocalPlayer.Character.HumanoidRootPart.CFrame = cframe
    end)
end

-- 5. Combat
createToggle(pages.Combat, "Kill Aura", function(state)
    if state then
        game:GetService("RunService").Heartbeat:Connect(function()
            for _, mob in ipairs(ws.Enemies:GetChildren()) do
                if mob.Humanoid.Health>0 and (mob.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude < 10 then
                    fireclickdetector(mob.Head.ClickDetector)
                end
            end
        end)
    end
end)

createToggle(pages.Combat, "Auto Haki/Buso", function(state)
    -- placeholder: chamar eventos equipar haki
end)

-- 6. Misc (FPS Boost, Stats)
createToggle(pages.Misc, "FPS Boost", function(state)
    if state then
        setfpscap(60) -- exemplo
    end
end)
