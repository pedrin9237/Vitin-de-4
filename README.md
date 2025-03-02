local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:FindFirstChildOfClass("Humanoid")
local rootPart = character:FindFirstChild("HumanoidRootPart")

local flying = false
local speed = 50  -- Velocidade do voo

-- Função para ativar/desativar o voo
local function toggleFly()
    if flying then
        -- Desativar voo
        flying = false
        humanoid.PlatformStand = false
    else
        -- Ativar voo
        flying = true
        humanoid.PlatformStand = true

        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVelocity.Parent = rootPart

        local bodyGyro = Instance.new("BodyGyro")
        bodyGyro.CFrame = rootPart.CFrame
        bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bodyGyro.Parent = rootPart

        -- Loop para manter o voo
        RunService.RenderStepped:Connect(function()
            if flying then
                local direction = Vector3.new(0, 0, 0)
                
                -- Captura os inputs do jogador
                if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                    direction = direction + (workspace.CurrentCamera.CFrame.LookVector * speed)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                    direction = direction - (workspace.CurrentCamera.CFrame.LookVector * speed)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                    direction = direction - (workspace.CurrentCamera.CFrame.RightVector * speed)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                    direction = direction + (workspace.CurrentCamera.CFrame.RightVector * speed)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                    direction = direction + Vector3.new(0, speed, 0)
                end
                if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
                    direction = direction - Vector3.new(0, speed, 0)
                end

                bodyVelocity.Velocity = direction
                bodyGyro.CFrame = workspace.CurrentCamera.CFrame
            else
                bodyVelocity:Destroy()
                bodyGyro:Destroy()
            end
        end)
    end
end

-- Ativa/desativa o voo ao pressionar a tecla "F"
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.F then
        toggleFly()
    end
end)
