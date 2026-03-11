# XFSC Documentation

This repository contains the XFSC documentation about the components, architecture and more.

## Rendering docs locally

A Dockerized AsciiDoc toolchain with Mermaid diagram support is available under [`tools/asciidoctor/`](tools/asciidoctor/). Build the image once, then use it to render any document:

```bash
# Build
docker build -t xfsc-asciidoctor tools/asciidoctor/

# Render to HTML
docker run --rm -v "$(pwd)":/documents xfsc-asciidoctor \
  asciidoctor -r asciidoctor-diagram path/to/document.adoc
```

See the [full README](tools/asciidoctor/README.md) for PDF output and more examples.