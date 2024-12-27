-- GG HUB [BETA] Script para Murder Mystery 2

-- Criar a interface do Hub
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local OpenCloseButton = Instance.new("TextButton")
local ESPButton = Instance.new("TextButton")

-- Configurar a interface
ScreenGui.Name = "GGHub"
ScreenGui.Parent = game.CoreGui

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
MainFrame.Position = UDim2.new(0.5, -200, 0.5, -150)
MainFrame.Size = UDim2.new(0, 400, 0, 300)
MainFrame.Visible = false

OpenCloseButton.Name = "OpenCloseButton"
OpenCloseButton.Parent = ScreenGui
OpenCloseButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
OpenCloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenCloseButton.Position = UDim2.new(0, 10, 0, 10)
OpenCloseButton.Size = UDim2.new(0, 100, 0, 50)
OpenCloseButton.Text = "Abrir GG HUB"

ESPButton.Name = "ESPButton"
ESPButton.Parent = MainFrame
ESPButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
ESPButton.Position = UDim2.new(0, 10, 0, 10)
ESPButton.Size = UDim2.new(0, 100, 0, 50)
ESPButton.Text = "ESP Jogadores"

-- Função para abrir e fechar o Hub
OpenCloseButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Função para criar o ESP
local function createESP(player, roleColor)
    if not player.Character then return end

    local highlight = Instance.new("Highlight")
    highlight.Parent = player.Character
    highlight.Adornee = player.Character
    highlight.FillColor = roleColor
    highlight.OutlineColor = Color3.new(1, 1, 1) -- Contorno branco
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
end

-- Função para remover o ESP
local function removeESP(player)
    if player.Character and player.Character:FindFirstChild("Highlight") then
        player.Character.Highlight:Destroy()
    end
end

-- Função para aplicar o ESP baseado no papel do jogador
local function applyESP(player)
    local role = getPlayerRole(player) -- Implementar esta função conforme a lógica do jogo
    if role == "Murderer" then
        createESP(player, Color3.new(1, 0, 0)) -- Vermelho para Murderer
    elseif role == "Sheriff" then
        createESP(player, Color3.new(0, 0, 1)) -- Azul para Sheriff
    else
        createESP(player, Color3.new(0, 1, 0)) -- Verde para Inocente
    end
end

-- Função para lidar com novos jogadores
local function onPlayerAdded(player)
    if player.Character then
        applyESP(player)
    end

    player.CharacterAdded:Connect(function()
        applyESP(player)
    end)

    player.CharacterRemoving:Connect(function()
        removeESP(player)
    end)
end

-- Função para lidar com jogadores saindo do jogo
local function onPlayerRemoving(player)
    removeESP(player)
end

-- Adicionar ESP aos jogadores existentes
for _, player in pairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        onPlayerAdded(player)
    end
end

-- Conectar eventos PlayerAdded e PlayerRemoving
game.Players.PlayerAdded:Connect(function(player)
    if player ~= game.Players.LocalPlayer then
        onPlayerAdded(player)
    end
end)

game.Players.PlayerRemoving:Connect(onPlayerRemoving)

-- Placeholder para a função que determina o papel do jogador
function getPlayerRole(player)
    -- Esta função deve retornar o papel do jogador: "Murderer", "Sheriff" ou "Innocent"
    -- Você precisa implementar isso baseado na lógica específica do jogo
    return "Innocent" -- Padrão para Inocente neste exemplo
end

-- Conectar a função ESP ao botão
ESPButton.MouseButton1Click:Connect(function()
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            applyESP(player)
        end
    end
end)

