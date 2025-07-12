-- Wastelane Full Script (Estilo Jey GUI) - Corrigido
-- Criado por ChatGPT | Para uso educacional

-- Proteção contra kick
pcall(function()
    for i, v in pairs(getconnections(game:GetService("Players").LocalPlayer.Idled)) do
        v:Disable()
    end
end)

-- GUI
local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/shlexware/Orion/main/source'))()
local Window = OrionLib:MakeWindow({Name = "Wastelane Script [ALFA]", HidePremium = false, SaveConfig = false, IntroEnabled = true, IntroText = "Wastelane - Classes", ConfigFolder = "WastelaneConfig"})

-- Variáveis globais
local Player = game.Players.LocalPlayer
local Humanoid = nil

-- Atualizar humanoid no respawn
local function setupHumanoid()
    if Player.Character then
        Humanoid = Player.Character:WaitForChild("Humanoid")
    end
end

Player.CharacterAdded:Connect(function()
    setupHumanoid()
end)

setupHumanoid()

-- Flags
local speedEnabled = false
local jumpEnabled = false
local autoFarm = false
local autoCollectEnabled = false

-- Speed Hack Coroutine
coroutine.wrap(function()
    while true do
        if speedEnabled and Humanoid then
            Humanoid.WalkSpeed = 80
        elseif Humanoid then
            Humanoid.WalkSpeed = 16
        end
        task.wait(0.1)
    end
end)()

-- High Jump Coroutine
coroutine.wrap(function()
    while true do
        if jumpEnabled and Humanoid then
            Humanoid.JumpPower = 120
        elseif Humanoid then
            Humanoid.JumpPower = 50
        end
        task.wait(0.1)
    end
end)()

-- Auto Farm Coroutine
coroutine.wrap(function()
    while true do
        if autoFarm and Player.Character and Humanoid then
            for _, mob in pairs(workspace:GetDescendants()) do
                if mob:IsA("Model") and mob:FindFirstChild("Humanoid") and mob:FindFirstChild("HumanoidRootPart") and mob ~= Player.Character then
                    local hrp = mob:FindFirstChild("HumanoidRootPart")
                    if hrp then
                        -- Teleporta perto do inimigo
                        Player.Character:PivotTo(hrp.CFrame * CFrame.new(0, 0, -3))
                        wait(0.5)
                    end
                end
            end
        end
        task.wait(0.1)
    end
end)()

-- Auto Collect Coroutine
coroutine.wrap(function()
    while true do
        if autoCollectEnabled and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
            for _, item in pairs(workspace:GetDescendants()) do
                if item:IsA("Tool") and item:FindFirstChild("Handle") then
                    pcall(function()
                        firetouchinterest(Player.Character.HumanoidRootPart, item.Handle, 0)
                        firetouchinterest(Player.Character.HumanoidRootPart, item.Handle, 1)
                    end)
                end
            end
            wait(2)
        else
            task.wait(0.5)
        end
    end
end)()

-- ESP
function createESP()
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("Model") and v:FindFirstChild("Humanoid") and not v:FindFirstChild("Highlight") and v ~= Player.Character then
            local highlight = Instance.new("Highlight")
            highlight.FillColor = Color3.fromRGB(255, 0, 0)
            highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
            highlight.FillTransparency = 0.5
            highlight.Parent = v
        end
    end
end

-- GUI Tabs
local Tab = Window:MakeTab({Name = "Funções", Icon = "rbxassetid://4483345998", PremiumOnly = false})

Tab:AddToggle({
    Name = "Speed",
    Default = false,
    Callback = function(value)
        speedEnabled = value
    end
})

Tab:AddToggle({
    Name = "High Jump",
    Default = false,
    Callback = function(value)
        jumpEnabled = value
    end
})

Tab:AddButton({
    Name = "ESP Inimigos",
    Callback = createESP
})

Tab:AddToggle({
    Name = "Auto Coletar Itens",
    Default = false,
    Callback = function(value)
        autoCollectEnabled = value
    end
})

Tab:AddToggle({
    Name = "Auto Farm",
    Default = false,
    Callback = function(value)
        autoFarm = value
    end
})

OrionLib:Init()
