-- SISTEMA DE CARRY PLAYER (SOLIDO)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- CARREGAR PLAYER
local function carryPlayer(targetCharacter)
    local myChar = LocalPlayer.Character
    if not myChar or not targetCharacter then return end

    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
    local targetHRP = targetCharacter:FindFirstChild("HumanoidRootPart")
    local humanoid = targetCharacter:FindFirstChild("Humanoid")

    if not myHRP or not targetHRP or not humanoid then return end

    -- Evita duplicar weld
    if targetHRP:FindFirstChild("CarryWeld") then return end

    -- Posiciona o player (ajuste se quiser)
    targetHRP.CFrame = myHRP.CFrame * CFrame.new(0, 0, -2)

    -- Weld sólido
    local weld = Instance.new("WeldConstraint")
    weld.Name = "CarryWeld"
    weld.Part0 = myHRP
    weld.Part1 = targetHRP
    weld.Parent = targetHRP

    -- Ajustes importantes
    targetHRP.Anchored = false
    targetHRP.CanCollide = true
    humanoid.PlatformStand = true

    -- Network ownership (suaviza)
    pcall(function()
        targetHRP:SetNetworkOwner(LocalPlayer)
    end)
end

-- SOLTAR PLAYER
local function releasePlayer(targetCharacter)
    if not targetCharacter then return end

    local hrp = targetCharacter:FindFirstChild("HumanoidRootPart")
    local humanoid = targetCharacter:FindFirstChild("Humanoid")

    if hrp then
        local weld = hrp:FindFirstChild("CarryWeld")
        if weld then
            weld:Destroy()
        end
    end

    if humanoid then
        humanoid.PlatformStand = false
    end
end
