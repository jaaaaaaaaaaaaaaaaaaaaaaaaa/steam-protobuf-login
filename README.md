# SteamQRLogin (Python)

Minimal Steam QR login via Valve’s modern auth flow (`IAuthenticationService`). Starts a QR auth session, prints a QR image URL to scan in Steam Guard, then polls until you approve the login in the Steam mobile app and returns access/refresh tokens.

## Usage

### Run as a script

```bash
python qr_login.py
```

You’ll see a Challenge URL and a QR Image URL. Open the QR image URL, scan it with Steam Guard, then approve the login. The script prints the tokens once complete.

### Use as a module

```python
from qr_login import SteamQRAuthenticator, build_qr_image_url

auth = SteamQRAuthenticator(timeout=15.0)

qr_session = auth.start_session_with_qr(device_friendly_name="Python QR login")
print("Challenge URL:", qr_session.challenge_url)
print("QR Image URL :", build_qr_image_url(qr_session.challenge_url))

poll_result = auth.wait_for_completion(qr_session, timeout=300.0, verbose=True)

print("steamid       :", poll_result.get("steamid"))
print("account_name  :", poll_result.get("account_name"))
print("access_token  :", poll_result.get("access_token"))
print("refresh_token :", poll_result.get("refresh_token"))
print("had_remote_int:", poll_result.get("had_remote_interaction"))
```

## Outputs

PollAuthSessionStatus result dict typically includes:

- steamid
- account_name
- access_token
- refresh_token
- had_remote_interaction
- state (may be present)

## Notes

- Poll interval is provided by Steam (defaults to ~5s if not included).
- The QR “image URL” is generated using api.qrserver.com from the challenge_url. If you prefer, generate the QR locally from challenge_url instead.
- Keep tokens private; do not commit them.
