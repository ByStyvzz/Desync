local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local rootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

local DesyncConnection = nil
local DesyncAtivo = false

local function AtivarDesync()
    if DesyncConnection then return end

    DesyncConnection = RunService.Heartbeat:Connect(function()
        if not rootPart or not humanoid or not rootPart.Parent then return end

        local originalCFrame = rootPart.CFrame
        local originalCamOffset = humanoid.CameraOffset

        local hiddenPos = originalCFrame * CFrame.new(0, -200000, 0)
        rootPart.CFrame = hiddenPos
        humanoid.CameraOffset = hiddenPos:ToObjectSpace(
            CFrame.new(originalCFrame.Position)
        ).Position

        RunService.RenderStepped:Wait()

        rootPart.CFrame = originalCFrame
        humanoid.CameraOffset = originalCamOffset
    end)

    print("Desync LIGADO")
end

local function DesativarDesync()
    if DesyncConnection then
        DesyncConnection:Disconnect()
        DesyncConnection = nil
    end

    print("Desync DESLIGADO")
end

player.CharacterAdded:Connect(function(newChar)
    character = newChar
    rootPart = newChar:WaitForChild("HumanoidRootPart")
    humanoid = newChar:WaitForChild("Humanoid")
end)
