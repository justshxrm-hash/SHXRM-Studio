# Data and accounts

SHXRM's dashboard and control services run on your Windows streaming PC. OBS credentials, stream destination keys, guest/share credentials, owner password hashes, and chat OAuth settings/tokens are stored under that PC's `Config` folder. Protect the Windows account and private backups. Most configuration is local JSON; the activation record is additionally encrypted with Windows DPAPI and checked for integrity. Chat history is held in memory and cleared when the dashboard process restarts.

For activation/validation/deactivation, the app sends your purchased key and computer instance identifier to Lemon Squeezy over HTTPS. The initial instance name is derived from a hash of the Windows machine identity; the raw Windows MachineGuid is not sent. Purchase details are handled by the publisher's Lemon Squeezy store under its published policies. Full keys and customer details are not returned to the Studio browser or intentionally logged by the license client.

Connecting chat sends the necessary OAuth/API requests to Twitch, Kick or Google. Kick events reach the public HTTPS receiver you configure. Tailscale and any reverse proxy/cloud service have their own policies. SHXRM does not include a centrally operated chat broker, cloud ingest, or analytics collector.

Deactivate before moving PCs. Disconnect/revoke platform authorizations when uninstalling. Removing local files does not cancel a purchase, remove a remote provider instance, or revoke platform access by itself.
