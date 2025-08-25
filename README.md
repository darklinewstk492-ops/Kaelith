local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer

_G.autoParryActive = false
_G.clashParryActive = false

-- Função AutoParry
function autoParry()
    local conn
    conn = RunService.Heartbeat:Connect(function()
        if not _G.autoParryActive then conn:Disconnect() return end
        local ball = workspace:FindFirstChild("Ball")
        local character = player.Character
        if ball and character and character:FindFirstChild("HumanoidRootPart") then
            local distance = (ball.Position - character.HumanoidRootPart.Position).Magnitude
            local ballVelocity = ball.Velocity
            local toPlayer = (character.HumanoidRootPart.Position - ball.Position).Unit
            local dot = ballVelocity.Unit:Dot(toPlayer)
            if distance < 15 and dot > 0.95 then
                character:FindFirstChild("ParryEvent"):FireServer()
            end
        end
    end)
end

-- Função AutoClashParry (Spam Parry para modo Clash)
function autoClashParry()
    local conn
    conn = RunService.Heartbeat:Connect(function()
        if not _G.clashParryActive then conn:Disconnect() return end
        local character = player.Character
        if character and character:FindFirstChild("ParryEvent") then
            for i = 1, 5 do
                character.ParryEvent:FireServer()
            end
        end
    end)
end

-- Criando Menu
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "AutoParryMenu"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 220, 0, 140)
frame.Position = UDim2.new(0.5, -110, 0.5, -70)
frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
frame.Visible = false

local function createButton(text, posY, toggleVar, toggleFunc)
    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0, 200, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, posY)
    btn.Text = "Ligar " .. text
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSans
    btn.TextSize = 20

    btn.MouseButton1Click:Connect(function()
        _G[toggleVar] = not _G[toggleVar]
        if _G[toggleVar] then
            toggleFunc()
            btn.Text = "Desligar " .. text
            btn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
        else
            btn.Text = "Ligar " .. text
            btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
        end
    end)
end

createButton("AutoParry", 20, "autoParryActive", autoParry)
createButton("AutoClashParry", 70, "clashParryActive", autoClashParry)

-- Abrir/fechar menu com Ctrl
UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.LeftControl then
        frame.Visible = not frame.Visible
    end
end)
