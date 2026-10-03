# Test before going live

Use a private/unlisted or otherwise appropriate test broadcast. Do not test deliberate disconnections during a public production stream.

- Launch the native app; activation status is allowed. Relaunch without consuming another computer slot.
- Create an owner login and sign in from your phone over Tailscale. An unauthenticated browser cannot operate controls or see your private camera.
- Connect OBS; scene list and stream/record/replay states are available. Start/stop each needed output and confirm it in OBS.
- Start the encoder; primary ingest reports ready/online and OBS receives video/audio. Preview works on desktop and phone; test both WebRTC and HLS fallback.
- Enable protection with existing normal/BRB scenes. During a test OBS broadcast, stop the encoder, wait the offline delay, verify BRB, restart the encoder, wait recovery delay, verify the camera returns. Confirm the destination broadcast stays active during this handoff.
- If using low-bitrate protection, test the actual thresholds without mistaking missing telemetry for a dropped camera.
- Add one guest camera, create its OBS scene, then disconnect/connect/reset/remove it. Other feeds should continue.
- Share one camera to a friend's OBS. Verify they receive audio/video, cannot publish with the viewing key, and lose access when the share is disconnected.
- Connect each chat platform using your own registrations. Receive a platform test message, reply to that platform, disconnect, and verify it stops. For YouTube, test an active broadcast with chat; for Kick, confirm real signed webhook delivery.
- Turn optional connections off/on. Verify disabled services stay disabled through infrastructure recovery and app restart.
- Stop all outputs, close services, install the next release, and verify configuration persists.
- On a spare test machine, verify no-key/wrong-key access is blocked; test deactivation, activation limit, transfer, temporary license-service outage, and a disabled key. Keep one test key separate from paying customers.

These checks cover your equipment, accounts, network and store. Automated project tests do not replace a Windows/OBS/phone test or a real purchase/activation test.
