FROM asciidoctor/docker-asciidoctor
LABEL org.opencontainers.image.title="xfsc-asciidoctor"

# Install Chromium (native ARM/x86 via Alpine) and Mermaid CLI.
# PUPPETEER_SKIP_DOWNLOAD prevents Puppeteer from pulling its own
# x86-only Chromium binary — we use the system package instead. (Fix for arm64 host architectures)
ENV PUPPETEER_SKIP_DOWNLOAD=true
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

RUN apk add --no-cache nodejs npm chromium font-noto \
    && npm install -g @mermaid-js/mermaid-cli \
    && npm cache clean --force

# Puppeteer config for mmdc: --no-sandbox is required inside containers due to
# Linux kernel namespace restrictions (applies to root and non-root users alike).
RUN cat <<'EOF' > /usr/local/share/puppeteer-config.json
{
  "executablePath": "/usr/bin/chromium-browser",
  "args": ["--no-sandbox", "--disable-gpu", "--disable-dev-shm-usage"]
}
EOF

# Wrap mmdc so it always passes -p with the puppeteer config.
# asciidoctor-diagram calls mmdc directly, and older versions don't forward
# the puppeteerConfigFile attribute, so a wrapper is the most reliable fix.
RUN REAL_MMDC=$(which mmdc) \
    && mv "$REAL_MMDC" "${REAL_MMDC}.real" \
    && printf '#!/bin/sh\nexec "$(dirname "$0")/mmdc.real" -p /usr/local/share/puppeteer-config.json "$@"\n' > "$REAL_MMDC" \
    && chmod +x "$REAL_MMDC"

# Run as a non-root user to reduce the attack surface of the container.
RUN addgroup -S asciidoc && adduser -S -G asciidoc asciidoc
USER asciidoc
