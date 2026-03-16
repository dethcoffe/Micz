# Micz
Wehbook fishit
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local webhook = "https://discord.com/api/webhooks/1482401763477815501/QYeCSbvu0m6KixtOsZBIaUYAnK3ckqsgYwEnMsFfl2sg1L0blPotSlRDHYKzBgQQZhR6"

local cooldown = false

local function SendWebhook(data)

    if cooldown then return end
    cooldown = true

    request({
        Url = webhook,
        Method = "POST",
        Headers = {
            ["Content-Type"] = "application/json"
        },
        Body = HttpService:JSONEncode(data)
    })

    task.wait(2)
    cooldown = false

end


local function SendFish(player, fish)

    if fish.Tier ~= "Secret" and fish.Tier ~= "Forgotten" then
        return
    end

    local shiny = "No"
    if fish.Shiny == true then
        shiny = "Yes ✨"
    end

    local data = {

        ["username"] = "Fish Logger",

        ["embeds"] = {{
            ["title"] = "Rare Fish Notification",
            ["color"] = 16753920,

            ["thumbnail"] = {
                ["url"] = fish.Image
            },

            ["fields"] = {

                {
                    ["name"] = "Username",
                    ["value"] = player.Name,
                    ["inline"] = true
                },

                {
                    ["name"] = "Fish Name",
                    ["value"] = fish.Name,
                    ["inline"] = true
                },

                {
                    ["name"] = "Shiny",
                    ["value"] = shiny,
                    ["inline"] = true
                },

                {
                    ["name"] = "Mutation",
                    ["value"] = fish.Mutation or "-",
                    ["inline"] = true
                },

                {
                    ["name"] = "Weight",
                    ["value"] = tostring(fish.Weight).." kg",
                    ["inline"] = true
                },

                {
                    ["name"] = "Rarity",
                    ["value"] = fish.Rarity,
                    ["inline"] = true
                },

                {
                    ["name"] = "Tier",
                    ["value"] = fish.Tier,
                    ["inline"] = true
                },

                {
                    ["name"] = "Server ID",
                    ["value"] = game.JobId
                }

            }
        }}

    }

    SendWebhook(data)

end


-- contoh event tangkapan (perlu disesuaikan dengan game)
local event = ReplicatedStorage:FindFirstChild("FishCaught")

if event then

    event.OnClientEvent:Connect(function(player, fishData)

        SendFish(player,{
            Name = fishData.Name,
            Shiny = fishData.Shiny,
            Mutation = fishData.Mutation,
            Weight = fishData.Weight,
            Rarity = fishData.Rarity,
            Tier = fishData.Tier,
            Image = fishData.Image
        })

    end)

end
