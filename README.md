# DeathByCaptcha Agent API Metadata

> **Purpose:** provide canonical, language‑agnostic metadata for the
> DeathByCaptcha service so that AI agents and tooling can understand or
> generate clients for *any* programming language. The repository does
> **not** aim to be a Python library; any Python code is purely a
> convenience for validating that the specifications map to the
> live API.

[![Validate APIs](https://github.com/deathbycaptcha/deathbycaptcha-agent-api-metadata/actions/workflows/validate-api.yml/badge.svg)](https://github.com/deathbycaptcha/deathbycaptcha-agent-api-metadata/actions/workflows/validate-api.yml)

## Repository Layout

```
/ (root)
├─ spec/
│   ├─ openapi/
│   │   ├─ http.yaml        # OpenAPI 3 HTTP API spec
│   │   └─ sockets.yaml     # AsyncAPI/TCP socket API spec
│   └─ jsonld/
│       ├─ http_context.jsonld
│       └─ socket_context.jsonld
└─ validation/              # optional tooling to exercise the API
    └─ python/
        ├─ tests/           # pytest suite (not required for spec use)
        └─ samples/         # placeholder images/audio used by tests
```

### Spec Files

- **OpenAPI**
  - `spec/openapi/http.yaml` – describes HTTP endpoints, parameters,
    request/response schemas, examples, etc.
  - `spec/openapi/sockets.yaml` – AsyncAPI/TCP description for the
    socket‑based API.

- **JSON‑LD Contexts**
  - `spec/jsonld/http_context.jsonld` – field names, datatypes and linked
    semantics for HTTP payloads.
  - `spec/jsonld/socket_context.jsonld` – similar context for socket
    messages.

Agents and code generators should parse these YAML/JSON‑LD artifacts directly;
these are the authoritative metadata and are intentionally self‑describing.

> 💡 *The specification is kept as close as possible to the public docs at*
> `https://deathbycaptcha.com/api` *and may serve as the single source of
> truth for tooling.*

### Validation (optional)

Although the repository is spec‑centric, there is a small Python project at
`validation/python` that exercises the live API using `pytest`. Its sole
purpose is to confirm that the specs remain accurate during development.
Nothing in `validation/python` is required for the primary use case and the
code there may be removed or ignored by consumers who simply need the
specs.

To run the validation suite you need Python 3.10+ and credentials.

A quick start:

```bash
cp .env.example .env           # create credential file at repo root
# edit .env to add DBC_USERNAME / DBC_PASSWORD
pip install -r validation/python/tests/requirements.txt
pytest validation/python/tests/ -v
```

The tests make minimal assumptions about API behaviour and are deliberately
lightweight.

---

## Agent‑friendly metadata

In addition to the formal OpenAPI and AsyncAPI specifications, the
repository includes several small artifacts that make it trivial for an
AI agent to grasp the service surface:

- **JSON‑LD contexts** (`spec/jsonld/http_context.jsonld` and
  `spec/jsonld/socket_context.jsonld`) describe the meaning and data types
  of individual fields.
- A simple **endpoint manifest** at
  `spec/metadata/api_manifest.jsonld` lists the primary HTTP paths,
  methods and human-readable descriptions.  This file can be regenerated
  from the OpenAPI spec using the helper script
  `validation/python/generate_manifest.py` or validated automatically via
  the pytest test `validation/python/tests/test_manifest.py`.

Agents may load the YAML/JSON‑LD directly, but the manifest provides a
quick start by surfacing the most important endpoints without parsing the
entire OpenAPI document.

## Contribution & CI

- Update the spec files whenever the DeathByCaptcha API changes.
- Optionally run the Python validation suite locally to catch drift.
- GitHub Actions and GitLab CI pipelines (see `.github/workflows/validate-api.yml` and
  `.gitlab-ci.yml`) use the validation tests; they trigger on changes to
  the `spec/` or `validation/python/tests/` directories.

