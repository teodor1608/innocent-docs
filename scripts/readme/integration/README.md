# Integration

This script is highly customizable and compatible with all major frameworks and some of the most popular scripts on the market.&#x20;

Don't use a mainstream framework or script? That's ok, custom integrations are supported too.&#x20;

{% hint style="info" %}
Every setting below defaults to `'auto'`, which detects what's running on
your server via `GetResourceState` — you usually don't need to touch any of
this at all. Set an explicit value only if you run something non-standard
or auto-detection picks the wrong one.
{% endhint %}

## Framework

Please proceed based on the framework you use.

{% tabs %}
{% tab title="QBCore" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Framework</kbd> to

```lua
-- Config.Framework = 'auto'
```

or&#x20;

```lua
Config.Framework = 'qb'
```
{% endtab %}

{% tab title="ESX" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Framework</kbd> to

```lua
-- Config.Framework = 'auto'
```

or&#x20;

```lua
Config.Framework = 'esx'
```
{% endtab %}

{% tab title="Qbox" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Framework</kbd> to

```lua
-- Config.Framework = 'auto'
```

or&#x20;

```lua
Config.Framework = 'qbox'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
For custom frameworks please see [custom-framework-integration.md](custom-framework-integration.md "mention")
{% endhint %}

***

## Target

Please proceed based on the target script you use.

{% tabs %}
{% tab title="qb-target" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Target</kbd> to

```lua
-- Config.Target = 'auto'
```

or&#x20;

```lua
Config.Target = 'qbtarget'
```
{% endtab %}

{% tab title="ox_target" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Target</kbd> to

```lua
-- Config.Target = 'auto'
```

or&#x20;

```lua
Config.Target = 'oxtarget'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Don't want a target dependency at all? Machines, the supplier NPC, the
generator, and shell entry/exit each have their own `useTarget` config
toggle — set them to `false` to use a simple `[E]`-prompt instead.
{% endhint %}

{% hint style="info" %}
For custom target scripts please see [custom-target-integration.md](custom-target-integration.md "mention")
{% endhint %}

***

## Inventory

Please proceed based on the inventory you use.

{% tabs %}
{% tab title="qb-inventory" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Inventory</kbd> to

```lua
-- Config.Inventory = 'auto'
```

or&#x20;

```lua
Config.Inventory = 'qb'
```
{% endtab %}

{% tab title="ox_inventory" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Inventory</kbd> to

```lua
-- Config.Inventory = 'auto'
```

or&#x20;

```lua
Config.Inventory = 'ox'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
For custom inventories please see [custom-inventory-integration.md](custom-inventory-integration.md "mention")
{% endhint %}

***

## Dispatch

Optional — if neither of these is installed, police raid alerts fall back
to this script's own built-in notification to online police, so you can
skip this section entirely.

{% tabs %}
{% tab title="ps-dispatch" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Dispatch</kbd> to

```lua
-- Config.Dispatch = 'auto'
```

or&#x20;

```lua
Config.Dispatch = 'psdispatch'
```

{% hint style="warning" %}
ps-dispatch dispatches by a `codeName` that must exist in **its own**
`config.lua`, not this script's. Add an entry there — see the comment at
the top of `bridge/dispatch/psdispatch.lua`.
{% endhint %}
{% endtab %}

{% tab title="cd_dispatch" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Dispatch</kbd> to

```lua
-- Config.Dispatch = 'auto'
```

or&#x20;

```lua
Config.Dispatch = 'cddispatch'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
For custom dispatch scripts please see [custom-dispatch-integration.md](custom-dispatch-integration.md "mention")
{% endhint %}

***

## Notify

Independent of `Config.Framework` — a QBCore server can still prefer
ox\_lib-styled toasts (or vice versa), so pick whichever matches your
server's look.

{% tabs %}
{% tab title="ox_lib" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Notify</kbd> to

```lua
Config.Notify = 'ox'
```
{% endtab %}

{% tab title="qb-core" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Notify</kbd> to

```lua
Config.Notify = 'qb'
```
{% endtab %}

{% tab title="ESX" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.Notify</kbd> to

```lua
Config.Notify = 'esx'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Unlike the other bridges, `'auto'` falls back to `'ox'` here rather than
`'custom'` if nothing else is detected — ox\_lib is already a hard
dependency of this whole resource, so it's always a safe default.
{% endhint %}

{% hint style="info" %}
For custom notification scripts please see [custom-notify-integration.md](custom-notify-integration.md "mention")
{% endhint %}

***

## Text UI

The on-screen `[E] interact` prompt shown by the `[E]`-prompt fallbacks
mentioned above (see **Target**). Independent of `Config.Framework`, same
as **Notify**.

{% tabs %}
{% tab title="ox_lib" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.TextUI</kbd> to

```lua
Config.TextUI = 'ox'
```
{% endtab %}

{% tab title="qb-core" %}
Navigate to <kbd>inn-laundry/config/config.lua</kbd> and set <kbd>Config.TextUI</kbd> to

```lua
Config.TextUI = 'qb'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
No ESX option — ESX has no built-in on-screen prompt system of its own to
bridge to (unlike QBCore's `DrawText`). `'auto'` falls back to `'ox'` for
ESX and anything else undetected, the same as **Notify** above.
{% endhint %}

{% hint style="info" %}
For custom text UI scripts please see [custom-text-ui-integration.md](custom-text-ui-integration.md "mention")
{% endhint %}
