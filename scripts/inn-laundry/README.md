---
description: >-
  A complete money-laundering resource for FiveM - washing machines, marked
  bills, admin-managed laundry zones, and police raids.
---

# inn-laundry

Players buy laundering supplies from a roaming supplier NPC, place washing machines inside admin-defined laundry zones, and wash marked/dirty bills into clean cash over time. Abnormal electricity usage alerts police, who can raid a zone and confiscate equipment. Admins create and manage laundry zones entirely in-game, with an optional private "shell" interior per zone.

Every server-authoritative rule (wash progress, electricity draw, access checks, purchases) is computed and enforced server-side from timestamps and database state — nothing about a wash cycle's outcome is ever trusted from the client.

## Features

* **Washing machines** — server-spawned, OneSync-synced props. Load marked bills, run a timed wash cycle, collect clean cash. Upgradeable in-game (motor, fan, drum, filter — 3 tiers each) to draw less power, finish faster, hold more, or convert more efficiently.
* **Machine health & repair kits** — every completed wash cycle wears a machine down a little; a worn machine still runs but gets slower and less efficient, and stops accepting new cycles entirely below a minimum health. Repair kits restore health partially, so upkeep is recurring rather than a one-time fix.
* **Generators** — an optional, player-placed fixture per zone that cuts electricity draw while fueled, burning fuel faster the more machines it's carrying load for. Refueling drains an existing jerry can item from your fuel resource rather than adding a new item to buy.
* **Laundry zones** — created entirely in-game via a freecam/walk-to-corner drawing tool, no map editor needed. Public or job-locked access, with a tablet-based employee access list for job-locked zones.
* **Optional private "shell" interior** — a zone can have its own admin-placed private room (a spawned prop, not a shared game interior) with an entrance teleport, so multiple laundry businesses don't have to fight over the same handful of fixed map interiors.
* **Supplier NPC** — sells washing machines and washing liquid. Fully configurable: roaming or static, target-based or a simple `[E]` prompt, shared or infinite stock per item, or disabled entirely if you'd rather sell the items through your own shop system.
* **Electricity detection & police raids** — running machines draw power; unattended zones draw more, and a fueled generator draws less. The meter moves through three risk bands (safe, elevated, critical) rather than a single trip wire, and a triggered raid alerts police via your dispatch resource, who can then confiscate flagged machines.
* **NUI app** — five React views (machine, tablet, supplier shop, zone electricity HUD, admin zone creator) replacing what would otherwise be a dialog-heavy UX.
* **Full localization** — every player-facing string (including the NUI) is driven by locale files.
* **Discord webhooks** — optional, independently toggled per event (wash completed, confiscation, admin actions).
* **Update checker** — console warning on startup if a newer version is published.

## Compatibility

Frameworks, inventories, target, dispatch, notify, and text UI systems are all bridged and auto-detected at startup — see [integration](integration/ "mention") for what's supported out of the box and how to plug in something else.

***

Continue to [installation.md](installation.md "mention") to get the script running on your server.
