# Map integration changes

Source snapshot: 2026-11-02. Map package: apple_maps_kombi 0.1.2.

- The published cluster-render 0.2.0 and waze-on-cluster 0.6.0 executables were **not modified**. Their source is pinned to idrive-apps commit `bdd8d16ca075c2095b384f0c8f301219749992a5`.
- Disabled secondary configuration overrides by clearing `local_conf` in both service configurations.
- Limited cluster-render to `targets = map` and `scene_ids_allowed = 0x010004f0`.
- Set capture `freeze_ms = 0` so a stationary route repeating the same picture is not hidden merely because its pixels repeat. Connection and idle timeouts remain. This does not prove a repeated picture is current.
- Added the 379 PC-side installer, compatibility checks, configuration-only update path, PC backup/restore and file verification. The firmware patch offsets and before/after bytes are included; the proprietary firmware is not.
- Added a local systemd/socket control helper, the app's status/on/off client and the map-view UI function. This is integration with the upstream engine, not an independently written engine.

Exact configuration diffs are in `integration/changes/` inside the source archive. The map-specific installer and control files are included in full. Shared transport/backup functions are extracted unchanged; unrelated app installation/export functions and the GUI progress screen are omitted. The console entry point and no-UI progress adapter were added for this source release.

The original upstream licenses and attribution must remain with redistributions. Follow the upstream build documentation and the included BUILD-INSTALL.md. This release is not a blanket claim of GPL compliance for unrelated product components.
