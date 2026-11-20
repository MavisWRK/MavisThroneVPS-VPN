# Brazilian Portuguese release verification

The `pt_BR` Qt catalog covers all 4,631 current translatable messages. The catalog was expanded from the initial 24 reviewed messages with machine-assisted drafts and a terminology pass. Translation coverage is complete, but a native-speaker review of the running application is still required before a public release.

## Automated checks

From the repository root, run:

```powershell
python -I script/check_translations.py --lupdate <path-to-lupdate.exe>
cmake --build <build-directory> --config Release --target Throne
```

The translation check fails if `pt_BR` has missing, stale, or unfinished messages. It also checks Qt placeholders, rich-text tags, menu accelerators, file filters, URL schemes, and PEM markers. The build must generate `pt_BR.qm` with 4,631 finished translations.

## Windows application review

1. Select **Português (Brasil)** in **Basic Settings > Language**, restart the app, and confirm that the main window and settings remain in Portuguese.
2. Review the main menu, profile editor, routing settings, TUN settings, connection states, and error dialogs. Check for clipped labels and wording that changes the meaning of a setting.
3. In the **Selected apps only** mode, verify the file picker, missing executable warning, empty list, and reconnect notice in Portuguese.
4. Trigger singular and plural messages, especially connection counts, imported profiles, scan results, and warnings.
5. Check that protocol names, command names, `throne://` links, file filters, and configuration examples remain usable.

The automated build verifies that translations compile and are embedded. It does not verify visual layout or the meaning of every message. VPN routing and public IP checks still require the separate Windows and external endpoint validation described in [selected-apps-vpn.md](selected-apps-vpn.md).
