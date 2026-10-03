# Troubleshooting

| Symptom | Check |
| --- | --- |
| Publisher setup required | The release has no valid embedded store/product settings. The publisher must rebuild it; buyers cannot configure payment IDs. |
| License rejected / computer limit | Copy the receipt key exactly, check its term and limit, deactivate the old PC, or contact the publisher to remove an obsolete instance. |
| Online validation required | Restore internet access and correct Windows time, then use Check activation. Offline grace is bounded. |
| Activation response lost | Contact the publisher before repeated retries; a provider instance may have been created. |
| Port already in use | Stop the previous toolkit while off air. Check listening processes; do not delete the old install or kill all Python processes. |
| Desktop window cannot open | Install WebView2 Runtime; inspect `Logs`. Services can be accessed at `http://127.0.0.1:8788` while running. |
| OBS disconnected | Enable OBS WebSocket 4455 authentication; run OBS and SHXRM as the same Windows user; reconnect. |
| Feed offline | Check encoder publishing, Tailscale routing, UDP 8890 firewall rule, stream ID, and the saved ingest host. |
| Metrics error / 9998 refused | Verify MediaMTX API 9997 and metrics 9998 are enabled on loopback. Missing metrics should not report a healthy camera as offline. |
| Mobile preview error | Reconnect preview; try HLS; verify ICE additional hosts and TCP/UDP 8189 access. |
| BRB never switches | Enable and save protection; map exact existing scenes; test while OBS broadcasts the managed normal scene. |
| Camera never returns | Verify fresh online ingest and recovery bitrate/delay; keep OBS Media Source reconnect enabled. |
| Multichat sign-in fails | Register the exact callback on that platform, enter your own client ID/secret, authorize from the streaming PC, and inspect provider status. |
| YouTube quota exhausted | Check the buyer's Google Cloud quota and active chat polling. Do not create projects to evade platform limits. |
| Kick connects but no messages | Account authorization is separate from public HTTPS webhook delivery. Check the dedicated receiver and signed-event forwarding. |
| Friend cannot receive camera | Share the streaming PC through Tailscale, grant port access, send the private generated read URL, and ensure your source is online. |

When requesting help, include release version, Windows/OBS version, the affected feature, and a sanitized error. Do not post activation keys, OAuth credentials/tokens, destination keys, private camera URLs, `Config`, or complete logs without reviewing them.
