-- Limpieza profunda preventiva para evitar errores de hilos colgados
if game:GetService("CoreGui"):FindFirstChild("GalaxyNeonFixSystem") then 
    game:GetService("CoreGui").MiranhaNeonSystem:Destroy() 
    game:GetService("CoreGui").GalaxyNeonFixSystem:Destroy() 
end

local Player = game.Players.LocalPlayer
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- CONFIGURACIONES MANUALES (Todo inicia estrictamente apagado)
local AutoCollectTPActive = false
local AutoAttackActive = false

-- ==========================================
-- CAPA VISUAL FLOTANTE NATIVA (CYAN & BLACK)
-- ==========================================
local FolderGui = Instance.new("ScreenGui", CoreGui)
FolderGui.Name = "GalaxyNeonFixSystem"
FolderGui.ResetOnSpawn = false

-- Contenedor de la rejilla alineado a la derecha de tu pantalla móvil
local GridContainer = Instance.new("Frame", FolderGui)
GridContainer.Size = UDim2.new(0, 160, 0, 220)
GridContainer.Position = UDim2.new(0.8, -15, 0.35, 0)
GridContainer.BackgroundTransparency = 1

local UIList = Instance.new("UIListLayout", GridContainer)
UIList.Padding = UDim.new(0, 8)
UIList.HorizontalAlignment = Enum.HorizontalAlignment.Right

-- Generador de Botones con Estética Cyan Neón de Alta Visibilidad
local function CreateCyanButton(text, callback)
    local B = Instance.new("TextButton", GridContainer)
    B.Size = UDim2.new(0, 155, 0, 42)
    B.BackgroundColor3 = Color3.fromRGB(0, 0, 0) -- Fondo Negro Absoluto
    B.Text = text
    B.TextColor3 = Color3.fromRGB(0, 240, 255) -- Letras Cyan Eléctrico
    B.Font = Enum.Font.SourceSansBold
    B.TextSize = 12
    
    local C = Instance.new("UICorner", B)
    C.CornerRadius = UDim.new(0, 8)
    
    -- Contorno grueso Cyan neón
    local Stroke = Instance.new("UIStroke", B)
    Stroke.Thickness = 2
    Stroke.Color = Color3.fromRGB(0, 240, 255)
    
    B.MouseButton1Click:Connect(function()
        callback(B, Stroke)
    end)
    return B
end

-- ==========================================
-- BOTÓN 1: INSTANT TP SEQUENTIAL A LAS MONEDAS (GALAXY COINS)
-- ==========================================
CreateCyanButton("🌌 MONEDAS TP: OFF", function(btn, stroke)
    AutoCollectTPActive = not AutoCollectTPActive
    if AutoCollectTPActive then
        btn.Text = "🔮 COIN TP: RECOLECTANDO"
        btn.TextColor3 = Color3.fromRGB(255, 255, 255) -- Letra blanca al encender
        btn.BackgroundColor3 = Color3.fromRGB(0, 90, 110) -- Fondo Cyan oscuro activo
        stroke.Color = Color3.fromRGB(255, 255, 255)
    else
        btn.Text = "🌌 MONEDAS TP: OFF"
        btn.TextColor3 = Color3.fromRGB(0, 240, 255)
        btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        stroke.Color = Color3.fromRGB(0, 240, 255)
    end
end)

task.spawn(function()
    while true do
        task.wait(0.15) -- Frecuencia calibrada para absorber y saltar sin ser congelado por el servidor
        if AutoCollectTPActive and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
            local Root = Player.Character.HumanoidRootPart
            
            -- Escáner masivo de Workspace para hallar las monedas del Galaxy Event
            for _, coin in pairs(Workspace:GetDescendants()) do
                local esMonedaEvento = false
                local nameLower = string.lower(coin.Name)
                
                -- Filtro de palabras clave actualizadas de la base de datos (crystals, shards, tokens, galaxy)
                if string.find(nameLower, "shard") or string.find(nameLower, "crystal") or string.find(nameLower, "galaxy") or string.find(nameLower, "token") then
                    if coin:IsA("BasePart") then
                        esMonedaEvento = true
                    end
                end
                
                if esMonedaEvento then
                    -- Localizar la posición física exacta de la moneda
                    local coinCFrame = coin.CFrame
                    if coinCFrame then
                        -- Forzar el Instant TP alterando directamente los vectores del personaje
                        Root.Velocity = Vector3.new(0, 0, 0) -- Cancela cualquier caída anterior para no bugearse
                        Root.CFrame = coinCFrame
                        task.wait(0.12) -- Breve tiempo de retraso para asegurar que el cliente absorbió el fragmento
                    end
                end
                -- Cortar el bucle inmediatamente si apagas el botón durante el viaje
                if not AutoCollectTPActive then break end
            end
        end
    end
end)

-- ==========================================
-- BOTÓN 2: AUTO-ATTACK CON EL BATE DE COMBATE
-- ==========================================
CreateCyanButton("⚔️ AUTO-ATTACK: OFF", function(btn, stroke)
    AutoAttackActive = not AutoAttackActive
    if AutoAttackActive then
        btn.Text = "🔥 ATTACK: ACTIVO"
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.BackgroundColor3 = Color3.fromRGB(0, 90, 110)
        stroke.Color = Color3.fromRGB(255, 255, 255)
    else
        btn.Text = "⚔️ AUTO-ATTACK: OFF"
        btn.TextColor3 = Color3.fromRGB(0, 240, 255)
        btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        stroke.Color = Color3.fromRGB(0, 240, 255)
    end
end)

task.spawn(function()
    while true do
        task.wait(0.08) -- Alta velocidad de clics continuos
        if AutoAttackActive and Player.Character then
            -- Equipar y activar de forma inmediata el bate o raqueta de tu mochila
            local Weapon = Player.Backpack:FindFirstChildOfClass("Tool") or Player.Character:FindFirstChildOfClass("Tool")
            if Weapon then
                Player.Character.Humanoid:EquipTool(Weapon)
                Weapon:Activate() -- Golpea de forma continua
            end
        end
    end
end)
