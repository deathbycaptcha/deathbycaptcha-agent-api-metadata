# API Specification and Validation
[![Validate APIs](https://github.com/deathbycaptcha/deathbycaptcha-agent-api-metadata/actions/workflows/validate-api.yml/badge.svg)](https://github.com/deathbycaptcha/deathbycaptcha-agent-api-metadata/actions/workflows/validate-api.yml)
This directory is optimized for **AI agents** and other automated tools
that need to build metadata about the DeathByCaptcha services. It contains
formal specs and simple validation utilities for DeathByCaptcha APIs used
by the Python client.

> ℹ **Reference:** the implementation follows the public API documented at
> `https://deathbycaptcha.com/api` – use it as a cross-check when consuming
> or extending these definitions.

## Specifications

The repository layout is intentionally structured so an automated agent can
crawl the metadata and assemble a complete service description. Key
artifacts include:

- `openapi_http.yaml` – OpenAPI 3 description of the HTTP API. Contains
  endpoints, parameters, schemas and example payloads.
- `openapi_sockets.yaml` – AsyncAPI/TCP description of the socket API.
- `jsonld/http_context.jsonld` – JSON-LD context defining field names,
  datatypes, and linked semantics for HTTP request/response payloads.
- `jsonld/socket_context.jsonld` – JSON-LD context for socket messages.

An AI agent should inspect these YAML/JSON-LD files directly; they are
self-describing and provide the canonical metadata needed to generate
client code, documentation, or other models.

A small set of sample images/audio used by the validators are copied into
`samples/` for self-containment. The directory contains `normal.jpg`,
`banner.jpg`, `audio.mp3` and a few auxiliary pictures.

## Validation Tests

Test suite in `tests/` exercises both HTTP and socket APIs using pytest.
**Credentials are loaded from environment variables or a `.env` file** to
avoid committing sensitive data. A sample `.env.example` is provided; the
`.gitignore` excludes `.env` so secrets stay local.

### Running Tests Locally

1. Duplicate and configure the environment file:

   ```bash
   cp .env.example .env
   # Edit .env to add your DBC_USERNAME and DBC_PASSWORD
   ```

2. Install test dependencies:

   ```bash
   pip install -r tests/requirements.txt
   ```

3. Run all tests:

   ```bash
   pytest tests/ -v
   ```

### Continuous Integration

Tests run automatically on **daily schedule** and on relevant code changes.

#### GitHub Actions

See `.github/workflows/validate-api.yml`. To enable:

1. Go to **Settings → Secrets and variables → Actions** in your repo.
2. Add secrets:
   - `DBC_USERNAME` – test account username
   - `DBC_PASSWORD` – test account password

View results in the **Actions** tab.

#### GitLab CI/CD

See `.gitlab-ci.yml`. To enable:

1. Go to **Settings → CI/CD → Variables** in your GitLab project.
2. Add variables (mark as **Protected** if desired):
   - `DBC_USERNAME` – test account username
   - `DBC_PASSWORD` – test account password
3. Optionally, create a **Pipeline Schedule** under **CI/CD → Schedules** for
   daily validation at 00:00 UTC.

Tests run on every push to `main`/`develop` and on merge requests. View
results under **CI/CD → Pipelines**.

### Test Coverage

Tests exercise HTTP and socket APIs across:
- **Authentication:** login, user info, balance checks
- **Regular images:** standard captcha uploads
- **Coordinates:** click-based captcha types
- **Image groups:** multi-image/banner captchas
- **Token APIs:** reCAPTCHA v2 and v3

Tests send minimal placeholder payloads. An HTTP 501 or `status` >= 250 indicates
a malformed request or missing parameters; tests assert these don't occur for
core types. For specialized types (Geetest, Turnstile, etc.) that require
provider-specific credentials, 501 is expected.
