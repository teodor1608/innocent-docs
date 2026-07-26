---
description: >-
  This is a guide designed to lead you through the successful target integration
  process of the script. Please follow the steps and do not deviate from the
  instructions.
---

# Custom Target Integration

## Custom

Don't run ox\_target or qb-target? `inn-laundry` never calls a target
resource's own API directly — every interaction only ever calls four
functions on a client-side `Target` table, defined once per target system
under `bridge/target/`.

{% hint style="info" %}
Target resources are typically integrated purely via
`exports['your-target']:FunctionName(...)` calls — both ox\_target and
qb-target work this way (see the reference implementation below), with no
extra object to fetch first.
{% endhint %}

{% hint style="info" %}
A target resource isn't strictly required at all — machines, the supplier
NPC, the generator, and shell entry/exit each have their own `useTarget`
config toggle. Set them all to `false` and you can skip this integration
entirely, using a simple `[E]`-prompt instead (see
[Custom Text UI Integration](custom-text-ui-integration.md) for that
prompt's own bridge).
{% endhint %}

{% stepper %}
{% step %}
### Open the custom target template

Open <kbd>inn-laundry/bridge/target/custom.lua</kbd>. It already contains
every function signature you need, each currently just raising an error so
you can't accidentally ship it unfinished.
{% endstep %}

{% step %}
### Implement each function

| Function | Purpose |
|---|---|
| `Target.AddTargetEntity(entity, options)` | Adds interaction options to a specific client-side entity. `options` is an array of `{ label, icon, distance, onSelect = function(entity) end, canInteract = function(entity) end }`. |
| `Target.RemoveTargetEntity(entity)` | Removes every option previously added to that entity. |
| `Target.AddTargetSphere(name, coords, radius, options)` | Adds a coordinate-based (not entity-tied) targetable zone. `name` is globally unique — that's the caller's responsibility, not yours. `options` is an array of `{ label, icon, onSelect = function() end, canInteract = function() end }`. |
| `Target.RemoveTargetSphere(name)` | Removes the zone previously created with that same `name`. |
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.Target = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Reference implementation

Every function above is implemented for ox\_target in
`bridge/target/oxtarget.lua`:

```lua
local entityOptions = {}

function Target.AddTargetEntity(entity, options)
    local mapped = {}
    local names = {}

    for i, opt in ipairs(options) do
        local name = ('inn-laundry_%s_%d'):format(entity, i)
        mapped[i] = {
            name = name,
            icon = opt.icon,
            label = opt.label,
            distance = opt.distance,
            onSelect = opt.onSelect,
            canInteract = opt.canInteract,
        }
        names[i] = name
    end

    entityOptions[entity] = names
    exports.ox_target:addLocalEntity(entity, mapped)
end

function Target.RemoveTargetEntity(entity)
    local names = entityOptions[entity]
    if not names then return end

    exports.ox_target:removeLocalEntity(entity, names)
    entityOptions[entity] = nil
end
```

{% hint style="warning" %}
Most target resources require a unique `name` per option, but
`inn-laundry` never generates one for you — as shown above, the ox\_target
bridge builds its own per-entity/per-option names internally and tracks
them so `RemoveTargetEntity` can clean them up correctly. Do the same in
your custom bridge rather than expecting `options` to already contain a
`name` field.
{% endhint %}
