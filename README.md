# Modded Flesh

This repository contains the public Modded Flesh patch package for Wrought Flesh. It is intended to contain only the xdelta patch file and patch manifest for users who already own the base game.

It does not include the full game, original game files, or a standalone playable build.

## Aim

This mod aims to fix bugs, crashes and performance issues introduced in the Purple Sands of Cumulun update and return the game to a playable state.

## Distribution

Install this patch with WroughtFleshPatcher:

https://github.com/bbrodo/WroughtFleshPatcher

WroughtFleshPatcher applies the xdelta patch to the user's existing installed copy of Wrought Flesh.

***
# Changes:

## Player Third-Person Arms

- Fixed first-person arm/body visibility when switching between first-person and third-person views.
- Added a deferred third-person arm IK refresh when entering third person so saved/new-game player initialization does not leave shoulders or arms in a bad pose.
- Restored the no-weapon third-person arm IK/finger-gun style hand pose path without letting it permanently offset shoulders.
- Fixed held/eatable bodies disappearing in third person by keeping carried body visibility separate from first-person arm hiding.

Main files:

- `characters/player/Player.gd`
- `characters/player/WeaponManager.gd`

## Character Creation Preview Positioning
- Fixed character preview positioning on wide-screen resolutions so the in-world character no longer moves behind the right-side creation menu.
- Updated the character placement logic to use the camera’s screen ray and lock the result to the ground plane, keeping the preview character grounded while maintaining a safe left-side screen position.
- Added viewport resize handling so the character preview updates correctly when the window size or resolution changes.

Main files:

- `ui/DurjaMove.gd`

## NPC Combat Performance

- Reduced soldier/NPC frame drops during combat by throttling expensive combat sensing checks.
- Cached line-of-sight, aim-point LOS, obstacle, ally-in-front, projectile speed, and projectile gravity values instead of recalculating all of them every frame.
- Optimized predictive aiming history by using a running velocity sum instead of summing the whole history every frame.
- Optimized bullet emitter chains so common one-child emitters call their child directly instead of repeatedly using the generic `fire_children()` loop.
- Added real bullet pooling for normal bullet projectiles instead of instancing and freeing every bullet.
- Fixed pooled bullet reset behavior so raycast exceptions, lifespan timers, visibility, and physics state reset correctly.
- Reduced blob/NPC alert frame spikes by clearing stale ally lists, preventing repeated same-target ally broadcasts, and spreading ally alert propagation across frames.
- Fixed ally alert propagation so nearby active NPCs are found by group lookup instead of relying only on stale physics-query caches.
- Added support for waking nearby inactive NPCs through their activator triggers when an ally alert is broadcast.
- Prevented ally alerts from rebroadcasting recursively, avoiding frame spikes when one NPC alerts a group.
- Increased ally alert and player gunfire alert radii so nearby enemies join ongoing fights more reliably.
- Fixed NPC vision LOS checks to start from eye height and ignore the NPC's own body/hitboxes, improving Pollop/Wizard and general NPC detection.
- Fixed NPC shotgun bursts sometimes missing completely at close range by adding one guaranteed center pellet to NPC shotgun spread while keeping the rest of the pellets randomized.
- Cached NPC group fallback results and short-lived alertable ally candidates so ally alert waves do less repeated full-group scanning.

Main files:

- `characters/npcs/NPC.gd`
- `characters/npcs/NPCActivatorTrigger.gd`
- `characters/npcs/HumanoidNPC.gd`
- `characters/npcs/blob/Blob.gd`
- `characters/npcs/nautilus/Nautilus.gd`
- `characters/npcs/soldier/Soldier.gd`
- `characters/VisionManager.gd`
- `singletons/dynamic_npc_render_culler.gd`
- `characters/player/Player.tscn`
- `characters/PredictiveAimLogic.gd`
- `items/weapons/bullet_emitters/BulletEmitter.gd`
- `items/weapons/bullet_emitters/BurstEmitter.gd`
- `items/weapons/bullet_emitters/SprayEmitter.gd`
- `items/weapons/DoubleBarrelShotgun.tscn`
- `items/weapons/PumpActionShotgun.tscn`
- `items/weapons/TacticalShotgun.tscn`
- `items/weapons/projectiles/Bullet.gd`
- `items/weapons/projectiles/Projectile.gd`
- `singletons/ObjectPoolManager.gd`

## Graphics And Culling Performance

- Added a static environment culler for medium and small world geometry to reduce draw calls while keeping large distant landmarks visible.
- Added a dynamic NPC render culler that hides distant non-combat NPC graphics and culls distant NPC shadows while keeping active, alerted, visible, or held NPCs rendered.
- Added graphics menu checkboxes for enabling/disabling the static environment culler and NPC culler.
- Set both culler settings to enabled by default for new settings files.
- Set shadows to off by default for new settings files because shadows have a large performance cost.
- Restored hidden geometry, NPC graphics, and shadow state when either culler is disabled.
- Added automatic chunked `MultiMesh` batching for repeated static environment meshes to reduce draw submissions in dense areas.
- Added a smaller-object culling tier for very small static geometry, separate from small and medium objects.
- Staggered static environment and NPC graphics restore work across frames to reduce culler-related visibility spikes.
- Prevented the static environment culler from indexing world items, weapons, and stale reparented geometry so picked-up weapons do not disappear based on their original world position.
- Cached the dynamic NPC render culler's NPC list so it no longer scans the full `npcs` group on every culler update.

Main files:

- `main_menu/Graphics.gd`
- `main_menu/SettingsMenu.tscn`
- `singletons/static_environment_culler.gd`
- `singletons/dynamic_npc_render_culler.gd`

## Weapon And Bullet Logging

- Disabled expensive per-shot bullet spawn logging during normal gameplay.
- Disabled expensive per-shot weapon fire logging unless the weapon is explicitly verbose.
- Added emitter verbosity propagation from weapons to bullet emitters.
- Added `log_organ_decay` to keep organ decay logs off by default.

Main files:

- `items/weapons/Weapon.gd`
- `items/weapons/bullet_emitters/BulletEmitter.gd`
- `items/weapons/bullet_emitters/ProjectileSpawner.gd`
- `characters/player/inventory/Inventory.gd`

## Grenade Launcher Aiming

- Fixed ballistic aiming range calculation to use horizontal X/Z distance instead of full 3D distance.
- Fixed vertical aiming sign so grenade enemies aim upward when the target is above them and downward when the target is below them.
- Applied the same fixes to the duplicate utility predictive aiming helper.

Main files:

- `characters/PredictiveAimLogic.gd`
- `utility/utility.gd`

## Health And Effects Performance

- Added blood decal throttling/chance controls to reduce decal spam and damage-time overhead.
- Cached filtered projectile collision-exclusion bodies so projectiles do not rebuild that list every movement tick.
- Added pooling support for shared non-bullet projectile lifecycles so blood and acid projectiles can be reused safely.
- Added pooling for blood hit effects, bullet hit effects, explosions, lightning effects, acid pools, and fire objects to reduce runtime instancing/freeing spikes.
- Added pooling for gibs, blood particle bursts, and line bullet tracer visuals to reduce death, hit, and firing-time instancing spikes.
- Added pooling for homing bullets, beetle bomber missile bugs, TerraWorm rock explosions, and flying rocks.
- Routed biogun elemental effects, lightning hands, acid projectiles, grenade explosions, homing bullet elemental effects, TerraWorm acid, and missile bug explosions through pooled effect paths where applicable.
- Added audio stream caching for hit effects and music so repeated sounds and tracks reuse loaded streams.
- Fixed pooled flying rocks not moving by restoring physics processing when reused.

Main files:

- `characters/HealthManager.gd`
- `effects/gibs/GibSpawner.gd`
- `effects/gibs/Gib.gd`
- `effects/BloodParticlesBurst.gd`
- `items/weapons/projectiles/Projectile.gd`
- `items/weapons/projectiles/BloodProjectile.gd`
- `items/weapons/projectiles/Acid.gd`
- `items/weapons/effects/Explosion.gd`
- `items/weapons/effects/Lightning.gd`
- `items/weapons/effects/HitEffect.gd`
- `items/weapons/effects/LineBullet.gd`
- `items/weapons/bullet_emitters/HitScanBulletEmitter.gd`
- `effects/acid_pool/AcidPool.gd`
- `items/weapons/bullet_emitters/LightningEmitter.gd`
- `characters/npcs/terraworm/LargeAcidSpawner.gd`
- `characters/npcs/terraworm/FlyingRockSpawner.gd`
- `characters/npcs/beetle_bomber/MissileBug.gd`
- `characters/npcs/beetle_bomber/BeetleBomber.gd`
- `effects/rock_explosion/RockExplosion.gd`
- `effects/rock_explosion/FlyingRock.gd`
- `items/weapons/bullet_emitters/HomingBulletEmitter.gd`
- `items/weapons/projectiles/HomingBullet.gd`
- `singletons/MusicManager.gd`
- `singletons/ObjectPoolManager.gd`

## Runtime Resource Caching

- Cached world item scenes for organs and dropped items so item drops no longer load their scenes at spawn time.
- Cached inventory, equipped weapon, and hotkey icon textures to reduce UI/equip-time resource loading.
- Cached colonist and tourist cosmetic textures/accessory scenes so those NPCs do less runtime loading when spawned.
- Cached NPC `PackedScene` resources in NPC spawners so horde, fog, and triggered NPC spawns no longer call `load(...).instance()` during the spawn path.

Main files:

- `items/ItemDB.gd`
- `characters/player/inventory/GroundContainer.gd`
- `characters/player/inventory/ItemUI.gd`
- `characters/player/EquippedWeaponsInfo.gd`
- `characters/player/inventory/Hotkeys.gd`
- `characters/npcs/colonist/Colonist.gd`
- `characters/npcs/colonist/tourist/Tourist.gd`
- `utility/NPCSpawner.gd`
- `singletons/ObjectPoolManager.gd`

## Grenade Explosion Performance

- Reduced grenade explosion frame spikes by lowering the number of lingering fire areas spawned per explosion.
- Spread explosion fire placement work across small batches instead of raycasting and spawning every fire in one frame.
- Added pooling for fire objects so repeated grenade explosions reuse existing fire nodes instead of constantly instancing and freeing them.
- Added a grenade impact guard so repeated `body_entered` signals cannot re-run the explosion path.
- Added pooling for grenade projectiles and grenade explosion effects, including reset handling for timers, visibility, collision, sounds, and motion trails.

Main files:

- `items/weapons/projectiles/Grenade.gd`
- `items/weapons/effects/Explosion.gd`
- `effects/fire/Fire.gd`
- `effects/fire/Fire.tscn`
- `singletons/ObjectPoolManager.gd`

## Eating Performance

- Reduced frame spikes while holding or eating corpses by avoiding repeated expensive eat-state work when the player is already at full health or has no digestion.
- Prevented the eating animation from being restarted every frame while the eat input is held.
- Guarded bite callbacks so late animation events do not emit healing signals after eating has stopped or health is already full.
- Made stopping eating idempotent so it no longer replays hold animations or logs every frame when already stopped.
- Removed eating debug logs so corpse pickup/eating no longer spams the console or log file.

Main file:

- `characters/player/EatManager.gd`

## TerraWorm Fixes

- Fixed TerraWorm kill zone lookup by using the explicit `Graphics/BoneAttachment tail_0/KillZone` node path.
- Fixed a crash when killing TerraWorm by making `set_path_to_death_path()` return the expected dictionary data.
- Pooled TerraWorm rock explosion and flying rock effects to reduce boss-effect instancing spikes.
- Gated TerraWorm missing-point debug output behind `debug_view`.

Main files:

- `characters/npcs/terraworm/TerraWorm.gd`
- `characters/npcs/terraworm/FlyingRockSpawner.gd`
- `effects/rock_explosion/RockExplosion.gd`
- `effects/rock_explosion/FlyingRock.gd`

## Dialogue Export Fixes

- Fixed exported builds freezing the player when dialogue failed to open by returning the player to normal state if a conversation cannot be loaded.
- Added dialogue path fallback support for both `res://dialog/...` and legacy `res://old_dialog/...` conversation files.
- Added missing-dialog diagnostics so exported builds report whether the expected dialogue directories/files are present in the packaged `.pck`.
- Added an export verification script for checking that required dialogue JSON files were packed.
- Fixed a Godot 3 type inference parse issue in dialogue condition checks.
- Export presets should include dialogue JSON files through Godot's non-resource include filter.

Main files:

- `characters/player/Player.gd`
- `characters/player/dialog_manager/DialogManager.gd`
- `tools/verify_export_dialogs.ps1`

## Player Weapon Fixes

- Fixed sabretooth not applying player stats to damage on pickup by updating inventory equipment when picking a weapon up

Main files:

- `characters/player/Player.gd`

## Steam DLL Removal

- Removed the native Steam autoload from this patch build so exported builds do not require Steamworks DLL files.
- Added a no-op Steam singleton stub so achievement calls remain safe without loading the Steam GDNative addon.
- Removed Windows Steam DLL export entries from the Steam GDNative library config.
- Steam achievements and leaderboards are disabled in this patch build; local achievement storage remains available through the existing fallback path.

Main files:

- `project.godot`
- `singletons/SteamStub.gd`
- `addons/steam_api/steam_api.gdnlib`

## Debug Logging

- Added persistent file logging for exported builds so tester crashes can be investigated after the console closes.
- Game logger output is written to `user://logs/latest.log`, with the previous run archived as `previous-YYYYMMDD-HHMMSS.log`.
- Godot engine output is also written to `user://logs/godot.log`.
- On Windows this uses the shared Wrought Flesh user directory, usually `AppData/Roaming/WroughtFlesh/logs`.
- Removed or debug-gated stray NPC, boss, laser pointer robot, and eating debug output that could spam logs during normal gameplay.

Main files:

- `singletons/log_config.gd`
- `project.godot`
- `characters/player/EatManager.gd`
- `characters/npcs/tiger_boss/laser_pointer/LaserPointerRobot.gd`
- `characters/npcs/terraworm/TerraWorm.gd`
