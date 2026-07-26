---
description: >-
  This is a guide designed to lead you through the successful text ui
  integration process of the script. Please follow the steps and do not deviate
  from the instructions.
---

# Custom Text UI Integration

## Custom

`inn-laundry` never calls a text UI resource's own API directly — the
on-screen `[E] interact` prompt used by every `useTarget = false` fallback
only ever calls two functions on a client-side `TextUI` table, defined once
per text UI system under `bridge/textui/`.

{% hint style="info" %}
This is the on-screen prompt bridge only. For the separate toast/
notification popups, see
[Custom Notify Integration](custom-notify-integration.md).
{% endhint %}

{% stepper %}
{% step %}
### Open the custom text UI template

Open <kbd>inn-laundry/bridge/textui/custom.lua</kbd>.
{% endstep %}

{% step %}
### Implement each function

| Function | Purpose |
|---|---|
| `TextUI.Show(text)` | Displays the on-screen prompt with the given text. |
| `TextUI.Hide()` | Hides it. |
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.TextUI = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Reference implementation

`bridge/textui/qb.lua`, wrapping QBCore's built-in `DrawText`:

```lua
function TextUI.Show(text)
    exports['qb-core']:DrawText(text, 'left')
end

function TextUI.Hide()
    exports['qb-core']:HideText()
end
```

and `bridge/textui/ox.lua`, wrapping ox\_lib's equivalent:

```lua
function TextUI.Show(text)
    lib.showTextUI(text)
end

function TextUI.Hide()
    lib.hideTextUI()
end
```

{% hint style="info" %}
No ESX option ships by default — ESX has no built-in on-screen prompt
system of its own to bridge to (unlike QBCore's `DrawText`), so ESX servers
get the `'ox'` fallback for `Config.TextUI` unless you set `'custom'` and
provide one yourself here.
{% endhint %}
