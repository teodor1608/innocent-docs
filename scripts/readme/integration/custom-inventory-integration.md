---
description: >-
  This is a guide designed to lead you through the successful inventory
  integration process of the script. Please follow the steps and do not deviate
  from the instructions.
---

# Custom Inventory Integration

## Custom

Don't run ox\_inventory or qb-inventory? `inn-laundry` never calls an
inventory's own API directly — every item interaction only ever calls
eight functions on a server-side `Inventory` table, defined once per
inventory under `bridge/inventory/`.

{% hint style="info" %}
**qs-inventory users start here too, for now.** A dedicated bridge isn't
shipped yet — it's planned for a future update. In the meantime,
qs-inventory was historically qb-core-compatible, so `bridge/inventory/qb.lua`
(see the reference implementation below) is a solid starting point.
{% endhint %}

{% hint style="info" %}
Most inventories expose their functionality via
`exports['your-inventory']:FunctionName(...)` rather than a global table —
both `ox_inventory` and `qb-inventory` work this way (see the reference
implementations below). Check your inventory's own docs for its exact
export names; the table below just tells you what each one needs to do.
{% endhint %}

{% stepper %}
{% step %}
### Open the custom inventory template

Open <kbd>inn-laundry/bridge/inventory/custom.lua</kbd>. It already
contains every function signature you need, each currently just raising an
error so you can't accidentally ship it unfinished.
{% endstep %}

{% step %}
### Implement each function

| Function | Returns | Purpose |
|---|---|---|
| `Inventory.GetItemCount(src, item)` | `number` | Total count of `item` the player holds, summed across all stacks. |
| `Inventory.AddItem(src, item, count, metadata)` | `boolean` success | `metadata` is an optional table, e.g. `{ worth = 500 }`. |
| `Inventory.RemoveItem(src, item, count, slot, metadata)` | `boolean` success | `slot`/`metadata` are optional hints for inventories that need them to target a specific stack. |
| `Inventory.GetMarkedBillsWorth(src)` | `number` | Sum of `Config.MarkedBills.metadataKey` across every stack of `Config.MarkedBills.item` the player holds. |
| `Inventory.RemoveMarkedBills(src, worth)` | `boolean` success | Removes marked bills stacks totalling **exactly** `worth`; must fail and remove nothing if the player doesn't hold enough. |
| `Inventory.GetItemMetadata(src, item)` | `table\|nil` metadata, `number\|nil` slot | First matching stack only — used where only one instance matters (e.g. a jerry can), not summed like marked bills. |
| `Inventory.SetItemMetadata(src, item, slot, metadata)` | `boolean` success | Overwrites the metadata of a single-count stack at `slot`. Not for merging into an existing stack of identical metadata. |
| `Inventory.CreateUsableItem(item, cb)` | — | Registers `cb(src, itemData, slot, metadata)` to run when a player uses `item`. |
{% endstep %}

{% step %}
### Point the config at your bridge

Open `config/config.lua` and set:

```lua
Config.Inventory = 'custom'
```
{% endstep %}
{% endstepper %}

***

## Marked bills

`Config.MarkedBills` (in `config/config.lua`) points at whatever "dirty
cash" item your server already uses:

```lua
Config.MarkedBills = {
    item = 'markedbills',
    metadataKey = 'worth', -- key read on item.info / slot.metadata for the dirty cash value
}
```

`inn-laundry` doesn't create this item for you — most economy/laundering
setups already have one. Your bridge's `GetMarkedBillsWorth` and
`RemoveMarkedBills` are what actually read/consume it, so they need to
understand however your inventory stores a numeric value per stack (a
metadata table, an `info` table, or similar).

***

## Reference implementation

Every function above is implemented for qb-inventory in
`bridge/inventory/qb.lua`. The marked bills functions are the most
involved part — they sum a metadata field across every matching stack,
then remove just enough stacks to cover the requested amount:

```lua
local function getMarkedBillSlots(Player)
    local slots = {}

    for _, item in pairs(Player.PlayerData.items) do
        if item and item.name == Config.MarkedBills.item then
            slots[#slots + 1] = item
        end
    end

    return slots
end

function Inventory.GetMarkedBillsWorth(src)
    local Player = QBCore.Functions.GetPlayer(src)
    if not Player then return 0 end

    local key = Config.MarkedBills.metadataKey
    local total = 0

    for _, item in ipairs(getMarkedBillSlots(Player)) do
        local worth = item.info and item.info[key]
        if worth then total = total + worth end
    end

    return total
end

function Inventory.RemoveMarkedBills(src, worth)
    local Player = QBCore.Functions.GetPlayer(src)
    if not Player then return false end

    local key = Config.MarkedBills.metadataKey
    local remaining = worth
    local consumed = {}

    for _, item in ipairs(getMarkedBillSlots(Player)) do
        if remaining <= 0 then break end

        local slotWorth = item.info and item.info[key] or 0
        if slotWorth > 0 then
            consumed[#consumed + 1] = item
            remaining = remaining - slotWorth
        end
    end

    if remaining > 0 then
        return false -- player doesn't hold enough marked bills to cover this amount
    end

    for _, item in ipairs(consumed) do
        exports['qb-inventory']:RemoveItem(src, Config.MarkedBills.item, item.amount or 1, item.slot)
    end

    return true
end
```

{% hint style="warning" %}
`RemoveMarkedBills` must be all-or-nothing. If the player doesn't hold
enough to cover `worth`, return `false` and remove **nothing** — don't
partially consume stacks and then fail, or the player loses bills without
getting paid for them.
{% endhint %}

qb-inventory's own item metadata is immutable per-instance, so
`SetItemMetadata` there is a remove-then-re-add at the same slot with the
new metadata table — the same pattern qb-fuel itself uses to persist a
jerry can's fuel level:

```lua
function Inventory.SetItemMetadata(src, item, slot, metadata)
    if not exports['qb-inventory']:RemoveItem(src, item, 1, slot) then return false end

    local success = exports['qb-inventory']:AddItem(src, item, 1, slot, metadata)
    return success and true or false
end
```

Adapt that same remove-then-re-add pattern if your inventory also treats
metadata as immutable; if it exposes a direct metadata-update call instead,
use that.
