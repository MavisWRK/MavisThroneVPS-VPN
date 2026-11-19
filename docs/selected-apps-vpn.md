# Selected apps only (Windows MVP)

1. Import or create a WireGuard profile using Throne's existing profile editor. Keep credentials in the application's local data only.
2. Create a local structured routing profile. Open its **Apps** tab, enable **Selected apps only**, and add each `.exe` with **Add .exe**. Check the executables that should use the VPN. An unchecked entry stays saved but uses the direct connection.
3. Select that routing profile and the WireGuard profile, enable **TUN mode**, then connect. Save and reconnect after changing the list. The routing editor marks an active route as needing a restart.
4. If an entry says **missing or moved**, select its row and use **Change path...**, or remove it and add the executable again. Paths are matched exactly; choosing an installed executable is safer than typing a process name. Browser selection applies to the whole executable, including all its tabs and profiles.

In this mode, a TUN connection from a checked Windows process matches its full executable path and routes to the connected WireGuard outbound. The final outbound is direct, even with no checked executables. The profile's ordinary routing rules are retained but inactive while this mode is enabled. The mode requires a local structured routing profile, a WireGuard profile, TUN, and no exit wrapper or group proxy hops. A failed prerequisite is reported as a connection build error. Paths and enabled flags are stored per routing profile in the local database; an existing profile defaults to its original routing mode.

This is routing, not a kill switch. The expected behavior when TUN stops or the application disconnects is that its routes are removed and applications can use the ordinary system connection; this still needs a Windows traffic test. A selected application can also fall back to the direct connection after a tunnel failure. The MVP does not promise that DNS for a selected application uses WireGuard: Windows may resolve through its system resolver, and application DNS behavior varies. Do not use this mode when DNS isolation or fail-closed behavior is required.

## Validation on Windows 11

- With a test WireGuard endpoint, connect in TUN mode and inspect the generated route configuration: a `process_path` rule for checked executables goes to `proxy`, and `final` is `direct`. Verify an empty or fully unchecked list has no `process_path` VPN rule.
- Close and reopen the app; confirm the list and checks persist. Remove an entry and confirm it stays removed. Switch between two routing profiles and confirm their lists remain separate.
- Run two different executables simultaneously, one checked and one unchecked. Confirm TCP and UDP routing in connection logs or at controlled servers. Uncheck, save, reconnect, and repeat.
- A local WSL WireGuard endpoint can validate a handshake and controlled TCP/UDP paths if an operational distribution exists. It shares the host's public internet exit, so it cannot prove a public IP change.
- With an external endpoint, compare the public IP using two browsers with different executable paths. Also test a Discord call and a game session. Disconnect and simulate a failed tunnel; observe whether the selected application's traffic falls back to direct.

No real WireGuard keys or configurations belong in this repository, test output, or shared logs.

## Validation status in this workspace

- Source review confirmed that Throne already emits `process_path` rules and routes the `proxy` tag to the connected profile. The selected mode generates a TUN-scoped path rule and a `direct` final route.
- `wsl --list --verbose` found `CFH-Ubuntu18`; `wsl -d CFH-Ubuntu18 -- uname -s` returned `Linux`, confirming an operational distribution.
- This Windows environment currently has no accessible CMake, Qt, Ninja, or C++ compiler; the WSL distribution also has no accessible CMake, Qt, or C++ compiler. Compilation, app reopen/persistence checks, two-process TCP/UDP checks, tunnel failure behavior, and Discord/game sessions have not been executed here. Use the Windows 11 steps above after installing the project's build prerequisites and providing a test endpoint. Public IP comparison requires an external endpoint.
- `git diff --check` passed. An in-memory SQLite check executed the extracted `route_profiles` create/insert SQL and read back the selected-app fields. A legacy-row check confirmed the new columns' default values.
