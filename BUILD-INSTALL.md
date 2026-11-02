# Build and installation

## Native code

The complete upstream source snapshot is in `upstream/`; its README, per-component READMEs, Makefiles, Dockerfile, ABI interfaces and license files are preserved. Native binaries were not edited by 379.

On a Linux build host with make, C++, Node.js, Python 3 and Docker:

```
make -C upstream/native/cluster-render test test-x86 target
make -C upstream/native/waze-on-cluster test test-x86 target
```

See upstream/native/cluster-render/README.md and upstream/native/waze-on-cluster/README.md for exact prerequisites. Optional firmware-dependent tests require the owner's own firmware and are not included in this archive. This source release was not independently rebuilt with Docker; the distributed native files were instead compared byte-for-byte with the pinned upstream release artifacts.

## Reproduce the 379 map payload

With Python 3.10 or later, from the extracted archive root:

```
python integration/package_kombi_maps.py
```

This downloads the two pinned public upstream release archives and verifies their sizes and SHA-256 checksums. It applies the configuration changes and writes integration/kyles-mgu-flasher/payload/apps/apple_maps_kombi@0.1.2.mgu and its catalog. The exact upstream source tarball is supplied locally and is also embedded in the rebuilt map payload. Container timestamps can change the outer archive hash; individual source and executable checksums identify the contents.

To use locally rebuilt native executables, replace the two files in the staging inputs of package_kombi_maps.py, retain the corresponding source, and regenerate the file hashes and payload catalog. The installer validates the payload catalog and ELF dependencies; no owner's update-server signing key is needed for this standalone SSH installation route. Do not label a modified executable as the original upstream release.

## Map-only installation / removal

This source snapshot is for an already authorized root-SSH connection to a compatible ID7 x64 MGU with CarPlay 22.28.3 matching the SHA-256 and patch anchors in integration/feature.json. ID8 or other firmware is not established by this release. SSH enrollment and proprietary firmware are not included.

From integration/kyles-mgu-flasher, run the map-only console using your own host, port and existing key path:

```
python maps_cli.py check --host <HU-IP> --port <SSH-PORT> --key <PRIVATE-KEY-PATH>
python maps_cli.py install --host <HU-IP> --port <SSH-PORT> --key <PRIVATE-KEY-PATH>
python maps_cli.py remove --host <HU-IP> --port <SSH-PORT> --key <PRIVATE-KEY-PATH>
```

Verify the SSH host fingerprint before accepting it. Install backs up the affected files to the computer, validates original firmware and stages checksummed changes. Keep that backup for removal. It never writes backup archives onto the head unit. Restart the head unit manually after successful installation or removal. For a configuration-only update, restart the map capture service as the installer indicates. An interrupted write requires inspection and restoration from the PC backup before retrying.

## 379 control integration

integration/control/map-control.js is the actual map helper. The service/socket units specify its installation at /var/opt/379-support/map-control.js and /etc/systemd/system/379-control.{service,socket}. A root administrator can install these files with mode 0644, then run `systemctl daemon-reload` and `systemctl enable --now 379-control.socket`. The socket grants access only to the tm_oap_app group. Do not expose it over TCP.

carplay-switch.js is the app-side client. carplay-view.js contains the actual map view function; the host supplies the canvas drawing primitives, colors, text helpers, focus-region callback and refresh callback described in that file. The client uses newline-delimited JSON over /run/379-control/control.sock: `{"cmd":"status"}` or `{"cmd":"set","enabled":true}`. The helper checks firmware and installed unit hashes before controlling the two fixed map services.

The source files and installation route are available without the private 379 update-server credentials. No private credentials, user records or car backups are supplied or required to read or rebuild the published source.
