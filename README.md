## vRP_eclipse_CC

## Requirements
- [vrp](https://github.com/DunkoUK/dunko_vrp) (Tested only with Dunko vRP 8.2. Other version use on your own risk)
- [vrp_barbershop](https://github.com/DunkoUK/dunko_vrp/tree/additonals/vrp_barbershop)


put Eclipse_CC in your resource directory

Setting config `resources\eclipse_CC\config\config.json`

  Change spawn position 
 
  
  ![image](https://user-images.githubusercontent.com/36680471/142732241-82eb0ee1-b1f4-488e-97ad-9d2fa3489e63.png)

  
## Installation:

Resource `vrp` 
File `base.lua`
```lua 
MySQL.createCommand("vRP/set_face_fueature_data","REPLACE INTO vrp_user_data(user_id,dkey,dvalue) VALUES(@user_id,@key,@value)")
```
![image](https://user-images.githubusercontent.com/36680471/142732372-9c1ff446-8602-45b7-b4a7-68f5819cdef6.png)
_________________________________________________________________________________________________________________________________________________
```lua
function vRP.setFaceFeatureData(user_id,key,value)
    MySQL.execute("vRP/set_face_fueature_data", {user_id = user_id, key = key, value = value})
end
```

![image](https://user-images.githubusercontent.com/36680471/142732333-8a0426a8-d86d-4138-a606-589b89269b26.png)

_________________________________________________________________________________________________________________________________________________
File `vrp/client/player_state.lua`

```lua
-- eclipse cc

function tvRP.setFaceFeatureCustomization(custom) -- indexed [drawable,texture,palette] components or props (p0...) plus .modelhash or .model
  local exit = TUNNEL_DELAYED() -- delay the return values

  Citizen.CreateThread(function() -- new thread
    if custom then
      local ped = GetPlayerPed(-1)
      for k,v in pairs(custom) do
        local isprop, index = parse_part(k)
        SetPedFaceFeature(ped, index, v[1])
      end
    end
    exit({})
  end)
end

function tvRP.setServerFaceFeatureCustomization(custom) -- indexed [drawable,texture,palette] components or props (p0...) plus .modelhash or .model
  local exit = TUNNEL_DELAYED() -- delay the return values

  Citizen.CreateThread(function() -- new thread
    if custom then
      local ped = GetPlayerPed(-1)
      for k,v in pairs(custom) do
        local isprop, index = parse_part(k)
        SetPedFaceFeature(ped, index, v)
      end
    end
    exit({})
  end)
end


function tvRP.getFaceFeature()
  local ped = GetPlayerPed(-1)

  local custom = {}
  for i=0,19 do -- index limit to 10
    custom[i] = GetPedFaceFeature(ped, i)
  end
  return custom
end

RegisterNetEvent("vRP:setCustomization")
AddEventHandler("vRP:setCustomization", function(data)
    custom = tvRP.getCustomization()
    tvRP.setCustomization(custom)
    vRPserver.updateCustomization(custom)
end)

RegisterNetEvent("vRP:setFaceFeature")
AddEventHandler("vRP:setFaceFeature", function(data) 
  customFaceFeature = json.decode(data)
  tvRP.setFaceFeatureCustomization(customFaceFeature)
  --vRPserver.updateFaceFeature({json.decode(data)})
  vRPserver.setFaceFeature({json.decode(data)})
  --vRPserver.setFaceFeature(customFaceFeature)
  
end)

RegisterNetEvent("vRP:getFaceFeature")
AddEventHandler("vRP:getFaceFeature", function(data) 
   customFaceFeature = tvRP.getFaceFeature()
end)

-- 
```
_________________________________________________________________________________________________________________________________________________
Lines 23 add this code

```lua
 vRPserver.updateFaceFeature({tvRP.getFaceFeature()})
```

![image](https://user-images.githubusercontent.com/36680471/142732442-e6f91b36-8b5b-477a-8f45-bb3433ea868e.png)

_________________________________________________________________________________________________________________________________________________
File `vrp/client/player_state.lua`

```lua 
-- eclipse cc
function tvRP.updateFaceFeature(customization)
    local user_id = vRP.getUserId(source)
    if user_id ~= nil then
        vRP.getUData(user_id, "vRP:faceFeature", function(sdata)
            local data = json.decode(sdata)
            if data ~= nil then
                vRP.setFaceFeatureData(user_id, "vRP:faceFeature", json.encode(customization))
            end 
        end)   	
    end
end

function tvRP.setFaceFeature(customization)
    local user_id = vRP.getUserId(source)
    if user_id ~= nil then
        vRP.setFaceFeatureData(user_id, "vRP:faceFeature", json.encode(customization))
    end
end
```
_________________________________________________________________________________________________________________________________________________
Comment lines 13-15

![image](https://user-images.githubusercontent.com/36680471/142732503-99e5f01c-9477-4f1e-a9ea-a799af00c47e.png)
_________________________________________________________________________________________________________________________________________________
Add 74-76

```lua
if data.customization == nil then
            TriggerClientEvent("ECLIPSE:OpenCharacterCreatioMenu", source)
end
```

![image](https://user-images.githubusercontent.com/36680471/142732527-881a6e95-31ba-403c-ba1a-11cd80b8b4c3.png)

_________________________________________________________________________________________________________________________________________________


For more questions https://discord.gg/uh72PyZrmc




