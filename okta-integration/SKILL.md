---
name: okta-integration
description: Okta as a SAML 2.0 identity provider. Use when implementing, debugging, or configuring Okta SAML for a web app (ACS, metadata, audience, SP- vs IdP-initiated flows, RelayState).
disable-model-invocation: true
---
# Okta integration (SAML 2.0)

Use this when implementing, debugging, or configuring **Okta as a SAML 2.0 identity provider** for a web application. Typical setups pair Okta (IdP) with a **service provider** (your API or monolith) that exposes an **Assertion Consumer Service (ACS)** endpoint and optionally a **single-page app** that starts login or handles redirects after session establishment.

---

## Common implementation styles

| Style | Idea |
| --- | --- |
| **Metadata-driven** | At runtime or deploy time, load Okta’s SAML metadata (URL or XML) and feed it into a SAML library (e.g. python3-saml / OneLogin) so Entity ID, SSO URL, and signing certs stay aligned with Okta. |
| **Explicit configuration** | Store IdP Entity ID, SSO URL, and signing certificate in environment variables or secrets; validate assertions with a dedicated validator. Operators often copy values **from** the metadata document even if the app never fetches the URL. |

Both are valid; metadata-driven reduces copy-paste errors when Okta rotates certificates.

---

## Okta SAML metadata URL (single source of truth)

Okta publishes a **SAML 2.0 metadata XML document** at a stable HTTPS URL. That document is the usual place to obtain **everything** needed to configure or verify the service provider:

- **IdP Entity ID** — `EntityDescriptor` `@entityID` (often mapped to something like `SAML_IDP_ENTITY_ID` or equivalent in your config).
- **SSO URL** — `SingleSignOnService` with binding `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect` (and sometimes HTTP-POST); this is the browser redirect target for SP-initiated login (may also appear in frontend env as a “SAML SSO URL” if the SPA triggers login).
- **Signing certificate(s)** — `KeyDescriptor use="signing"` → `X509Certificate`. You may store PEM/base64 in secrets or rely on metadata parsing so rotation is easier.
- **NameID formats** — what Okta may send as the subject (often email).
- **Bindings and endpoints** — confirms redirect vs POST for SSO.

**Where to find the URL in Okta (admin):** open the **SAML 2.0** app integration → **Metadata URL** / **Identity Provider metadata** (labels vary). The path usually resembles:

`https://<your-okta-host>/app/<app-name-or-id>/<key>/sso/saml/metadata`

`<your-okta-host>` may be your Okta org subdomain (e.g. `dev-12345.okta.com`) or a **custom domain**. Treat the metadata URL as **environment-specific**: separate dev, staging, and production apps each have their own metadata.

**Using metadata in code:** many stacks parse metadata with a helper (e.g. `OneLogin_Saml2_IdPMetadataParser.parse_remote(url)` or `parse(xml)`). That fills IdP settings in the SAML library. If your deployment uses only env vars, **open the same metadata** once and transcribe Entity ID, SSO URL, and signing cert into secrets—those values must stay in sync with what the metadata shows.

---

## Architecture notes

- **ACS URL** belongs on the **backend** (or SAML-handling service), not on a static file host, when the browser POSTs the SAML response to your app. The exact string must match what is registered in Okta and what your validator expects for **Recipient** / **Destination** (library-dependent).
- **Audience** (SP Entity ID / Audience URI in Okta) must match what your application allows when validating `<saml:Audience>` in the assertion.
- **Multi-environment deployments:** if the same stored ACS or SP Entity ID is rebased to the current host (scheme + host), verify that rebasing matches what Okta sends—**Destination** mismatches are a frequent failure mode. Strip default ports (`:443` / `:80`) when comparing URLs if your stack or load balancer normalizes hosts.
- **RelayState:** SP-initiated flows often put opaque or signed state in `RelayState` (CSRF protection). IdP-initiated flows (dashboard tile) may send different or empty RelayState; design validation so both paths are acceptable if your product supports them.

---

## Quick debugging checklist

1. **Metadata URL** — fetch with a browser or `curl`; confirm Entity ID, SSO URL, and signing cert match your configured values.
2. **ACS** — must match exactly what Okta POSTs to (path, host, scheme).
3. **Audience** — must match Okta’s Audience URI and your allowed audience list.
4. **Clock skew** — large skew breaks `NotBefore` / `NotOnOrAfter`; many libraries allow a small drift (e.g. a couple of minutes).
5. **RelayState** — confirm SP-initiated vs IdP-initiated behavior matches your implementation.

---

## Frontend (optional)

If a SPA initiates SAML login or needs the IdP SSO URL at build time, inject it via your bundler’s environment mechanism (names vary by tool). The SSO URL should match the **HTTP-Redirect** SSO endpoint from Okta’s metadata for SP-initiated login when that is the binding you use.
