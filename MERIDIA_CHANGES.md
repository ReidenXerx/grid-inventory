# Grid Inventory: Meridia fork changes

Fork of Grid Inventory by skypia0147-dev (https://github.com/skypia0147-dev/grid-inventory),
GPL-3.0 with the Modding Exception, which stays in force for this fork (see LICENSE-GPL and
EXCEPTIONS.txt). Base: upstream `main` at c0d8718 (1.6.5 plus later fixes).

Changed 2026-11-24 by the Meridia collection team (DuduPhudu):

## Let ring mods handle rings

Grid Inventory wears at most two rings: with both of its ring cells full, a new ring replaces
the one drawn on the hand (game/DualRing.cpp). That cap also overrides dedicated ring mods
(MARA, S04 Armour + Mara, Jewelry Limiter), so their third and later rings were taken off.

This fork adds a switch that steps aside for those mods:

- `game/DualRing.h/.cpp`: new `DetectRingMod()` and `RingModMode()`. When ring-mod mode is on,
  `PrepareForEquip()` lends no slot bit and takes nothing off, and `Tick()` enforces nothing.
  The ring is then equipped by the caller's plain `EquipObject`, which is the same path the
  vanilla Favorites menu uses, and the ring mod decides how many fingers are worn.
- `main.cpp`: `DetectRingMod()` runs at kDataLoaded.
- Optional `Data/SKSE/Plugins/GridInventory_rings.ini`:

      iLetRingModsHandleRings = -1   ; auto (default): on when a ring mod is loaded
                              =  0   ; never (stock two-ring rule)
                              =  1   ; always

  Auto detection matches loaded SKSE plugins by their registered name (`SKSEPlugin_Version`:
  "MARA", "S04Armor"), not by file name, or a loaded `Jewelry limiter.esp`.

Nothing else is changed. The log line `[DUALRING] ring mod mode ON/off (reason)` in
GridInventory.log shows the decision at load.
