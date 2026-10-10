# Experimental Eufy recording-history add-on

This is an isolated **local-build** test add-on. It does not alter the installed stable add-on or its image.
Instead of publishing a custom GHCR image, Supervisor builds the official bridge `0.4.0` image
with two modified modules from commit `f72e40c137176db482567cdd7a1ddb6939ea4d0f`
of `zetard/ha-eufy-sdk-bridge`. Requires outbound access to GHCR and raw.githubusercontent.com.

## Before starting

1. Keep your original installed add-on and its settings. Make an HA backup if practical.
2. Install this test add-on, but leave it **stopped** while the original is running.
3. Copy your Eufy account settings securely through Home Assistant's Configuration tab. The test
   add-on has separate persistent storage and may require reauthentication or 2FA.
4. For the test window, **stop the original bridge first**, then configure this add-on's
   Network port mappings to match the original (for example, control 3030 and RTSP 8556).
5. Start the test add-on. Confirm Eufy login and the `/healthz` endpoint.
6. Trigger two separate Front Door motion events. Request the new endpoints from a trusted local client:

   - `GET http://<HA-IP>:3030/recordings/<camera-serial>`
   - `GET http://<HA-IP>:3030/recordings/<camera-serial>/<recording-id>`
   - `GET http://<HA-IP>:3030/clip/<camera-serial>` (existing latest clip)

   IDs must come from the list response. Check the clips correspond to different motion events.
   The index is limited to 50 pushes per camera **received while this test bridge runs**,
   not the entire historical HomeBase library.

## Rollback

Stop the test add-on and restart the original `b0e7d1ed_eufy_sdk_bridge` add-on.
Restore the original port mappings/configuration if you changed them. Verify Home Assistant's
Eufy integration, camera streaming, notifications and Home Now afterwards.
Never run both add-ons simultaneously against the same account: they can displace each other's
sessions and event push delivery.

## Security

Both existing and new raw HTTP endpoints are **unauthenticated**. HA ingress provides auth,
but the separately mapped TCP control port does not. Do not expose it to the internet.
Video recordings and the recording index (including cipher references) are sensitive household data.
The new index is stored privately under the test add-on's `/data` volume.

See [bridge API notes](https://github.com/zetard/ha-eufy-sdk-bridge/blob/feature/event-recording-history/docs/recording-history.md).
