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

Main files:

- `characters/npcs/NPC.gd`
- `characters/npcs/NPCActivatorTrigger.gd`
- `characters/npcs/HumanoidNPC.gd`
- `characters/npcs/blob/Blob.gd`
- `characters/npcs/nautilus/Nautilus.gd`
- `characters/npcs/soldier/Soldier.gd`
- `characters/VisionManager.gd`
- `characters/player/Player.tscn`
- `characters/PredictiveAimLogic.gd`
- `items/weapons/bullet_emitters/BulletEmitter.gd`
- `items/weapons/bullet_emitters/BurstEmitter.gd`
- `items/weapons/bullet_emitters/SprayEmitter.gd`
- `items/weapons/projectiles/Bullet.gd`
- `items/weapons/projectiles/Projectile.gd`
- `singletons/ObjectPoolManager.gd`

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
- Routed biogun elemental effects, lightning hands, acid projectiles, grenade explosions, homing bullet elemental effects, TerraWorm acid, and missile bug explosions through pooled effect paths where applicable.

Main files:

- `characters/HealthManager.gd`
- `items/weapons/projectiles/Projectile.gd`
- `items/weapons/projectiles/BloodProjectile.gd`
- `items/weapons/projectiles/Acid.gd`
- `items/weapons/effects/Explosion.gd`
- `items/weapons/effects/Lightning.gd`
- `effects/acid_pool/AcidPool.gd`
- `items/weapons/bullet_emitters/LightningEmitter.gd`
- `characters/npcs/terraworm/LargeAcidSpawner.gd`
- `characters/npcs/beetle_bomber/MissileBug.gd`
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

Main file:

- `characters/player/EatManager.gd`

## TerraWorm Fixes

- Fixed TerraWorm kill zone lookup by using the explicit `Graphics/BoneAttachment tail_0/KillZone` node path.
- Fixed a crash when killing TerraWorm by making `set_path_to_death_path()` return the expected dictionary data.

Main file:

- `characters/npcs/terraworm/TerraWorm.gd`

## Dialogue Export Fixes

- Fixed exported builds freezing the player when dialogue failed to open by returning the player to normal state if a conversation cannot be loaded.
- Added dialogue path fallback support for both `res://dialog/...` and legacy `res://old_dialog/...` conversation files.
- Added missing-dialog diagnostics so exported builds report whether the expected dialogue directories/files are present in the packaged `.pck`.
- Added an export verification script for checking that required dialogue JSON files were packed.
- Export presets should include dialogue JSON files through Godot's non-resource include filter.

Main files:

- `characters/player/Player.gd`
- `characters/player/dialog_manager/DialogManager.gd`
- `tools/verify_export_dialogs.ps1`

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

Main files:

- `singletons/log_config.gd`
- `project.godot`
