# Changelog

## v0.1.7 - 2026-06-28

### Added
- Added graphics menu checkboxes for the static environment culler and NPC culler.
- Added saved settings for enabling or disabling both cullers, enabled by default.
- Added runtime enable/disable support for the static environment culler and dynamic NPC render culler.

### Changed
- Set shadows to off by default for new settings files to improve performance.
- Restored culled geometry, NPC graphics, and shadow state when cullers are disabled from settings.

### Fixed
- Fixed sabretooth pickup not applying player stats to damage by updating inventory equipment when picking up weapons.
- Fixed NPC shotgun bursts sometimes missing completely at close range by adding one guaranteed center pellet to NPC shotgun spread.

## v0.1.6 - 2026-06-27

### Added
- Added pooling for blood hit effects, bullet hit effects, explosions, lightning effects, acid pools, and fire objects.
- Added shared pooling support for non-bullet projectile lifecycles, including blood projectiles and acid projectiles.
- Added grenade projectile and grenade explosion effect pooling with reset handling for timers, visibility, collision, sounds, and motion trails.

### Changed
- Routed biogun elemental effects, lightning hands, acid projectiles, grenade explosions, homing bullet elemental effects, TerraWorm acid, and missile bug explosions through pooled effect paths where applicable.
- Reduced eating-related frame spikes by avoiding repeated eat-state work when the player is already at full health or has no digestion.
- Made stopping eating idempotent so it no longer replays hold animations or logs every frame when already stopped.

### Fixed
- Fixed eating animation restarts while the eat input is held.
- Fixed late eating animation bite callbacks emitting healing after eating has stopped or health is already full.
- Fixed pooled projectile reset behavior so reused projectiles restore collision, visibility, timers, and state correctly.

## v0.1.4 - 2026-06-24

### Added
- Added persistent file logging for exported builds so tester crashes can be investigated after the console closes.
- Added Godot engine output logging to `user://logs/godot.log`.
- Added support for waking nearby inactive NPCs through activator triggers when ally alerts are broadcast.
- Added emitter verbosity propagation from weapons to bullet emitters.
- Added blood decal throttling and chance controls to reduce decal spam and damage-time overhead.

### Changed
- Increased ally alert and player gunfire alert radii so nearby enemies join fights more reliably.
- Reduced soldier and NPC combat frame drops by throttling expensive combat sensing checks.
- Cached line-of-sight, aim-point LOS, obstacle, ally-in-front, projectile speed, and projectile gravity values instead of recalculating them every frame.
- Optimized predictive aiming history by using a running velocity sum instead of summing the whole history every frame.
- Optimized bullet emitter chains so common one-child emitters call their child directly instead of repeatedly using the generic `fire_children()` loop.
- Reduced grenade explosion frame spikes by lowering lingering fire counts and spreading explosion fire placement work across batches.

### Fixed
- Fixed ally alert propagation so nearby active NPCs are found by group lookup instead of relying only on stale physics-query caches.
- Fixed NPC ally alerts rebroadcasting recursively and causing group alert frame spikes.
- Fixed NPC vision LOS checks to start from eye height and ignore the NPC's own body and hitboxes.
- Fixed Pollop, Wizard, and general NPC detection range issues caused by bad LOS checks.
- Fixed grenade launcher ballistic aiming to use horizontal X/Z distance instead of full 3D distance.
- Fixed grenade launcher vertical aiming sign so enemies aim upward at targets above them and downward at targets below them.
- Fixed grenade explosion paths re-running from repeated `body_entered` signals.

## v0.1 - 2026-06-23

### Added
- Added the public Modded Flesh patch package README for users applying an xdelta patch with WroughtFleshPatcher.
- Added dialogue path fallback support for both `res://dialog/...` and legacy `res://old_dialog/...` conversation files.
- Added missing-dialog diagnostics for exported builds.
- Added an export verification script for checking required dialogue JSON files.
- Added a no-op Steam singleton stub so achievement calls remain safe without loading the Steam GDNative addon.
- Added `log_organ_decay` so organ decay logs are off by default.
- Added real bullet pooling for normal bullet projectiles.

### Changed
- Updated distribution wording to clarify that the repository contains only the patch and manifest, not the full game.
- Disabled expensive per-shot bullet spawn logging during normal gameplay.
- Disabled expensive per-shot weapon fire logging unless the weapon is explicitly verbose.
- Removed the native Steam autoload and Windows Steam DLL export entries from the patch build.
- Disabled Steam achievements and leaderboards for the patch build while keeping local achievement fallback storage.

### Fixed
- Fixed first-person arm and body visibility when switching between first-person and third-person views.
- Fixed saved/new-game third-person arm initialization by refreshing third-person arm IK after entering third person.
- Fixed no-weapon third-person arm IK and finger-gun hand pose behavior without permanently offsetting shoulders.
- Fixed held and eatable bodies disappearing in third person.
- Fixed character creation preview positioning on wide-screen resolutions.
- Fixed character preview placement by using the camera screen ray and locking the result to the ground plane.
- Fixed viewport resize handling for the character creation preview.
- Fixed pooled bullet reset behavior so raycast exceptions, lifespan timers, visibility, and physics state reset correctly.
- Fixed cached projectile collision-exclusion bodies so projectiles do not rebuild that list every movement tick.
- Fixed TerraWorm kill zone lookup by using the explicit `Graphics/BoneAttachment tail_0/KillZone` node path.
- Fixed a TerraWorm death crash by making `set_path_to_death_path()` return the expected dictionary data.
- Fixed exported builds freezing the player when dialogue failed to open by returning the player to normal state.
