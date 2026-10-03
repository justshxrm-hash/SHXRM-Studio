# SHXRM Studio

A paid, self-hosted Windows toolkit for IRL streaming: a native desktop control room, mobile dashboard, OBS control, incoming guest cameras, feed sharing, automatic BRB recovery, and Twitch / Kick / YouTube multichat.

**Download the Windows app from this repository's [Releases](../../releases). A purchased activation key is required to use the toolkit.** GitHub's **Code → Download ZIP** contains documentation, not the Windows app. If Releases is empty, a Windows build has not been published yet.

The app's **License** screen opens the publisher's purchase checkout. Your key appears in the purchase receipt. OBS, streaming-platform accounts, your encoder, networking, and optional cloud hosting are set up and maintained by you. The toolkit does not include a hosted streaming server, shared chat registrations, or bonding hardware.

## Start here

1. Download and extract the Windows x64 release ZIP.
2. Run `INSTALL.bat` using the Windows account that will run OBS.
3. Open **SHXRM Studio**, enter your purchase key, and activate this PC.
4. Follow the [complete setup guide](docs/SETUP.md).
5. Register your own chat applications using [Multichat setup](docs/MULTICHAT.md).
6. Complete the [off-air acceptance test](docs/ACCEPTANCE_TEST.md) before your first broadcast.

## Included controls

| Workspace | Function |
| --- | --- |
| Live monitor | Camera preview, OBS program image, output start/stop, source mute and Fix |
| Ingests | Add/connect/disconnect/remove incoming cameras; primary and backup selection |
| Share my feed | Private read-only camera connections for friends' OBS |
| Connections | Toggle toolkit connections and infrastructure recovery |
| Settings | OBS launch path, scenes, bitrate thresholds, protection, destinations, owner login |
| Multichat | Your authorized Twitch, Kick, and YouTube chat in one view |
| Signal analysis | Receive bitrate, connection telemetry, and history |
| License | Activate, validate, or deactivate this streaming PC |

See [activation and computer transfer](docs/ACTIVATION.md), [feed sharing](docs/FEED_SHARING.md), [troubleshooting](docs/TROUBLESHOOTING.md), [privacy](docs/PRIVACY.md), and [third-party notices](docs/THIRD_PARTY.md).

Windows 10/11 x64, OBS with WebSocket enabled, and Microsoft Edge WebView2 Runtime are required. Windows 11 is recommended; maintain a supported Windows installation. Remote operation also requires an awake streaming PC and working network access. Phone/browser access to the same activated PC uses your Studio owner login and does not consume another PC activation.

SHXRM is proprietary software distributed as compiled releases. This repository holds setup documentation and release downloads. Third-party components retain their respective licenses. See [LICENSE.txt](LICENSE.txt).
