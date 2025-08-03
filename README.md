-- Werick Hub para Blox Fruits
-- Senha: "inscritos"

-- Proteção
if game.CoreGui:FindFirstChild("WerickHub") then return end

local Players, TweenService, RunService, UIS = game:GetService("Players"), game:GetService("TweenService"), game:GetService("RunService"), game:GetService("UserInputService")
local lp, char = Players.LocalPlayer, Players.LocalPlayer.Character or Players.LocalPlayer.CharacterAdded:Wait()

-- GUI Principal
local sg = Instance.new("ScreenGui", game.CoreGui)
sg.Name = "WerickHub"
local main = Instance.new("Frame", sg)
main.Size = UDim2.new(0, 320, 0, 360)
main.Position = UDim2.new(0.5, -160, 0.5, -180)
main.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true

local uicorner = Instance.new("UICorner", main)
uicorner.CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "🌊 Werick Hub - Blox Fruits"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

local KeyBox = Instance.new("TextBox", main)
KeyBox.PlaceholderText = "Digite a senha"
KeyBox.Text = ""
KeyBox.Position = UDim2.new(0.1, 0, 0.2, 0)
KeyBox.Size = UDim2.new(0.8, 0, 0, 30)
KeyBox.TextSize = 16
KeyBox.Font = Enum.Font.Gotham
KeyBox.BackgroundColor3 = Color3.fromRGB(30,30,30)
KeyBox.TextColor3 = Color3.new(1,1,1)
Instance.new("UICorner", KeyBox)

local confirm = Instance.new("TextButton", main)
confirm.Text = "Confirmar"
confirm.Position = UDim2.new(0.3, 0, 0.35, 0)
confirm.Size = UDim2.new(0.4, 0, 0, 30)
confirm.BackgroundColor3 = Color3.fromRGB(0, 170, 100)
confirm.TextColor3 = Color3.new(1,1,1)
confirm.Font = Enum.Font.GothamBold
confirm.TextSize = 14
Instance.new("UICorner", confirm)

-- Script concluído
local Success = Instance.new("TextLabel", sg)
Success.Text = "✔ Script Concluído"
Success.Position = UDim2.new(1, -260, 0, 20)
Success.Size = UDim2.new(0, 250, 0, 30)
Success.BackgroundColor3 = Color3.fromRGB(30, 200, 80)
Success.TextColor3 = Color3.new(1, 1, 1)
Success.Font = Enum.Font.GothamBold
Success.TextSize = 16
Success.Visible = false
Instance.new("UICorner", Success)

-- Funções
local function createToggle(name, parent, funcOn, funcOff)
    local enabled = false
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Text = name .. ": Off"
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = name .. ": " .. (enabled and "On" or "Off")
        if enabled then funcOn() else funcOff() end
    end)
end

local function openMainMenu()
    KeyBox.Visible = false
    confirm.Visible = false
    title.Text = "✅ Menu Principal"
    
    local options = Instance.new("Frame", main)
    options.Position = UDim2.new(0, 10, 0, 60)
    options.Size = UDim2.new(1, -20, 1, -70)
    options.BackgroundTransparency = 1
    
    createToggle("ESP", options,
        function()
            RunService.RenderStepped:Connect(function()
                for _, v in ipairs(Players:GetPlayers()) do
                    if v ~= lp and v.Character and v.Character:FindFirstChild("HumanoidRootPart") and not v.Character:FindFirstChild("ESPBox") then
                        local b = Instance.new("BoxHandleAdornment", v.Character)
                        b.Name = "ESPBox"
                        b.Adornee = v.Character:FindFirstChild("HumanoidRootPart")
                        b.Size = Vector3.new(5, 6, 1)
                        b.Transparency = 0.6
                        b.Color3 = v.Team == lp.Team and Color3.new(0, 1, 0) or Color3.new(1, 0, 0)
                        b.AlwaysOnTop = true
                        b.ZIndex = 10
                    end
                end
            end)
        end,
        function()
            for _, v in ipairs(Players:GetPlayers()) do
                if v.Character and v.Character:FindFirstChild("ESPBox") then
                    v.Character.ESPBox:Destroy()
                end
            end
        end
    )

    createToggle("Speed Boost", options,
        function()
            lp.Character.Humanoid.WalkSpeed = 150
        end,
        function()
            lp.Character.Humanoid.WalkSpeed = 16
        end
    )

    createToggle("Jump Boost", options,
        function()
            lp.Character.Humanoid.JumpPower = 150
        end,
        function()
            lp.Character.Humanoid.JumpPower = 50
        end
    )

    createToggle("God Mode", options,
        function()
            lp.Character:FindFirstChildOfClass("Humanoid").Name = "GodHumanoid"
            local clone = lp.Character["GodHumanoid"]:Clone()
            lp.Character["GodHumanoid"]:Destroy()
            clone.Parent = lp.Character
        end,
        function()
            -- reiniciar personagem para tirar god mode
            lp:LoadCharacter()
        end
    )

    createToggle("Auto Farm", options,
        function()
            _G.farming = true
            task.spawn(function()
                while _G.farming do
                    for _, v in ipairs(workspace.Enemies:GetChildren()) do
                        if v:FindFirstChild("HumanoidRootPart") and v:FindFirstChildOfClass("Humanoid") and v:FindFirstChildOfClass("Humanoid").Health > 0 then
                            local hrp = lp.Character:FindFirstChild("HumanoidRootPart")
                            hrp.CFrame = v.HumanoidRootPart.CFrame + Vector3.new(0, 10, 0)
                            v.Humanoid:TakeDamage(100)
                            wait(0.2)
                        end
                    end
                    wait(1)
                end
            end)
        end,
        function()
            _G.farming = false
        end
    )
end

-- Verificar senha
confirm.MouseButton1Click:Connect(function()
    if KeyBox.Text:lower() == "inscritos" then
        Success.Visible = true
        wait(2)
        Success.Visible = false
        openMainMenu()
    else
        KeyBox.Text = "Senha incorreta"
    end
end)
