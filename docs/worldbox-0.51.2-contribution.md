# ModernBox 5.01: WorldBox 0.51.2 compatibility and research progression

Contribution by Manu (LN55Ultra), prepared with AI-assisted development. Based on the author's standard M5 branch, commit e43989e9133409f28d08fcdb8f7fef5f34d6be75. This does not port the M2 branch or the separate M5TrainsNStuff content.

Validated locally on Windows with WorldBox 0.51.2 (build 719) and NeoModLoader 1.2.0.1. Offered for maintainer review; not an official ModernBox release.

## Existing compatibility fixes included

- Correct the 0.51 weapon resource layout for 31 weapons and supply nine existing item icons at the paths used by the game.
- Remove spawn buttons for undefined actor IDs instead of throwing during startup. No missing unit definitions are invented.
- Avoid clearing another mod's result for standard building orders. Preserve Building Styles' legitimate prefix result.
- Make MIRV, MGL and drug switches apply to the real crafting fallback as well as preferred culture lists.
- Supply missing future bonfire, N2 projectile/explosion and ground-explosion sprites; protect the future bonfire fallback.
- Fit 67 large unit portraits and register unit/item names at the keys WorldBox actually requests.
- Throttle expensive statistics text/count refresh and avoid reflection allocations in the movement patch. Missing-asset diagnostics remain available as log messages rather than hundreds of console-opening errors.
- Link ModernBox's spawn powers to their units, as WorldBox does for its own powers, so hovering or clicking a spawn button no longer throws an ArgumentNullException. The existing "Spawn the unit: ..." text is reused, with an English fallback. (Added 2026-10-07, see "Follow-up: spawn report".)
- Show fixed in-game hint texts (vehicle outside a kingdom, pizza, events, resource import) without a translation lookup, so they no longer log "missing text" and open the game console. The displayed text is unchanged. (Added 2026-10-07.)
- Register the research window title for German and English, so it survives a language switch. (Added 2026-10-07.)

The author already merged the PlanetCount directory creation fixes and the macOS time-zone fallback. Those changes are retained, not submitted again or overwritten.

## Research and development

Nine saved, sequential technologies link Renaissance firearms, industrialization, motorization, aviation, nuclear technology and the future era to settlement age, population, armies, completed buildings, libraries and paid research. Research costs two surplus gold per credited year. At most five elapsed years are credited per update; loading cannot charge or award the same year twice. Cities retain completed knowledge and partial progress through a change of ruler/state without summing duplicate progress.

Every civilization species receives a complete build order using its own existing architecture. New species can register later. Original faction assignments are retained, with a fallback for species unknown to ModernBox. House, hall and windmill levels, wells, statues, libraries, barracks, docks, markets and earned bonfire upgrades remain reachable. Existing god era buttons are explicit player overrides; automatic research does not overwrite earned progress. The Automatic research button clears the override and enables all four eras. The original Modern toggle initializer mistakenly toggled Medieval a second time and never enabled Modern; this copy/paste error is fixed.

The real crafting, vehicle-production and nuclear decision paths consult research. MIRV/N2 use is also guarded at Actor.tryToAttack, so looted or inherited equipment cannot bypass the nuclear requirement after a kingdom fragments. Nuclear crafting, EliteBomber production and nuclear use require current population of at least 1,000 and at least 150 warriors after nuclear research; the explicit god override is separate. Equipment, factories, wall materials and supported economy integrations preserve construction/research funds when checking affordability; actual debits are not clipped. Territory, housing, equipment storage, support buildings, armies and vehicle fleets scale with the settlement. Stockpile delivery and hard resource caps now accommodate actual construction costs and city population; the default 999 cap could not pay the 1000-gold future bonfire. Increasing capacity grants no resources. Higher house stages retain the capacity of earlier stages. There is no claim of unlimited performance or compatibility with arbitrary other mods.

Optional integrations currently bind CoreBox, Building Styles, Civilization Odyssey, WallBox and the local Classical Economics helper when present. Absent optional types are not required dependencies. The private Classical Economics helper is not distributed here, and its extra reserve integration is unavailable in stock copies that do not expose it.

## Graphics and provenance

107 new or relocated PNGs are delivered at their actual game resource paths. Existing weapon art and icons remain the original ModernBox authors' work. The replacement future bonfire, N2 and ground explosion were created with ImageGen from original prompts and adapted to pixel sprites. Five additional previously empty registered effects now have 39 transparent 64x64 frames: TestyWesty (8), Kwell (8), Kwell2 (8), ExplodingPillarThingy (7) and MapFull (8). These are newly generated stand-ins, not recovered original artwork. The author can keep or replace them.

The five prototype effect IDs currently have no direct gameplay call sites in stock M5. Their registered resources are repaired and were tested through explicit in-game effect spawning.

The five new effect sheets were generated with Codex ImageGen on 2026-10-06, then cropped to their chosen cells, reduced to 64x64 and a 20-color palette with transparent edges. One clipped pillar source cell was excluded. Raw masters and prompts are retained in the contributor's project; only consumed sprites are part of the mod contribution. No game DLLs, other mods, user saves, credentials or private runtime logs are included. Existing authorship and rights are preserved; this contribution does not invent a repository-wide license.

## Research thresholds and save behavior

| Technology | Research points | Settlement age | Population | Warriors | Completed buildings |
|---|---:|---:|---:|---:|---:|
| Architecture | 20 | 10 | 30 | 0 | 8 |
| Metallurgy | 35 | 20 | 60 | 0 | 12 |
| Education | 50 | 35 | 100 | 0 | 18 |
| Renaissance / firearms | 80 | 60 | 200 | 35 | 25 |
| Industrialization | 130 | 100 | 400 | 70 | 35 |
| Motorization / tanks | 180 | 150 | 650 | 120 | 45 |
| Aviation | 200 | 190 | 900 | 180 | 55 |
| Nuclear technology | 400 | 280 | 1,500 | 300 | 70 |
| Future technology | 650 | 380 | 2,200 | 450 | 90 |

All requirements are cumulative. After Architecture, a library is required; military stages also need a barracks. The sponsor needs a hall and enough gold above the next construction reserve. Research is stored in kingdom and city custom data. A settlement's retained knowledge survives a new ruler or a successor state without adding duplicate progress.

Existing M5 saves that do not contain these new research fields begin the research path at its foundations. Existing buildings and ordinary equipment remain in the save; the new crafting, upgrading and nuclear-use rules apply. The labelled god-era controls provide an explicit override. Automatic research clears that override and enables all four eras. Separate weapon switches remain available.

Renaissance halls and temples acquire a storage function. Vanilla upgradeBuilding only changes the template, skipping the initial storage setup normally done by setBuilding. The contribution initializes missing resource/book containers for these ModernBox upgrades while retaining existing contents. This fixes the standalone countFood exception rather than swallowing it or reporting a false zero.

## Evidence and limits

- **Normal development:** Five species started with 40 inhabitants each, initial ores/food and a generated 512x512 continent. No later population, research-stage, calendar or treasury injection was used for this run. Motorization was researched in game year 170 at 776 inhabitants / 388 warriors, and aviation in year 265 at 946 / 531. The world reached about 4,300 units; a short x1 sample measured 58.8 FPS at about 4,119 units, at 1024x768. After a save/reload and 20 seconds of observation: 4,300 -> 4,296 units and 29 -> 29 cities, with zero simulation/load exceptions. CoreBox, Building Styles, Civilization Odyssey, Classical Economics and Abyssfall were absent; two unrelated precompiled helper mods and three BepInEx utilities remained present. This is not a universal performance or balance claim.
- **Full mod configuration:** A separate ten-minute x100 replay of an already grown test world completed with zero new simulation/load exceptions. In that particular war-fragmented world the small states remained at early research levels. It does not prove that every collection of political mods, map or war setting will naturally produce a large empire.
- **Prepared endgame:** Real stockpiles, buildings, population and armies were deliberately prepared; controlled years and an explicit test income exercised all nine paid research steps. This is labelled algorithm/integration evidence, not natural demographic growth. Actual producers yielded tanks, artillery, jets and future units at their required stages. Full house/hall chains, future bonfires and markets worked, and future buildings/research survived save/reload. The Odyssey nuclear path rejected the early case specifically because of ModernBox research, accepted the mature case and produced an actual observed atomic explosion.
- **Counterchecks:** The actual crafting selector blocked early firearms while leaving a wooden-weapon neighbor available. Removing only the research postfix exposed the weapon; restoring it blocked it again. A small state with inherited nuclear knowledge could not craft or use MIRV/N2 weapons; an ordinary attack remained allowed. Removing only the nuclear-use prefix enabled the unsafe attack, then restoration blocked it again. Only the probe's own actor/projectiles were removed. A separate local WallBox geometry test retained 1,888 boundary tiles; an isolated old-limit mutation truncated to 1,600, then restoration retained all 1,888. That WallBox patch is not included in this contribution.
- **Species and graphics:** 58 civilization species and 328 generated building sprite bindings were checked with the full local configuration. A newly registered species resolved its architecture and build path; a non-civilized neighbor was unchanged. DE/EN research windows were viewed in the game. Each of the five new VFX was viewed in four actual game frames over approximately 0.02-0.71 seconds; FMOD events resolved and speaker-loopback output was measured separately. No subjective listening claim is made.
- **Startup without optional integrations:** ModernBox was also launched as the only NML mod, without the test harness. Its research window and Automatic research button were operated in the real game. Optional integrations were absent without preventing initialization. BepInEx's three existing utilities remained installed.

Earlier unchanged portrait, localization, weapon-texture, crafting-toggle and statistics fixes retain their prior in-game evidence. Known loader reflection/fallback warnings are distinguished from runtime exceptions. Advanced-stage natural growth beyond aviation was not demonstrated in the normal-development run; those paths were tested in the prepared endgame cases. No game binaries, other mods or private saves are supplied.

## Follow-up: spawn report (2026-10-07)

A player in the ModernBox Discord reported that the game crashes when spawning people. We tried to reproduce this on WorldBox 0.51.2 (build 719) with NeoModLoader 1.2.0.1. Every spawn was done by clicking the real god-power buttons in the game, the way a player does it.

- **Stock ModernBox 5.01 (Workshop copy) as the only NML mod:** humans, elves, orcs and dwarves, both single clicks and Shift multi-spawn; 11 ModernBox units; vehicles inside and outside a kingdom. There was no crash and no freeze. The log showed 22 `ArgumentNullException`s from hovering or clicking ModernBox spawn buttons (`LocalizedTextManager.getText` <- `ActorAsset.getLocalizedDescription`, from the spawn tooltip and the toolbar text). It also showed the already known missing weapon textures and the startup `NullReferenceException` for `crusaderdreadnaught`, both fixed earlier in this PR.
- **Result:** the reported crash was **not reproduced**. Without the player's game version, mod list and `Player.log` its cause remains open. This follow-up only claims to fix what was measured.
- **Cause of the exceptions:** `ActorAsset.getDescriptionID()` asks the unit's god power (`power_id ?? base_asset_id ?? id`). WorldBox only links `power_id` while it links its asset libraries (`PowerLibrary`), but ModernBox creates its spawn powers later. The fix performs the same link for ModernBox's own powers, only for units that have no power at all (94 units). It then registers the existing button description under the key the game requests, with an English fallback.
- **Found while testing:** spawning a vehicle outside a kingdom logged `missing text: You must spawn this vehicle within a kingdom.`. Being the first error of the session, it opened the game console over the screen. The first pizza click did the same. All 23 fixed hint texts in the mod now use `WorldTip.showNow(text, false, ...)`, and the visible text is identical.
- **Same clicks with this branch:** 0 exceptions, 0 "missing text" for the spawn tooltips and the vehicle hint. The tooltip reads "Elite Soldier / Spawn the unit: spaceork".
- **Language switch (de -> en -> de in the running game, ModernBox alone):** the spawn tooltips (`spaceork`, `Railgun`) and the research window title stay readable in English. Pre-existing and unchanged: ModernBox's own tab titles (`ModernBoxUnitsFuckYou`, `ModernBoxErasFuckYou`, `Tuxxego_mod_creator`) are registered for the active language only. After a switch they show their raw keys until restart, as do several NeoModLoader window titles during the reload.
- **Limits:** the pizza hint was not seen again after the fix; it is the same one-line change as the vehicle hint. Event and resource-import hints were changed in the same way but not triggered in the game.

## Tested configurations

- **ModernBox alone:** ModernBox was the only NML mod, with the BepInEx 5.4.22 utilities MapSizes, SimpleMessages and SimpleSpells present. Stock 5.01 was used for the reproduction; this branch was used for the before/after checks and the language switch.
- **Full private collection (this branch):** 69 mods on NeoModLoader 1.2.0.1 and BepInEx 5.4.22. The checks covered the spawn tooltips, vehicle hints and language switch, with 0 exceptions and only the known loader warnings. Many of these mods run as locally patched copies for 0.51.2. Two are private helper mods, and one is a private port of an older mod. The list documents the test environment; it is not a compatibility promise for stock downloads. One private helper restores runtime texts after a language switch, which hides text loss, so the language checks above were repeated with ModernBox alone.
- **Mods in the full collection (name and version):** ModernBox 5.01, PowerBox 1.5.2, Abyssfall 1.1.0, Building styles culture traits, Civilization Odyssey 1.2.0, Isekai 0.0.3 (private port), Maravillas de Dominio 1.0.0, TheRaces 1.8, 西幻世界 0.8.6, Defence Kingdom 1.10, Dragon Empire 1.0.0, The Parasite Infestation (TPI) 0.0.3, WallBox 2.0.0, Avb's Expanded Armory 1.82, Avb's Ultra Trait Pack 3.01, ClanSuccession 1.21, 古典经济学 (Classical Economics) 2.2.0, Darkie Custom Traits 1.0.8, Fides Vera 0.1.26, Political World 1.11.0-RC1, Political World: Grand Strategy 0.9.1-dev1, Lineage - Full Family Tree 1.0.0, Avb's Aesthetic Adjustments 1.71, CoreBox 0.2.0, Custom Kingdom Colors 0.0.2, WorldBox Radio Player, Optime 0.5.0-beta, Sandbox 1.2.4, Smooth Borders 1.0.6, Speed Unlocked 3.0.1, VoiceBox 0.5.0, Worldbox Ambience 1.0, All Tomorrows Box, ArtifactBox 0.2.0, Avatar: The Last Boxbender 1.3.2, Biome Generation Control 2.0.0, Boat Fix 1.1.0, BridgeBuilders 0.1, Divine Bloodlines 0.51.2, FlagBox 0.1.0, Friendly Villagers 1.5.1, Genetics Plus 0.2.0, GOWB 3.3, GreatConstructor 1.0.8, Heavenly Traits: Resurrected 2.0, 圣骑传说 (Holy Knight Mythos) 1.55, Interspecies Romance 1.1, Kinship - No Incest 1.0.0, Lycanthropy 1.0.0, MoBox 0.3.0, Moddersheepbox - Custom Name Generator 1.0.3, Mountain Barrier 1.1.0, Newspaper 1.0.1, No more deaths outside the map 2.0.0.0, NuclearFallout 1.0.0, Old Age Fix 1.0.0, RAINCARNATION3 4.0.0, Render fix, SimpleMessages 0.7, SimpleSpells 0.2, TraitBrowserMod 1.1.0, TraitBox 0.1.4, Traits+ : Expanded Chaos 6.0, WorldLawsPlus 1.2, Xavii's Mounts & Taming Mod, Map Makers Mod 1.0.3, MapSizes 1.0, plus two private helper mods (a performance/stability guard and a tab-layout fix).
