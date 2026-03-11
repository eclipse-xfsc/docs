# xfsc-asciidoctor

Docker image for rendering AsciiDoc documents with Mermaid diagram support. Based on the official [asciidoctor/docker-asciidoctor](https://github.com/asciidoctor/docker-asciidoctor) image, extended with Chromium and the Mermaid CLI (`mmdc`).

## Build

```bash
docker build -t xfsc-asciidoctor docs/tools/asciidoctor/
```

## Prerequisites

Some image files in the repository are stored with **Git LFS**. If they haven't been fetched yet, the renderer will warn about "unrecognised format" images. Pull LFS objects before rendering:

```bash
git lfs pull
```

## Usage

Run from the repository root so that all doc sources are available inside the container.

### HTML output

```bash
docker run --rm -v "$(pwd)":/documents xfsc-asciidoctor \
  asciidoctor -r asciidoctor-diagram path/to/document.adoc
```

### PDF output

```bash
docker run --rm -v "$(pwd)":/documents xfsc-asciidoctor \
  asciidoctor-pdf -r asciidoctor-diagram path/to/document.adoc
```

### Example — render the architecture doc

```bash
docker run --rm -v "$(pwd)":/documents xfsc-asciidoctor \
  asciidoctor -r asciidoctor-diagram \
  ./federated-catalogue/src/docs/architecture/catalogue-architecture.adoc
```

Note that the path is relative to the directory you run the command from (pwd). 
The above example assumes you're in the repository root. 
Adjust the path accordingly if you're running it from somewhere else.

The rendered file will be written next to the source (e.g. `catalogue-architecture.html`).

## What's included

| Component | Purpose |
|---|---|
| asciidoctor | AsciiDoc → HTML renderer |
| asciidoctor-pdf | AsciiDoc → PDF renderer |
| asciidoctor-diagram | Processes embedded diagram blocks (Mermaid, PlantUML, etc.) |
| @mermaid-js/mermaid-cli (`mmdc`) | Renders Mermaid diagrams to SVG/PNG |
| Chromium | Headless browser used by Mermaid CLI via Puppeteer |