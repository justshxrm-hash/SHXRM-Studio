# Multichat: bring your own Twitch, Kick and Google applications

Open **Multichat** in the workspace navigation. All three platforms can stay connected at the same time. The app combines incoming messages with platform labels and lets you choose exactly one platform for each reply. The last 500 messages stay in memory; they are cleared when the dashboard process restarts. No conversation history is written to disk.

## One-time account setup

This is a self-hosted app, with no SHXRM-hosted OAuth broker or shared developer credentials. Create your own developer application for each platform, enter its client ID and client secret under **One-time app setup**, save, and press **Connect** to authorize the channel owner account. Do not put stream keys in these fields. Developer credentials are different from stream keys.

The simplest first connection is on your streaming PC, using the localhost callback URLs below. Register each URL exactly, including port and path, in that platform's developer console. The native desktop Connect button opens the system browser for authorization. After authorization, return to Multichat; its status updates automatically.

| Platform | Developer application | Callback on your streaming PC |
| --- | --- | --- |
| Twitch | https://dev.twitch.tv/console/apps | `http://localhost:8788/oauth/twitch/callback` |
| Kick | https://kick.com/settings/developer | `http://localhost:8788/oauth/kick/callback` |
| YouTube | Google Cloud OAuth client, type **Web application** | `http://localhost:8788/oauth/youtube/callback` |

Once connected on your streaming PC, chat and sending work from your authenticated mobile dashboard while your streaming PC and the toolkit remain running. A localhost callback opened on your phone points at your phone, so use your streaming PC for initial authorization and reauthorization. To authorize from mobile, register and save a reachable HTTPS dashboard callback accepted by the provider; domain/consent verification requirements depend on the provider. Your existing Tailscale access can continue serving your private dashboard.

Disconnect removes the local account tokens immediately, disables sending and stops accepting messages for that connection. Saved developer app settings stay for reconnection. To revoke the app's authorization at the platform itself, use the platform account's authorized-app settings. Kick subscriptions may remain registered remotely after local disconnect, but received events are ignored while disconnected.

### Twitch

Register an application and authorize your own Twitch channel. Studio requests `user:read:chat` and `user:write:chat`, receives messages through the official EventSub WebSocket, validates the account token, and renews tokens when possible. No public incoming webhook is needed. A connection interruption triggers automatic reconnection; messages missed during a full disconnect are not guaranteed to be replayed. Chat restrictions can still prevent a reply.

Official references: https://dev.twitch.tv/docs/authentication/register-app/ · https://dev.twitch.tv/docs/chat/authenticating/ · https://dev.twitch.tv/docs/eventsub/handling-websocket-events/

### YouTube

In Google Cloud, enable **YouTube Data API v3**, configure the OAuth consent screen, and create a **Web application** OAuth client. If your app is in testing, add the channel owner's Google account as a test user. Authorize the Google/Brand Account that owns the live broadcast. Studio requests the `youtube.force-ssl` scope, which permits reading and posting chat.

The account can be connected while offline. Studio waits for an active broadcast with live chat and checks for one approximately every minute. When live, it polls messages no faster than every 10 seconds and honors any longer interval returned by YouTube. This trades some latency for fewer requests; it is not instant chat. API quota still applies and long streams may exhaust a default project quota. If several active broadcasts are returned, Studio asks you to resolve the ambiguity instead of silently sending to one. Google testing-mode authorizations may expire and require Connect again.

Official references: https://developers.google.com/identity/protocols/oauth2/web-server · https://developers.google.com/youtube/v3/live/docs/liveBroadcasts/list · https://developers.google.com/youtube/v3/live/docs/liveChatMessages/list · https://developers.google.com/youtube/v3/live/docs/liveChatMessages/insert

### Kick — public webhook required to receive chat

Kick delivers chat to a public HTTPS webhook. Account sign-in by itself does not make incoming messages available.

1. Set up a public HTTPS endpoint ending in `/kick/events`, forwarded to **127.0.0.1:8793** on your streaming PC. This dedicated listener serves only Kick events. Your chosen HTTPS reverse proxy/tunnel must preserve the raw body and Kick event headers.
2. Enable webhooks in the Kick developer app and register that exact public URL there.
3. Enter the same public webhook URL in Studio's Kick setup, save, then Connect the channel owner account.
4. Send a test message through Kick's own website. Studio changes from **Subscribed · waiting for first chat event** to **Receiving chat** only after a valid signed event arrives.

The package does not deploy a public tunnel, domain, or cloud receiver. Private Tailscale access alone is not reachable by Kick's servers. Expose the dedicated receiver through your chosen public HTTPS service; the private dashboard does not need to be exposed. The webhook URL field records the setup; it does not configure a tunnel or change Kick's developer settings for you.

Studio verifies Kick's RSA signature over the event ID, timestamp and raw body; rejects stale/tampered events; filters for the authorized broadcaster; and deduplicates delivery. A created subscription proves registration, not delivery. If messages never arrive, check webhook enablement, public forwarding, your streaming PC availability and system clock. No unofficial chat scraping is used.

Official references: https://docs.kick.com/getting-started/generating-tokens-oauth2-flow · https://docs.kick.com/events/webhook-security · https://api.kick.com/swagger/doc.yaml

## Troubleshooting and limits

- **Connection needs attention:** read the provider's error. Check credentials, authorization, internet access, API enablement and quota. Reconnect if authorization was revoked or expired.
- **Send failure:** the draft stays in the composer. Sends are not retried automatically because a lost response can follow a successful send; check the platform chat before retrying.
- **YouTube waiting:** connected account is not necessarily live. The active broadcast must have live chat enabled.
- **Kick subscribed but empty:** verify the public webhook; this is separate from successful account authorization.
- **Changes to developer settings:** disconnect that provider, edit its setup, save, then reconnect.
- **Restart:** saved connections resume automatically. The app keeps developer secrets and OAuth tokens in `Config/multichat.json` on your streaming PC. Protect that Windows account and configuration folder, and exclude this file from public uploads. Tokens are not returned to the browser.
- These are owner-channel connections: one account per platform. This release does not include moderation actions, emote rendering, third-party channel selection, or automatic cross-posting.

Validation uses mocked platform APIs, signed local webhook fixtures and real Chromium UI interaction. Actual Twitch/Kick/Google authorizations, public webhook delivery and Windows installation require your own accounts and PC; they were not exercised on your behalf.
