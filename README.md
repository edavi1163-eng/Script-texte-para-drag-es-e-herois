```lua
-- Cria o painel de controle
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 350)
Frame.Position = UDim2.new(0.5, -100, 0.5, -175)
Frame.BackgroundColor3 = Color3.new(1, 1, 1)
Frame.Parent = ScreenGui

local AutoKillButton = Instance.new("TextButton")
AutoKillButton.Size = UDim2.new(0, 100, 0, 50)
AutoKillButton.Position = UDim2.new(0.5, -50, 0.5, -150)
AutoKillButton.Text = "Ativar Auto Kill"
AutoKillButton.Parent = Frame

local HitKillButton = Instance.new("TextButton")
HitKillButton.Size = UDim2.new(0, 100, 0, 50)
HitKillButton.Position = UDim2.new(0.5, -50, 0.5, -100)
HitKillButton.Text = "Ativar Hit Kill"
HitKillButton.Parent = Frame

local FlyButton = Instance.new("TextButton")
FlyButton.Size = UDim2.new(0, 100, 0, 50)
FlyButton.Position = UDim2.new(0.5, -50, 0.5, -50)
FlyButton.Text = "Ativar Fly"
FlyButton.Parent = Frame

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(0, 50, 0, 20)
SpeedInput.Position = UDim2.new(0.5, 50, 0.5, -50)
SpeedInput.Text = "50"
SpeedInput.Parent = Frame

local DodgeButton = Instance.new("TextButton")
DodgeButton.Size = UDim2.new(0, 100, 0, 50)
DodgeButton.Position = UDim2.new(0.5, -50, 0.5, 0)
DodgeButton.Text = "Ativar Dodge"
DodgeButton.Parent = Frame

local AutoAttackButton = Instance.new("TextButton")
AutoAttackButton.Size = UDim2.new(0, 100, 0, 50)
AutoAttackButton.Position = UDim2.new(0.5, -50, 0.5, 50)
AutoAttackButton.Text = "Ativar Auto Attack"
AutoAttackButton.Parent = Frame

local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Size = UDim2.new(0, 20, 0, 20)
MinimizeButton.Position = UDim2.new(1, -20, 0, 0)
MinimizeButton.Text = "-"
MinimizeButton.Parent = Frame

-- Variável para controlar o auto kill, hit kill, fly, dodge e auto attack
local autoKillAtivo = false
local hitKillAtivo = false
local flyAtivo = false
local dodgeAtivo = false
local autoAttackAtivo = false
local minimizado = false
local flySpeed = 50

-- Função para matar os mobs
local function matarMobs()
    while autoKillAtivo do
        local mobs = game:GetService("Workspace"):GetDescendants()
        for _, mob in pairs(mobs) do
            if mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
                game:GetService("ReplicatedStorage").Attack:FireServer(mob)
                wait(0.1)
            end
        end
        wait(1)
    end
end

-- Função para hit kill os mobs
local function hitKillMobs()
    while hitKillAtivo do
        local mobs = game:GetService("Workspace"):GetDescendants()
        for _, mob in pairs(mobs) do
            if mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
                game:GetService("ReplicatedStorage").Attack:FireServer(mob, "HitKill")
                wait(0.1)
            end
        end
        wait(1)
    end
end

-- Função para fly
local function fly()
    while flyAtivo do
        local player = game.Players.LocalPlayer
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.PlatformStand = true
            local velocity = Vector3.new(0, 0, 0)
            if game.UserInputService:IsKeyDown(Enum.KeyCode.W) then
                velocity = velocity + game.Workspace.CurrentCamera.CFrame.LookVector * flySpeed
            end
            if game.UserInputService:IsKeyDown(Enum.KeyCode.S) then
                velocity = velocity - game.Workspace.CurrentCamera.CFrame.LookVector * flySpeed
            end
            if game.UserInputService:IsKeyDown(Enum.KeyCode.A) then
                velocity = velocity - game.Workspace.CurrentCamera.CFrame.RightVector * flySpeed
            end
            if game.UserInputService:IsKeyDown(Enum.KeyCode.D) then
                velocity = velocity + game.Workspace.CurrentCamera.CFrame.RightVector * flySpeed
            end
            if game.UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                velocity = velocity + Vector3.new(0, flySpeed, 0)
            end
            if game.UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                velocity = velocity - Vector3.new(0, flySpeed, 0)
            end
            humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                humanoidRootPart.Velocity = velocity
            end
        end
        wait(0.1)
    end
end

-- Função para dodge
local function dodge()
    while dodgeAtivo do
        local player = game.Players.LocalPlayer
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            local mobs = game:GetService("Workspace"):GetDescendants()
            for _, mob in pairs(mobs) do
                if mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
                    local mobPosition = mob.HumanoidRootPart.Position
                    local playerPosition = player.Character.HumanoidRootPart.Position
                    local distance = (mobPosition - playerPosition).Magnitude
                    if distance < 10 then
                        local direction = (playerPosition - mobPosition).Unit
                        player.Character.HumanoidRootPart.Velocity = direction * 50
                        wait(0.5)
                    end
                end
            end
        end
        wait(0.1)
    end
end

-- Função para auto attack
local function autoAttack()
    while autoAttackAtivo do
        local player = game.Players.LocalPlayer
        local humanoid = player.Character:FindFirstChild("Humanoid")
        if humanoid then
            local mobs = game:GetService("Workspace"):GetDescendants()
            for _, mob in pairs(mobs) do
                if mob:FindFirstChild("Humanoid") and mob.Humanoid.Health > 0 then
                    local mobPosition = mob.HumanoidRootPart.Position
                    local playerPosition = player.Character.HumanoidRootPart.Position
                    local distance = (mobPosition - playerPosition).Magnitude
                    if distance < 10 then
                        game:GetService("ReplicatedStorage").Attack:FireServer(mob)
                        wait(0.1)
                    end
                end
            end
        end
        wait(0.1)
    end
end

-- Evento para o botão de auto kill
AutoKillButton.MouseButton1Click:Connect(function()
    if autoKillAtivo then
        autoKillAtivo = false
        AutoKillButton.Text = "Ativar Auto Kill"
    else
        autoKillAtivo = true
        AutoKillButton.Text = "Desativar Auto Kill"
        matarMobs()
    end
end)

-- Evento para o botão de hit kill
HitKillButton.MouseButton1Click:Connect(function()
    if hitKillAtivo then
        hitKillAtivo = false
        HitKillButton.Text = "Ativar Hit Kill"
    else
        hitKillAtivo = true
        HitKillButton.Text = "Desativar Hit Kill"
        hitKillMobs()
    end
end)

-- Evento para o botão de fly
FlyButton.MouseButton1Click:Connect(function()
    if flyAtivo then
        flyAtivo = false
        FlyButton.Text = "Ativar Fly"
    else
        flyAtivo = true
        FlyButton.Text = "Desativar Fly"
        flySpeed = tonumber(SpeedInput.Text)
        fly()
    end
end)

-- Evento para o botão de dodge
DodgeButton.MouseButton1Click:Connect(function()
    if dodgeAtivo then
        dodgeAtivo = false
        DodgeButton.Text = "Ativar Dodge"
    else
        dodgeAtivo = true
        DodgeButton.Text = "Desativar Dodge"
        dodge()
    end
end)

-- Evento para o botão de auto attack
AutoAttackButton.MouseButton1Click:Connect(function()
    if autoAttackAtivo then
        autoAttackAtivo = false
        AutoAttackButton.Text = "Ativar Auto Attack"
    else
        autoAttackAtivo = true
        AutoAttackButton.Text = "Desativar Auto Attack"
        autoAttack()
    end
