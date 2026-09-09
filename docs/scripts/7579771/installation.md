---
description: >-
  This is a guide designed to lead you through the successful installation
  process of the script. Please follow the steps and do not deviate from the
  instructions.
---

# Installation

​[https://scripts.innocent-rp.store/packages/7579771](https://scripts.innocent-rp.store/packages/7579771)​

## Download Script

To download the assets of the script which you've purchased from INNOCENT RP you must follow the steps below. Asset delivery is handled by Tabex and Cfx.re in the Cfx.re Portal aka Keymaster.

{% stepper %}
{% step %}
### Go to <kbd>Cfx.re Portal</kbd> and navigate to the <kbd>Granted Assets Page</kbd>

<a href="https://portal.cfx.re/assets/granted-assets?search=inn-laundry" class="button primary" data-icon="arrow-up-right-from-square">Go to Cfx.re Portal</a>
{% endstep %}

{% step %}
### Sign in if prompted

Sign in with your Cfx.re account

{% hint style="warning" %}
Sign in using the same Cfx.re account you used when purchasing the asset.
{% endhint %}

If you can't find the asset please make sure you used the correct Cfx.re account before making any support requests.
{% endstep %}

{% step %}
### Download the script

Locate and download

* <kbd><mark style="color:$primary;">inn-laundry<mark style="color:$primary;"></kbd>
{% endstep %}
{% endstepper %}

***

## Download Dependencies

{% tabs %}
{% tab title="Required" %}
{% hint style="warning" %}
It is essential that you install all of the dependencies below, in addition to the framework/inventory you already run.
{% endhint %}

{% embed url="https://github.com/overextended/oxmysql" %}
MySQL access
{% endembed %}

{% embed url="https://github.com/overextended/ox_lib" %}
Callbacks, zones, points, notifications
{% endembed %}

{% embed url="https://github.com/DemiAutomatic/object_gizmo" %}
In-world 3D gizmo used to precisely place a washing machine
{% endembed %}

{% embed url="https://bzzz.tebex.io/package/7390529" %}
bzzz\_money — washing machine + supplier NPC bag props and animations. Install and update it separately, and don't rename anything inside it.
{% endembed %}
{% endtab %}

{% tab title="Optional" %}
{% hint style="info" %}
The dependencies below are not required for the core features of the script to function and are interchangeable for other assets of your liking.
{% endhint %}

**Target** (`ox_target` or `qb-target`) — recommended, but every interaction (machines, the supplier NPC, the generator, shell entry/exit) can fall back to a simple `[E]`-prompt instead via each feature's `useTarget` config toggle, so a target resource isn't strictly required.

**Dispatch** (`ps-dispatch` or `cd_dispatch`) — if neither is installed, police raid alerts fall back to this script's own built-in notification to online police, so no third-party dispatch resource is required either.

**qb-interiors** — only needed if you want the default shell interior option (`shell_warehouse1`, the free shell from [k4mb1's shell pack](https://k4mb1maps.com/product/5015840)) to resolve in-game. Skip this if you clear `Config.Shells.options` or only add your own shell pack entries.
{% endtab %}
{% endtabs %}

***

{% hint style="danger" %}
It is highly recommended that you shutdown your FiveM server before you proceed with any of the steps below.
{% endhint %}

***

## Assets Upload

{% stepper %}
{% step %}
### Upload the script

Drag the <kbd>inn-laundry</kbd> folder into your server's <kbd>resources</kbd> directory.
{% endstep %}

{% step %}
### Upload bzzz\_money

Drag the <kbd>bzzz\_money</kbd> folder into <kbd>resources</kbd> too, and follow its own `readme.txt` for setup.
{% endstep %}

{% step %}
### Copy the item icons

Copy the item icons included in <kbd>install/images/</kbd> into your inventory's image folder:

* ox\_inventory → `ox_inventory/web/images/`
* qb-inventory → `qb-inventory/html/images/`
* qs-inventory → `qs-inventory/html/images/`
* qs-inventory (Advanced) → `qs-inventory/web/dist/images/`
{% endstep %}
{% endstepper %}

***

## Starting Order

Dependencies must start **before** `inn-laundry` in `server.cfg`. Framework, inventory, and target can be whichever of the listed options you actually run:

```cfg
ensure oxmysql
ensure ox_lib
ensure qb-core        # or es_extended / qbx_core
ensure ox_inventory    # or qb-inventory
ensure ox_target       # or qb-target
ensure object_gizmo
ensure bzzz_money
ensure qb-interiors    # only if using the default shell option
ensure inn-laundry
```

***

## Items Setup

{% tabs %}
{% tab title="ox_inventory" %}
Add the contents of `install/items/ox_inventory.lua` to `ox_inventory/data/items.lua`.
{% endtab %}

{% tab title="qb-inventory" %}
Add the contents of `install/items/qb-inventory.lua` to `qb-core/shared/items.lua` (QBCore or Qbox running qb-inventory).
{% endtab %}

{% tab title="qs-inventory" %}
Add the contents of `install/items/qs-inventory.lua` wherever your qs-inventory version declares items.
{% endtab %}

{% tab title="Anything else" %}
Use one of the files above as a reference for the item names/weights and add them however your inventory expects.
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
If you rename an item in `config/items.lua`, rename it in your inventory's item list too, or the script will hand players an item your inventory doesn't recognize.
{% endhint %}

***

## Database Setup

`server/db.lua` runs `CREATE TABLE IF NOT EXISTS` for all five tables the first time the resource starts — you don't need to import anything by hand in the normal case.

{% hint style="info" %}
If your database user isn't allowed to run `CREATE TABLE` (some shared hosts restrict this), import `install/sql/inn-laundry.sql` once by hand instead, preferably using HeidiSQL or another manager compatible with MariaDB/MySQL — the resource will detect the tables already exist and skip creating them.

Avoid doing this through XAMPP or other non-optimized local server setups, as they're prone to causing connection errors.
{% endhint %}

***

## Extra

{% hint style="warning" %}
Starting the script or the server is not recommended before you have completed [integration](integration/ "mention").
{% endhint %}

{% hint style="info" %}
If any of the steps or instructions in this guide are incompatible with your infrastructure please request [support.md](../../innocent-rp/support.md "mention").
{% endhint %}
