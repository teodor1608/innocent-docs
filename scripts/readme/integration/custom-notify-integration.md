---
description: >-
  This is a guide designed to lead you through the successful notify integration
  process of the script. Please follow the steps and do not deviate from the
  instructions.
---

# Custom Notify Integration

## Custom

`inn-laundry` never calls a notification resource's own API directly —
every toast/popup only ever calls a single function on a client-side
`Notify` table, defined once per notify system under `bridge/notify/`.

{% hint style="info" %}
This is the toast/notification popup bridge only. For the separate
on-screen `[E] interact` prompt, see
[Custom Text UI Integration](custom-text-ui-integration.md).
{% endhint %}

{% stepper %}
{% step %}
### Open the custom notify template

Open <kbd>inn-laundry/bridge/notify/custom.lua</kbd>.
{% endstep %}

{% step %}
### Implement the function

| Function | Purpose |
|---|---|
| `Notify.Show(data)` | `data` is `{ description, title?, type }`, where `type` is one of `'inform'`, `'success'`, `'error'`, `'warning'`. |
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.Notify = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Reference implementation

`inn-laundry`'s own `type` values are ox\_lib's, so bridging to a
notification system with a different set of types is mostly a small lookup
table. `bridge/notify/qb.lua`:

```lua
-- Our type values (ox_lib's) -> QBCore.Functions.Notify's
-- ('success' | 'error' | 'primary' | 'warning' | 'info').
local TYPE_MAP = {
    inform = 'primary',
    success = 'success',
    error = 'error',
    warning = 'warning',
}

function Notify.Show(data)
    QBCore.Functions.Notify(data.description, TYPE_MAP[data.type] or 'primary')
end
```

and `bridge/notify/esx.lua`, the same pattern against a different target
system with fewer distinct types:

```lua
-- Our type values (ox_lib's) -> ESX.ShowNotification's ('success' | 'error' | 'info').
local TYPE_MAP = {
    inform = 'info',
    success = 'success',
    error = 'error',
    warning = 'info',
}

function Notify.Show(data)
    ESX.ShowNotification(data.description, TYPE_MAP[data.type] or 'info')
end
```

{% hint style="info" %}
Unlike the other bridges, `Config.Notify = 'auto'` falls back to `'ox'`
rather than `'custom'` if nothing else is detected — ox\_lib is already a
hard dependency of this whole resource, so it's always a safe default.
Setting `'custom'` explicitly always wins over that fallback.
{% endhint %}
