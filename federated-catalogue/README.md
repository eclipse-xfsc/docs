# Architecture document for the XFSC Federated Catalogue

## XFSC Federated Catalogue

The rendered Architecture Document (PDF and full HTML rendering) is published as a **release asset** of this
repository.

### Download

Each documentation status is published as a release of this repository, tagged `cat-architecture-*`. The
newest one is the current status; the older ones stay retrievable.

**[→ Architecture Document releases](https://github.com/eclipse-xfsc/docs/releases?q=cat-architecture&expanded=true)**

Every release carries the document as `catalogue-architecture.pdf` and as `catalogue-architecture-html.zip`,
a ZIP of the complete HTML rendering. Release assets never expire, need no login, and are unaffected by the
90-day retention limit that applies to GitHub Actions artifacts.

To read the HTML rendering, unpack the archive and open `html5/architecture/catalogue-architecture.html` in
a browser. All images and generated diagrams are contained in the archive and the main stylesheet is embedded
in the page, so the document is complete and correctly laid out without a network connection. Three
supplementary stylesheets are still loaded from CDNs (Google Fonts, Font Awesome, highlight.js); without
network access the admonition icons and the syntax colouring of code blocks are lost, nothing else.

### How the publication works

The [`Run docToolchain`](https://github.com/eclipse-xfsc/docs/actions/workflows/buildDocs.yml) workflow
(`.github/workflows/buildDocs.yml`) runs on every push to `main` that touches `federated-catalogue/`. It
builds the document from the AsciiDoc source in `federated-catalogue/src/docs/` via docToolchain
(`generateHTML`, `generatePDF`) and then publishes it. The workflow runs as two jobs: `build` renders and
packages, `release` creates the release. The four publication steps are:

1. **Package** — the PDF and a ZIP of the whole HTML output directory.
2. **Draft** — a release is created as a *draft* under a new tag
   `cat-architecture-<date>-<short commit SHA>`, for example `cat-architecture-2026-09-14-4be1769`.
   The SHA is the commit **of this repository** that produced the rendering — the state of the AsciiDoc
   source, not a version of the Federated Catalogue implementation. `cat` names the component the document
   describes; it does not tie the release to any release of that component.
3. **Attach** — both assets are uploaded while the release is still a draft.
4. **Publish** — the draft is published. Assets are attached *before* publication because GitHub's
   [immutable releases](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases)
   forbid adding, replacing or removing assets afterwards; a workflow built on overwriting would break the
   moment that setting is switched on.

Points worth knowing before changing any of this:

- **Each change gets its own release.** Existing releases are never modified. Tag names are permanently
  consumed — GitHub does not release a tag name even after the release is deleted — so the short commit SHA
  in the tag keeps every publication unique.
- **Never link `.../releases/latest`.** That URL is repository-wide: it resolves to whichever component
  published most recently, so the link would silently start pointing at another component's release. The
  workflow publishes with `make_latest=false` so the snapshot does not actively claim the marker, but GitHub
  still derives `.../releases/latest` from the newest published release when no release claims it -- the
  marker can be left unclaimed, not empty.
- **The workflow uses only the built-in `GITHUB_TOKEN`.** No personal access token is involved: those are
  tied to an individual, expire, and would fail silently — nobody would notice until someone went looking
  for a document that had not been updated in months.
- **Write access is confined to the `release` job.** The workflow grants `contents: read` by default; only
  `release` raises it to `contents: write`, and it runs nothing but `gh api` calls. The `build` job — which
  executes third-party code (npm, docToolchain, Chrome) — therefore never has a token that could change
  this repository, and its checkout uses `persist-credentials: false` so no token is left in `.git/config`
  while that code runs. Keep the split when changing the workflow: moving a release step back into `build`
  silently widens what the build tooling can reach.
- **The Mermaid CLI is pinned** to an exact version and installed with `--ignore-scripts`. Unpinned, every
  run would depend on whatever is current, so the rendered diagrams could change without a commit. Raise the
  version deliberately and check the diagrams afterwards. `--ignore-scripts` blocks the lifecycle scripts of
  its transitive packages; the only one that matters is Puppeteer's browser download, and the build uses the
  runner's own Chrome instead. That leaves no bundled browser to fall back on, which is why Chrome is
  resolved in one step that fails the run when it is missing, and why the mmdc smoke test is no longer
  allowed to pass silently.
- **The workflow writes only to this repository.** There are no cross-repository writes anywhere.
- **The workflow never pushes to this repository.** It only creates a release through the API. Nothing in
  this README has to be regenerated when a release is published, which is why the link above points at the
  release listing rather than at an asset URL: an asset URL carries the release tag and would go stale.
- **The short-lived `Documentation` artifact remains** on each workflow run for quick access from the Actions
  tab. It is a convenience, not the durable copy.

Immutability protects a release against modification, not against deletion. If a guaranteed long-term
availability is required, a copy has to be kept outside GitHub as well.

## About the Catalogue

The XFSC Federated Catalogue manages metadata objects — typically verifiable credentials or RDF descriptions of
providers, service offerings, and resources — throughout their life cycle and exposes them to consumers. It verifies
these objects against schemas and trust anchors.

The current generation of the catalogue modularizes credential verification against trust anchors and generalizes
metadata-object management beyond credentials, enabling reuse as a template repository for adjacent services.

The full functional and non-functional specification is published in the same documentation repository and linked from
the rendered document above.

The reference implementation lives
at [eclipse-xfsc/federated-catalogue](https://github.com/eclipse-xfsc/federated-catalogue).
