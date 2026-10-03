# Complete self-hosted setup

## 1. Prepare the streaming PC

Install [OBS Studio](https://obsproject.com/download) and [Microsoft Edge WebView2 Runtime](https://developer.microsoft.com/en-us/microsoft-edge/webview2/). Keep the PC connected to reliable power and internet; disable sleep while using it remotely. The Windows release includes the toolkit services and MediaMTX. Buyers do not need Python or developer build tools.

Extract the release ZIP to a temporary folder. Run **INSTALL.bat**, then open **SHXRM Studio** from the Desktop or Start menu. The default installation is `%LOCALAPPDATA%\SHXRM\Studio`. Keep the extracted download separate from this installation. Run the app as the same Windows user each time; activation storage belongs to that account.

Activate the purchase key from your receipt. The native desktop opens an authenticated local session. For mobile access, create an owner username/password in Studio Settings. This local login is separate from your purchase key and platform accounts. Never give a friend your owner login just to share a camera.

## 2. Connect OBS

In OBS, open **Tools → WebSocket Server Settings**, enable the server, use port **4455**, and enable authentication. Studio detects the current Windows account's OBS WebSocket configuration. For a customized configuration, use the toolkit's OBS settings and reconnect. OBS output status must show explicit active/inactive states before starting a broadcast.

In Studio Settings, set the path to `obs64.exe` if it is outside the usual OBS installation. **Open OBS** launches OBS on this streaming PC; it does not turn on a powered-off PC or log into Windows. Launch it once locally first. Remote launch uses the Windows session running SHXRM.

## 3. Set up private remote access

Install [Tailscale](https://tailscale.com/download) on the streaming PC and encoder phone. Sign in and verify both devices can communicate. In **Ingests → Ingest network**, save the streaming PC's Tailscale IPv4 address (`100.x.x.x`). Configure access policies so only the intended devices can reach the required ports.

| Port | Access | Purpose |
| --- | --- | --- |
| TCP 8788 | Authorized Tailscale devices | Owner mobile dashboard |
| UDP 8890 | Encoder / authorized receiving friends | SRT ingest and camera sharing |
| TCP 1935 | Authorized encoders, only if needed | Optional RTMP ingest |
| TCP/UDP 8189 | Owner viewing devices | WebRTC preview media |
| TCP 8787, 8790–8792, 9997–9998 | Loopback only | Internal services / telemetry / control |
| TCP 8554, 8888–8889 | Loopback only | OBS source and dashboard media proxy |
| TCP 8793 | Loopback / chosen public HTTPS proxy | Optional Kick event receiver |

If Windows Firewall blocks Tailscale traffic, create narrowly scoped inbound rules. For example, in an administrator PowerShell:

```powershell
New-NetFirewallRule -DisplayName 'SHXRM dashboard over Tailscale' -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8788 -RemoteAddress 100.64.0.0/10
New-NetFirewallRule -DisplayName 'SHXRM SRT over Tailscale' -Direction Inbound -Action Allow -Protocol UDP -LocalPort 8890 -RemoteAddress 100.64.0.0/10
New-NetFirewallRule -DisplayName 'SHXRM preview TCP over Tailscale' -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8189 -RemoteAddress 100.64.0.0/10
New-NetFirewallRule -DisplayName 'SHXRM preview UDP over Tailscale' -Direction Inbound -Action Allow -Protocol UDP -LocalPort 8189 -RemoteAddress 100.64.0.0/10
```

The installer detects Tailscale when already installed. For remote WebRTC, verify `MediaMTX\mediamtx.yml` contains the PC's Tailscale IP under `webrtcAdditionalHosts`; restart off air after changing it. HLS playback is available when WebRTC cannot connect. The supplied media authentication restricts the main camera's publishing/reading to loopback and Tailscale addresses. Guests use separate credentials. Configure Windows Firewall and Tailscale policies as well; IP restrictions alone do not identify the camera operator.

On your phone, open `http://YOUR_PC_TAILSCALE_IP:8788`, create/use the owner login, and test preview. Use private Tailscale routing for this HTTP address. Public internet access requires your own authenticated HTTPS reverse proxy and careful access policy; do not expose internal toolkit ports. Tailscale, Windows Firewall and router configuration are not changed automatically by the app.

## 4. Connect your camera encoder

Use an encoder that can publish SRT or RTMP. For the default main camera, configure SRT caller mode:

```text
Server: your streaming PC's Tailscale IP
Port: 8890
Stream ID: publish:shxrm-irl
Latency: start with 1000 ms
```

The corresponding URL is `srt://YOUR_PC_TAILSCALE_IP:8890?mode=caller&streamid=publish:shxrm-irl&latency=1000000`. For RTMP, use `rtmp://YOUR_PC_TAILSCALE_IP:1935/shxrm-irl`; enable only the needed firewall rule. Encoder field formats differ: use the generated connection details in **Ingests** and the encoder's manual. Start camera publishing before **Go live**.

SHXRM receives SRT, but this release does not install an SRTLA bonding receiver. SRTLA encoders need a separately configured compatible receiver that forwards ordinary SRT to MediaMTX. A license does not include bonding, mobile data, or guaranteed network capacity.

## 5. Create OBS scenes and stream destinations

In Ingests, create the primary camera's OBS scene or map it to an existing scene. A manual OBS Media Source uses `rtsp://127.0.0.1:8554/shxrm-irl`; disable **Local File**, use TCP RTSP, allow reconnect, and keep the source available while inactive. Managed guest scenes use their generated local reader credentials.

Create **BRB** in OBS with a static image, animation, or video. Optionally create a separate low-bitrate scene. Set Settings → Protection normal / low / offline scenes to exact existing names, enable protection, and save. Start with the supplied delay/threshold settings; tune them for your encoder bitrate. Recovery requires both a healthy feed and the configured recovery bitrate for its delay. A manual scene change gives you temporary priority. Automatic protection is designed for an active OBS broadcast and protects the configured IRL scenes; testing while OBS is not broadcasting will not exercise the BRB handoff.

Configure OBS's platform output or save a destination in Studio. Use your own Twitch, YouTube, Kick or custom RTMP server and stream key. Chat authorization does not configure OBS output. Studio controls one OBS stream output at a time; simultaneous multi-platform broadcasting requires a separate OBS plugin or relay configured by you.

## 6. Add incoming friends or share your outgoing camera

**Ingests → Add** creates an incoming camera with a private publishing credential. Copy its connection details to that camera operator, give them network access, then connect/map its OBS scene. Do not use the main camera's publishing URL for every friend. Use Connect/Disconnect/Remove to control that source. Configure primary and backup feeds only after each camera is independently online.

**Share my feed** creates a read-only outgoing camera link that a friend can add to their own OBS. It is different from adding their incoming camera. Follow [Feed sharing](FEED_SHARING.md); the friend needs network access and their own OBS/platform setup. Each active recipient consumes more of your upload bandwidth.

## 7. Set up optional multichat and phone telemetry

Follow [Multichat setup](MULTICHAT.md) to register your own Twitch / Kick / Google applications, configure callback URLs, and authorize your own channels. YouTube API quota belongs to your Google project and still applies. Kick requires a reachable public HTTPS webhook receiver. Neither of these is set up by buying the toolkit.

Android ADB telemetry is optional. Install [Android platform tools](https://developer.android.com/tools/releases/platform-tools), authorize your phone over USB or your chosen private wireless ADB setup, and make `adb.exe` available at `C:\Android\platform-tools\adb.exe` or via the `SHXRM_ADB` environment variable before starting the app. Turn Phone Monitor off in Connections if unused. This release does not install drivers or pair wireless ADB automatically.

## Updates and uninstall

Go off air. Stop streaming, recording and replay outputs in Studio/OBS. Use **Stop server**, then run **STOP_SHXRM.bat** from the installed folder to close the native app and services. The script refuses to close a running media server if it cannot verify that OBS outputs have stopped. Install the new compiled release with the same destination; existing `Config` and MediaMTX configuration are preserved, and replaced files are backed up under `Backups`. An initial install with an older Python toolkit on the same ports requires closing that old toolkit first; the installer does not kill unrelated listeners.

To uninstall, deactivate the license first, stop services, remove your installation folder and shortcuts, and remove any firewall rules you created. Preserve a private copy of configuration if needed. Revoke chat application access in each platform's account settings. Do not delete or share `Config\license.dat` to move a license; use Deactivate and activate on the next PC.
