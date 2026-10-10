# Local-build test channel: official bridge runtime plus two pinned feature modules.
ARG BUILD_FROM=ghcr.io/mega-yfue/ha-eufy-sdk-bridge:0.4.0
FROM ${BUILD_FROM}
RUN apk add --no-cache curl jq
ARG BRIDGE_COMMIT=f72e40c137176db482567cdd7a1ddb6939ea4d0f
RUN set -eu; \
    curl -fsSL --retry 3 "https://raw.githubusercontent.com/zetard/ha-eufy-sdk-bridge/${BRIDGE_COMMIT}/src/clip.mjs" -o /app/src/clip.mjs; \
    curl -fsSL --retry 3 "https://raw.githubusercontent.com/zetard/ha-eufy-sdk-bridge/${BRIDGE_COMMIT}/src/http-routes.mjs" -o /app/src/http-routes.mjs; \
    node --check /app/src/clip.mjs; \
    node --check /app/src/http-routes.mjs
COPY run.sh /run.sh
RUN chmod +x /run.sh
LABEL org.opencontainers.image.source="https://github.com/zetard/ha-eufy-sdk-addon"
CMD ["/run.sh"]
