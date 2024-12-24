-- Inicializando a biblioteca
local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

-- Criando uma janela
local Window = OrionLib:MakeWindow({Name = "Orion Gateway", HidePremium = false, SaveConfig = true, ConfigFolder = "SaveConfigOrion"})

OrionLib:MakeNotification({
	Name = "Bem-vindo ao Orion Gateway!",
	Content = "Explore as opções disponíveis e aproveite a experiência!",
	Image = "rbxassetid://4483345998",
	Time = 5
})

-- Criando uma aba
local Tab = Window:MakeTab({
    Name = "Orion Gateway",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local Section = Tab:AddSection({
	Name = "Places Manager"
})

-- Serviços necessários
local AssetService = game:GetService("AssetService")
local TeleportService = game:GetService("TeleportService")
local Players = game:GetService("Players")

-- Variável para armazenar a place selecionada
local selectedPlace = nil

-- Função para obter todas as places e subplaces
local function getAllPlaces()
    local places = {}
    local pages = AssetService:GetGamePlacesAsync()
    while true do
        for _, place in pairs(pages:GetCurrentPage()) do
            table.insert(places, {Name = place.Name, Id = place.PlaceId})
        end
        if pages.IsFinished then
            break
        end
        pages:AdvanceToNextPageAsync()
    end
    return places
end

-- Lista de places
local places = getAllPlaces()

-- Configuração do menu suspenso
local dropdownOptions = {}
for _, place in ipairs(places) do
    table.insert(dropdownOptions, place.Name .. " [" .. place.Id .. "]")
end

-- Criando o menu suspenso
Tab:AddDropdown({
    Name = "Selecione uma Place",
    Default = dropdownOptions[1] or "Nenhuma Place Encontrada",
    Options = dropdownOptions,
    Callback = function(Value)
        -- Atualiza a place selecionada
        for _, place in ipairs(places) do
            if Value == (place.Name .. " [" .. place.Id .. "]") then
                selectedPlace = place
                break
            end
        end
    end    
})

-- Botão: Copiar o nome da place selecionada
Tab:AddButton({
    Name = "Copiar Nome!",
    Callback = function()
        if selectedPlace then
            setclipboard(selectedPlace.Name) -- Copia o nome para a área de transferência
            print("Nome copiado: " .. selectedPlace.Name)
        else
            print("Nenhuma place selecionada.")
        end
    end    
})

-- Botão: Copiar o ID da place selecionada
Tab:AddButton({
    Name = "Copiar ID!",
    Callback = function()
        if selectedPlace then
            setclipboard(tostring(selectedPlace.Id)) -- Copia o ID para a área de transferência
            print("ID copiado: " .. selectedPlace.Id)
        else
            print("Nenhuma place selecionada.")
        end
    end    
})

-- Botão: Teleportar para a place selecionada
Tab:AddButton({
    Name = "Ir!",
    Callback = function()
        if selectedPlace then
            local player = Players.LocalPlayer
            if player then
                TeleportService:Teleport(selectedPlace.Id, player) -- Teleporta o jogador
                print("Teleportando para: " .. selectedPlace.Name)
            else
                print("Jogador local não encontrado.")
            end
        else
            print("Nenhuma place selecionada.")
        end
    end    
})

-- Inicializando a interface
OrionLib:Init()
