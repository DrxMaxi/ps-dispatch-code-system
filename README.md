# ps-dispatch-code-system
Türkçe:
Merhaba, bir çok kişinin yapamadığını ve talep ettiklerini gördüğüm bir rehberi paylaşıyorum. Kodlamam biraz karmaşık olabilir kusuruma bakmayın.

<details>

<summary> Eski ps-dispatch sistemini kullanıyorsanız: </summary>

ps-dispatch/server/sv_dispatchcodes.lua En alt kısmına;
```lua
  ["kod0"] =  {displayCode = 'KOD 0', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod1"] =  {displayCode = 'KOD 1', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod2"] =  {displayCode = 'KOD 2', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod3"] =  {displayCode = 'KOD 3', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod4"] =  {displayCode = 'KOD 4', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod5"] =  {displayCode = 'KOD 5', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod6"] =  {displayCode = 'KOD 6', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod7"] =  {displayCode = 'KOD 7', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod8"] =  {displayCode = 'KOD 8', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod9"] =  {displayCode = 'KOD 9', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["kod99"] =  {displayCode = 'KOD 99', description = "", radius = 0, recipientList = {'police'}, blipSprite = 480, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
```

ps-dispatch/client/cl_commands.lua 61. satır;

```lua
RegisterCommand('kod', function(source, args, rawCommand)
    -- if not QBCore.Functions.PlayerData().job.name == "police" then return end
    if QBCore.Functions.HasItem('gps', 1) then
    local msg = rawCommand:sub(5)
    if string.len(msg) > 0 then
        if not exports['qb-policejob']:IsHandcuffed() then
            local plyData = QBCore.Functions.GetPlayerData()
            local currentPos = GetEntityCoords(PlayerPedId())
            local locationInfo = getStreetandZone(currentPos)
            local gender = GetPedGender()
            TriggerServerEvent("dispatch:server:notify",{
                dispatchcodename = "kod"..msg, -- has to match the codes in sv_dispatchcodes.lua so that it generates the right blip
                dispatchCode = plyData.charinfo.firstname:sub(1,1):upper()..plyData.charinfo.firstname:sub(2).. " ".. plyData.charinfo.lastname:sub(1,1):upper()..plyData.charinfo.lastname:sub(2),
                firstStreet = locationInfo,
                priority = 2, -- priority
                name = plyData.charinfo.firstname:sub(1,1):upper()..plyData.charinfo.firstname:sub(2).. " ".. plyData.charinfo.lastname:sub(1,1):upper()..plyData.charinfo.lastname:sub(2),
                number = plyData.charinfo.phone,
                origin = {
                    x = currentPos.x,
                    y = currentPos.y,
                    z = currentPos.z
                },
                  dispatchMessage = "KOD "..msg, -- message
                 job = {"police", "ambulance"} -- jobs that will get the alerts
            })
        else
            QBCore.Functions.Notify("Kelepçelisin Kod Atamazsın!", "error", 4500)
        end
    else
        QBCore.Functions.Notify('Kod Levelinizi Belirleyin örn. /kod 99', "success")
    end
else 
QBCore.Functions.Notify('Üzerinde GPS Yok!')
end
end)
```
</details>

<details>

<summary>Yeni ps-dispatch Sistemini kullanıyorsanız:</summary>

ps-dispatch/shared/config.lua 381. satırı silip yerine bunu yapıştırıyoruz; 

```lua
    ['explosion'] = {
        radius = 0,
        sprite = 436,
        color = 1,
        scale = 1.5,
        length = 2,
        sound = 'Lose_1st',
        sound2 = 'GTAO_FM_Events_Soundset',
        offset = false,
        flash = false
    },
    ['kod'] = {
        radius = 0,
        sprite = 480,
        color = 1,
        scale = 1.5,
        length = 2,
        sound = 'Lose_1st',
        sound2 = 'GTAO_FM_Events_Soundset',
        offset = false,
        flash = false
    }
```

ps-dispath/client/main.lua en alt satıra;

```lua
RegisterCommand('kod', function(source, args, rawCommand)
    -- if not QBCore.Functions.PlayerData().job.name == "police" then return end
    if QBCore.Functions.HasItem('gps', 1) then
        local msg = rawCommand:sub(5)
        local coords = GetEntityCoords(cache.ped)
        local plyData = QBCore.Functions.GetPlayerData()
        local dispatchData = {
            message = 'Kod '..msg,
            name = plyData.charinfo.firstname:sub(1,1):upper()..plyData.charinfo.firstname:sub(2).. " ".. plyData.charinfo.lastname:sub(1,1):upper()..plyData.charinfo.lastname:sub(2),
            codeName = 'kod',
            code = 'Acil Durum',
            icon = 'fa-solid fa-heart',
            priority = 2,
            displayCode = "Kod Level "..msg,
            description = "31",
            coords = coords,
            gender = GetPlayerGender(),
            street = GetStreetAndZone(coords),
            jobs = { 'leo' }
        }
    if string.len(msg) > 0 then
        if not exports['qb-policejob']:IsHandcuffed() then
            TriggerServerEvent("ps-dispatch:server:notify", dispatchData)
        else
            QBCore.Functions.Notify("Kelepçelisin Kod Atamazsın!", "error", 4500)
        end
    else
        QBCore.Functions.Notify('Kod Levelinizi Belirleyin örn. /kod 99', "success")
    end
else
QBCore.Functions.Notify('Üzerinde GPS Yok!')
end
end)
```

</details>


### Eğer GPS zorunluluğu istemiyorsanız şu satırları silin ve şunları açın;
Silinenler:
```lua
if QBCore.Functions.HasItem('gps', 1) then

else
QBCore.Functions.Notify('Üzerinde GPS Yok!')
end
```
