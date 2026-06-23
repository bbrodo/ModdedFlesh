# Modded Flesh Changes

This project is a decompiled/rebuilt Godot 3.4.2 version of the game with local gameplay, bug-fix, and performance changes.

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

Main files:

- `characters/npcs/NPC.gd`
- `characters/npcs/HumanoidNPC.gd`
- `characters/npcs/soldier/Soldier.gd`
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

Main files:

- `characters/HealthManager.gd`
- `items/weapons/projectiles/Projectile.gd`

## TerraWorm Fixes

- Fixed TerraWorm kill zone lookup by using the explicit `Graphics/BoneAttachment tail_0/KillZone` node path.
- Fixed a crash when killing TerraWorm by making `set_path_to_death_path()` return the expected dictionary data.

Main file:

- `characters/npcs/terraworm/TerraWorm.gd`

## Validation Notes

Most script changes were checked by loading relevant scenes headlessly with Godot 3.4.2:

- `characters/player/Player.tscn`
- `characters/npcs/soldier/Soldier.tscn`
- `items/weapons/BullpupSmg.tscn`
- `items/weapons/MilitaryGrenadeLauncher.tscn`
- `items/weapons/projectiles/Grenade.tscn`
- `levels/TerraWormBossFight.tscn`

The Steam GDNative warning is currently expected in this workspace:

```text
Can't open dynamic library: res://addons/steam_api/steamsdk-godot.dll
```

This is separate from the gameplay/script fixes above.
