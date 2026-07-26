---
description: >-
  This is a guide designed to lead you through the successful framework
  integration process of the script. Please follow the steps and do not deviate
  from the instructions.
---

# Custom Framework Integration

## Custom

Don't run QBCore, Qbox, or ESX? `inn-laundry` never calls a framework's own
API directly — every part of the script only ever calls six functions on a
`Framework` table, defined once per framework under `bridge/framework/`.
Implementing a custom framework means filling in those six functions
yourself.

{% stepper %}
{% step %}
### Open the custom framework template

Open <kbd>inn-laundry/bridge/framework/custom.lua</kbd>. It already contains
every function signature you need, each currently just raising an error so
you can't accidentally ship it unfinished.
{% endstep %}

{% step %}
### Implement each function

| Function | Returns | Purpose |
|---|---|---|
| `Framework.GetPlayer(src)` | opaque player object, or `nil` if offline | Looked up once and reused — the shape of this object is entirely up to you, nothing outside your own bridge file reads it directly. |
| `Framework.GetIdentifier(src)` | `string` identifier, or `nil` | Stored as the owner on zones/machines — must be stable across sessions (e.g. a license or citizenid), not the temporary server id. |
| `Framework.GetJob(src)` | `{ name, grade, isboss }`, or `nil` | `isboss` marks the player as the automatic owner of a job-locked zone on that job. |
| `Framework.AddMoney(src, amount, reason, moneyType)` | `boolean` success | `moneyType` is `'cash'` or `'bank'`, falling back to `Config.Payout.moneyType` when not given. |
| `Framework.RemoveMoney(src, amount, reason, moneyType)` | `boolean` success | Must fail (return `false`) and remove nothing if the player doesn't have enough. |
| `Framework.GetPlayersOnJob(job)` | `number[]` of source ids | Used to find every online player to notify for a job (e.g. police during a raid). |

{% hint style="info" %}
`Framework.HasPermission(src)` also exists on the `Framework` table, but
it's implemented once in `bridge/framework/init.lua` using FiveM's native
ace permissions (`IsPlayerAceAllowed`) — it's the same for every framework,
so you don't need to (and shouldn't) implement it yourself.
{% endhint %}
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.Framework = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Reference implementation

Every function above is implemented for QBCore in
`bridge/framework/qb.lua` — it's a useful reference even if your framework
isn't QBCore-based, since the shape of what each function needs to return
is identical:

```lua
local QBCore = exports['qb-core']:GetCoreObject()

function Framework.GetPlayer(src)
    return QBCore.Functions.GetPlayer(src)
end

function Framework.GetIdentifier(src)
    local Player = QBCore.Functions.GetPlayer(src)
    return Player and Player.PlayerData.citizenid or nil
end

function Framework.GetJob(src)
    local Player = QBCore.Functions.GetPlayer(src)
    if not Player then return nil end

    return {
        name = Player.PlayerData.job.name,
        grade = Player.PlayerData.job.grade.level,
        isboss = Player.PlayerData.job.isboss or false,
    }
end
```

{% hint style="warning" %}
ESX has no built-in "boss" concept the way QBCore/Qbox do. If your custom
framework is ESX-like, don't hardcode `isboss = false` — `inn-laundry`
falls back to `Config.ESXBossGrades` (in `config/config.lua`) to decide the
automatic owner grade per job in that case. Match that same pattern in your
custom bridge, or every job-locked zone will start with no automatic owner
at all.
{% endhint %}
