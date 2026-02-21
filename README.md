-- Cria o painel de controle
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer.PlayerGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.5, -100, 0.5, -75)
Frame.BackgroundColor3 = Color3.new(1, 1, 1)
Frame.Parent = ScreenGui

local AimbotButton = Instance.new("TextButton")
AimbotButton.Size = UDim2.new(0, 100, 0, 50)
AimbotButton.Position = UDim2.new(0.5, -50, 0.5, -50)
AimbotButton.Text = "Ativar Aimbot"
AimbotButton.Parent = Frame

local FOVInput = Instance.new("TextBox")
FOVInput.Size = UDim2.new(0, 50, 0, 20)
FOVInput.Position = UDim2.new(0.5, -25, 0.5, 0)
FOVInput.Text = "100"
FOVInput.Parent = Frame

local FOVLabel = Instance.new("TextLabel")
FOVLabel.Size = UDim2.new(0, 50, 0, 20)
FOVLabel.Position = UDim2.new(0.5, 25, 0.5, 0)
FOVLabel.Text = "FOV"
FOVLabel.Parent = Frame

-- Variável para controlar o aimbot
local aimbotAtivo = false
local fov = 100

-- Função para aimbot
local function aimbot()
    while aimbotAtivo do
        -- Encontra o jogador mais próximo
        local players = game.Players:GetPlayers()
        local closestPlayer = nil
        local closestDistance = math.huge
        for _, player in pairs(players) do
            if player ~= game.Players.LocalPlayer and player.Character and player.Character:FindFirstChild("Humanoid") then
                local distance = (player.Character.HumanoidRootPart.Position - game.Workspace.CurrentCamera.CFrame.Position).Magnitude
                if distance < closestDistance then
                    closestPlayer = player
                    closestDistance = distance
                end
            end
        end
        if closestPlayer then
            -- Verifica se o jogador está dentro do FOV
            local screenPosition = game.Workspace.CurrentCamera:WorldToScreenPoint(closestPlayer.Character.HumanoidRootPart.Position)
            local distanceFromCenter = math.sqrt((screenPosition.X - game.Workspace.CurrentCamera.ViewportSize.X / 2) ^ 2 + (screenPosition.Y - game.Workspace.CurrentCamera.ViewportSize.Y / 2) ^ 2)
            if distanceFromCenter < fov / 2 then
                -- Avisa o jogador
                game.Workspace.CurrentCamera.CFrame = CFrame.new(game.Workspace.CurrentCamera.CFrame.Position, closestPlayer.Character.HumanoidRootPart.Position)
            end
        end
        wait(0.1)
    end
end

-- Evento para o botão de aimbot
AimbotButton.MouseButton1Click:Connect(function()
    if aimbotAtivo then
        aimbotAtivo = false
        AimbotButton.Text = "Ativar Aimbot"
    else
        aimbotAtivo = true
        AimbotButton.Text = "Desativar Aimbot"
        fov = tonumber(FOVInput.Text)
        aimbot()
    end
end)
