-- ServerScript que gerencia o fluxo completo da dungeon
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local portalService = ReplicatedStorage:WaitForChild("PortalService")
local dungeonService = ReplicatedStorage:WaitForChild("DungeonService")

-- Função para iniciar a dungeon
local function startDungeon()
    print("Iniciando a Dungeon...")

    -- Etapa 1: Abrir o Portal (PortalTierIII)
    local portalArgs = { "PortalTierIII", 1 }
    local success, result = pcall(function()
        print("Tentando ativar o Portal Tier III...")
        portalService.RF.request_item_consumption:InvokeServer(unpack(portalArgs))
    end)

    if success then
        print("Portal Tier III ativado!")
    else
        print("Falha ao ativar o Portal Tier III:", result)
        return
    end

    wait(3)  -- Aguardar um pouco para o portal abrir

    -- Etapa 2: Pular o tempo de espera para começar a dungeon
    local successSkip, resultSkip = pcall(function()
        print("Tentando pular o tempo de espera...")
        portalService.RF.skip_portal:InvokeServer()
    end)

    if successSkip then
        print("Tempo de espera pulado, dungeon iniciada!")
    else
        print("Falha ao pular o tempo de espera:", resultSkip)
        return
    end

    wait(3)  -- Aguardar mais um pouco para garantir que a ação tenha ocorrido corretamente

    -- Etapa 3: Invocar mobs (primeira wave)
    local pillarUUID = "d568ba3f-30f6-4847-b168-16827e1d1fd9"  -- Exemplo de UUID do pilar (substitua pelo correto)
    local successWave, resultWave = pcall(function()
        print("Tentando invocar os mobs...")
        dungeonService.RF.spawn_wave:InvokeServer(pillarUUID)
    end)

    if successWave then
        print("Mobs da primeira wave invocados!")
    else
        print("Falha ao invocar mobs:", resultWave)
        return
    end

    wait(5)  -- Simulando o tempo para derrotar a wave de mobs

    -- Etapa 4: Matar os mobs e abrir a próxima porta da dungeon
    print("Mobs derrotados! Abrindo próxima porta...")
    local nextDoorUUID = "d568ba3f-30f6-4847-b168-16827e1d1fd9"  -- UUID da próxima porta (substitua pelo correto)
    local successDoor, resultDoor = pcall(function()
        print("Tentando abrir a próxima porta...")
        dungeonService.RF.open_door:InvokeServer(nextDoorUUID)
    end)

    if successDoor then
        print("Próxima porta aberta!")
    else
        print("Falha ao abrir a próxima porta:", resultDoor)
        return
    end

    wait(3)  -- Aguardar para garantir que a porta seja aberta corretamente

    -- Repetir o processo de invocar mobs, derrotar e abrir portas até dropar a chave do Boss
    wait(3)

    -- Etapa 5: Invocar a chave do Boss
    local bossKeyUUID = "bossKeyUUID"  -- Substitua pelo UUID real da chave do Boss
    local successKey, resultKey = pcall(function()
        print("Tentando invocar a chave do Boss...")
        dungeonService.RF.spawn_boss_key:InvokeServer(bossKeyUUID)
    end)

    if successKey then
        print("Chave do Boss Droppada!")
    else
        print("Falha ao invocar a chave do Boss:", resultKey)
        return
    end

    wait(2)

    -- Etapa 6: Derrotar o Boss
    local bossUUID = "bossUUID"  -- Substitua pelo UUID do Boss
    local successBoss, resultBoss = pcall(function()
        print("Tentando derrotar o Boss...")
        dungeonService.RF.defeat_boss:InvokeServer(bossUUID)
    end)

    if successBoss then
        print("Boss derrotado!")
    else
        print("Falha ao derrotar o Boss:", resultBoss)
        return
    end

    -- Etapa 7: Recompensar o jogador após derrotar o Boss
    -- Aqui você pode adicionar código para recompensar o jogador, como dar itens ou moedas.
    -- Exemplo:
    local player = game.Players:GetPlayerByUserId(playerUserId)  -- Substitua com o UserId do jogador que completou a dungeon
    if player then
        player.leaderstats.Rewards.Value = player.leaderstats.Rewards.Value + 100  -- Adicionando recompensa
        print("Jogador recompensado com 100 moedas!")
    end
end

-- Disparar o fluxo de execução da dungeon automaticamente ao iniciar o jogo
startDungeon()
