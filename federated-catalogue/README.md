# Architecture Document — XFSC Federated Catalogue

The architecture is built by
the [buildDocs workflow](https://github.com/eclipse-xfsc/docs/actions/workflows/buildDocs.yml) in the
`eclipse-xfsc/docs` repository. Each workflow run uploads a `Documentation.zip` artifact containing the rendered static
HTML and PDF; download it from the "Artifacts" section of the most recent successful run.

> TODO: replace the workflow-run download with a stable, versioned public URL (e.g. GitHub Pages or a release asset)
> once that publication channel is in place.

## About the Catalogue

The XFSC Federated Catalogue manages metadata objects — typically verifiable credentials or RDF descriptions of
providers, service offerings, and resources — throughout their life cycle and exposes them to consumers. It verifies
these objects against schemas and trust anchors.

The current generation of the catalogue modularizes credential verification against trust anchors and generalizes
metadata-object management beyond credentials, enabling reuse as a template repository for adjacent services.

The full functional and non-functional specification is published in the same documentation repository and linked from
the rendered website above.

The reference implementation lives
at [eclipse-xfsc/federated-catalogue](https://github.com/eclipse-xfsc/federated-catalogue).
