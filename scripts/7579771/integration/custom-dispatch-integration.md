---
description: >-
  This is a guide designed to lead you through the successful dispatch
  integration process of the script. Please follow the steps and do not deviate
  from the instructions.
---

# Custom Dispatch Integration

## Custom

`inn-laundry` never calls a dispatch resource's own API directly — a
triggered raid only ever calls a single function on a server-side
`Dispatch` table, defined once per dispatch resource under
`bridge/dispatch/`.

{% hint style="info" %}
A third-party dispatch resource isn't required at all. `Config.Dispatch =
'custom'` (or auto-detection finding neither `ps-dispatch` nor
`cd_dispatch`) falls back to `bridge/dispatch/custom.lua`'s default
behaviour: notifying every online player on a `Config.Police` job directly
via this script's own client notify event — no third-party dispatch
resource required. You only need this guide if you want to route raid
alerts through a **different** dispatch resource than ps-dispatch/cd\_dispatch.
{% endhint %}

{% stepper %}
{% step %}
### Open the custom dispatch template

Open <kbd>inn-laundry/bridge/dispatch/custom.lua</kbd>.
{% endstep %}

{% step %}
### Implement the function

| Function | Purpose |
|---|---|
| `Dispatch.SendRaidAlert(coords, businessLabel)` | Called once per triggered raid (one call per raided zone, not per machine). Notify your dispatch resource however it expects. |

{% hint style="info" %}
How you notify your dispatch resource depends entirely on what it exposes —
some (like `cd_dispatch` below) listen for a `TriggerClientEvent`/
`TriggerServerEvent`, others expose an `exports['your-dispatch']:SomeFunction(...)`
call instead. Check your dispatch resource's own documentation for which
one it wants.
{% endhint %}
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.Dispatch = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Reference implementation

`bridge/dispatch/cddispatch.lua` shows the shape of a typical event-based
integration — a single client event with a payload built from
`Config.Police.jobs` and the raided zone's coordinates/label:

```lua
function Dispatch.SendRaidAlert(coords, businessLabel)
    TriggerClientEvent('cd_dispatch:AddNotification', -1, {
        job_table = Config.Police.jobs,
        coords = coords,
        title = '10-35 - Suspicious Activity',
        message = businessLabel,
        flash = 0,
        unique_id = tostring(math.random(0, 9999999)),
        sound = 1,
        blip = {
            sprite = 431,
            scale = 1.2,
            colour = 3,
            flashes = false,
            text = businessLabel,
            time = 5,
            radius = 0,
        },
    })
end
```

If your dispatch resource instead expects an export call, the shape is
just as simple — for example:

```lua
function Dispatch.SendRaidAlert(coords, businessLabel)
    exports['your-dispatch']:CreateAlert({
        code = '10-35',
        message = businessLabel,
        coords = coords,
        jobs = Config.Police.jobs,
    })
end
```

{% hint style="warning" %}
`bridge/dispatch/psdispatch.lua` needs one extra step beyond the Lua
itself: ps-dispatch dispatches by a `codeName` that must exist in **its
own** `config.lua`, not `inn-laundry`'s. If your custom dispatch resource
has a similar "registered alert type" concept, you'll likely need the same
kind of one-time setup on that resource's side too — check its own docs.
{% endhint %}
