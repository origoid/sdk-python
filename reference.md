# Reference
## Authentication
<details><summary><code>client.authentication.<a href="src/origoid/authentication/client.py">issue_token</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** Free — authentication endpoint.

Issues a short-lived Bearer JWT from valid API Key or Basic credentials. The token can then be sent as `Authorization: Bearer <access_token>` on subsequent requests instead of resending your long-lived API Key. Useful for handing access to a downstream client without sharing your primary credentials.

**Auth:** API Key (`x-api-key`) or HTTP Basic. Bearer tokens cannot issue new tokens (no token chaining).

**Body:** entirely optional. Send `{}` to get a token with default lifetime.

**Lifetime:** controlled by `expireAfter` (seconds). When omitted, the gateway uses its configured default (currently 3600). When present, it must be between 1 and the configured maximum (currently 3600). Values outside that range are rejected with `OUT_OF_RANGE`.

**Revocation:** tokens are stateless and self-expiring. There is no revocation endpoint — if a token is compromised, rotate the underlying API Key. The next snapshot reload propagates the rotation across all gateway instances within ~5 minutes.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.authentication.issue_token()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**expire_after:** `typing.Optional[int]` — Token lifetime in seconds. Optional. When omitted the gateway uses its configured default (currently 3600). Values outside [1, 3600] are rejected with `OUT_OF_RANGE`.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## RENAPO
<details><summary><code>client.renapo.<a href="src/origoid/renapo/client.py">validate_curp</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates a CURP (Clave Única de Registro de Población) against the official RENAPO registry and returns the full personal record associated with it: given names, surnames, gender, date of birth, birth state, document status (active, deceased, apocryphal, judicial suspension), and registration metadata.

Optionally generates the associated 13-character RFC (Registro Federal de Contribuyentes) when `generateRfc: true` is sent. RFC generation is deterministic from CURP and does not call SAT.

Use this endpoint when you have a CURP and need to confirm it is genuine, find out who owns it, or detect if the holder is deceased before extending a financial product.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.renapo.validate_curp(
    curp="TEST900101HDFRRN09",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**curp:** `str` — CURP to validate (18 characters).
    
</dd>
</dl>

<dl>
<dd>

**generate_rfc:** `typing.Optional[bool]` — Generate RFC associated with the CURP.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.renapo.<a href="src/origoid/renapo/client.py">lookup_curp</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 2 per call.

Reconstructs a CURP from the four official input fields: given names, first surname, second surname, gender, date of birth, and birth state code. Calls RENAPO and returns the matching CURP plus the full personal record (same shape as `validateCurp`).

Use this endpoint when your KYC form collects names and date of birth but not the CURP, and you need the CURP to file a financial product or report to regulators. The lookup uses RENAPO's strict matching — if any field is misspelled, no match is returned (`CURP_NOT_FOUND`).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.renapo.lookup_curp(
    given_names="JUAN",
    first_surname="PEREZ",
    date_of_birth="1990-01-01",
    gender="H",
    birth_state_code="DF",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**given_names:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**first_surname:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**date_of_birth:** `str` — Format YYYY-MM-DD
    
</dd>
</dl>

<dl>
<dd>

**gender:** `LookupCurpRequestGender` 
    
</dd>
</dl>

<dl>
<dd>

**birth_state_code:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**second_surname:** `typing.Optional[str]` 
    
</dd>
</dl>

<dl>
<dd>

**generate_rfc:** `typing.Optional[bool]` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.renapo.<a href="src/origoid/renapo/client.py">extract_curp_document</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 2 per call.

Retrieves the official RENAPO CURP document ("Constancia de la CURP") as a PDF, together with the full validated record and the CURP's RENAPO status — the **same status matrix as `curp-validations`** (active, homonymy, deceased, apocryphal, judicial suspension, inactive). Use it when you need the citizen's official, printable certificate, not just the validated data.

The PDF is returned **inline as base64** in `data.files[0].content`, alongside the parsed identity fields. This is a **synchronous** call.

Optionally pass `generateRfc: true` to also receive the deterministic `personalInfo.rfc` (computed from the CURP, no SAT call) — identical to `curp-validations`.

**Why it differs from `curp-validations`:** this endpoint retrieves the actual document from RENAPO, so a response takes a little longer to return. It is priced at **2 credits** and has a lower rate limit than the high-volume `curp-validations`.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.renapo.extract_curp_document(
    curp="TEST900101HDFRRN09",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**curp:** `str` — The 18-character CURP whose official document is requested.
    
</dd>
</dl>

<dl>
<dd>

**generate_rfc:** `typing.Optional[bool]` — Generate the RFC associated with the CURP. Deterministic from the CURP (no SAT call). When true, `personalInfo.rfc` is included — same as `curp-validations`.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Social Security
<details><summary><code>client.social_security.<a href="src/origoid/social_security/client.py">lookup_imss_nss</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Retrieves a worker's NSS (Número de Seguridad Social) from IMSS based on CURP. Returns the 11-digit NSS.

Use this endpoint when onboarding employees for payroll or social-security registration: a CURP is far easier to collect than asking the candidate for their NSS card, which is frequently misplaced.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.social_security.lookup_imss_nss(
    curp="ALMR900805HDFRZA09",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**curp:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.social_security.<a href="src/origoid/social_security/client.py">get_imss_employment_status</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Returns the current IMSS employment status of a worker (identified by CURP + NSS): whether they are currently registered as employed, inactive (no current registration), the modality of registration, the registered employer's RFC, employer name, state, base salary, contributed days, and the period the IMSS report covers.

Use this endpoint for income verification (lending, leasing), employment confirmation (background checks), or to detect overlapping employment when complying with employment regulations.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.social_security.get_imss_employment_status(
    curp="GARM900101HDFRZA01",
    nss="92038109713",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**curp:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**nss:** `str` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.social_security.<a href="src/origoid/social_security/client.py">get_isste_record</a>(...) -> GetIssteRecordResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Retrieves a government worker's full ISSSTE record by CURP — personal data, affiliation, pension regime, positions, contribution history, address, assigned clinic, and the official PDF (always included as base64 in `data.files[]` (kind `document`)).

A retrieved record returns `type: SUCCESS` for an active worker. `PENSIONER`, `SCHOLAR`, `INACTIVE`, and `DECEASED` flag KYC-material standings; when more than one applies, precedence is `DECEASED` > `PENSIONER`/`SCHOLAR` > `INACTIVE`. The raw affiliation status and beneficiary type are always in `data.affiliation`, so new statuses ISSSTE may report never break the contract. `CURP_NOT_FOUND` when the CURP has no ISSSTE record. Dates are ISO `YYYY-MM-DD`; monetary amounts are strings (MXN).

**Field presence:** every field is always present (stable contract) — absent values are `null` (objects/scalars, including empty source strings normalized to `null`) or `[]` (arrays), never omitted. Examples: `pensions` and `family` are `[]` when the worker has none; `terminationDate` and a history row's `endDate` are `null` while ongoing; binary assets in `data.files[]` follow array semantics — only assets actually produced are listed (absent ones omitted, not `null`). Unmigrated placeholder values from ISSSTE (e.g. address `POR ACTUALIZAR`, clinic `FAVOR DE ATENDER A ESTE TRABAJADOR` / clave `0409999`, state `ENTIDAD DESCONOCIDA`) are normalized to `null` (data not yet migrated). ISSSTE only exposes records for titulares (TRABAJADOR/PENSIONISTA/BECARIO); a CURP that is only a dependant (not a titular) returns `CURP_NOT_FOUND`. To retrieve a dependant, query the titular CURP and match `data.family[]` by the dependant CURP. `beneficiaryType` is derived authoritatively from the record (a DIRECT pension means PENSIONISTA even if the document labels them TRABAJADOR); each pension carries a derived `category` (DIRECT/SURVIVOR/INSURANCE/DISABILITY).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.social_security.get_isste_record(
    curp="GOTM850312MDFMRX05",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**curp:** `str` — CURP of the government worker whose ISSSTE record is requested.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## INE
<details><summary><code>client.ine.<a href="src/origoid/ine/client.py">validate_voter_list</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates that a Mexican voter ID (INE / IFE) credential exists in INE's Lista Nominal — the official roll of registered voters — by sending CIC, OCR or ID number depending on the credential model. Returns a confirmation, the voter's polling section, and validity dates.

Use this endpoint as part of KYC to verify that the voter ID presented by your customer is registered and valid (not stolen, not lost, not cancelled).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.ine.validate_voter_list(
    request={"key": "value"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `ValidateVoterListRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.ine.<a href="src/origoid/ine/client.py">extract_voter_id_data</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Performs OCR on the front and back of a Mexican voter ID (INE / IFE) and returns the structured data printed on the credential: full name, CURP, voter key (CIC / OCR), address, photograph metadata, the document model variant (D, E, F, G, H — current and recent INE designs), and the MRZ read from the back when present.

What sets this endpoint apart is **integrated address normalization + geocoding**: the address printed on the INE is rarely clean — abbreviations, missing colonia, inconsistent casing. We normalize and enrich it automatically. You get back not only the raw address text, but also:

- **`addressNormalized`**: the printed INE address, normalized and enriched (corrected casing, expanded abbreviations, validated postal code, and neighborhood / municipality / state matched from the official catalog). The `geocodingStatus` field reports the match confidence: `VERIFIED` (house- or street-level match), `PARTIAL` (locality or postal-code match), or `UNVERIFIED` (no confident match).
- **`electoralGeography`**: derived electoral district, federal entity, and polling section — useful for cross-checking with `validateVoterList`.
- **Document model detection** (E, G, H) and per-model security feature validation.
- **MRZ + QR cross-validation**: when the back contains MRZ and QR, we read both and confirm they agree with the printed fields. Mismatches are flagged.

Use this endpoint to digitize voter ID capture without manual transcription, and to obtain a geo-enriched address record in a single call — eliminating a separate geocoding step in your KYC flow.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.ine.extract_voter_id_data(
    front="front",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**front:** `str` — Front image in Base64 (PNG/JPG)
    
</dd>
</dl>

<dl>
<dd>

**back:** `typing.Optional[str]` — Back image in Base64 (PNG/JPG). Optional.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.ine.<a href="src/origoid/ine/client.py">extract_qr_data</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 2 per call.

Decrypts and parses the QR codes printed on Mexican voter IDs (INE models G and H). The two QRs on the back contain RSA-signed payloads with the holder's full record (name, CURP, voter key, address, signature). This endpoint decrypts both QRs and merges the result.

Use this endpoint as a tamper-evidence check: if the QR decrypts successfully and matches the printed data, the credential is highly likely to be authentic.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.ine.extract_qr_data(
    back="back",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**back:** `str` — Back image of the INE credential in Base64 (PNG/JPG). The QR code must be visible. **Required.**
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Biometrics
<details><summary><code>client.biometrics.<a href="src/origoid/biometrics/client.py">match_faces</a>(...) -> MatchFacesResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Compares two facial images and returns a similarity score (0–100) plus a binary match/no-match decision. Typical use is 1:1 verification between a live selfie and the photograph on an ID document.

Use this endpoint to confirm that the person presenting an ID is the same person depicted on it.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.biometrics.match_faces(
    face="face",
    front="front",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**face:** `str` — Face image (selfie) in Base64 (PNG/JPG)
    
</dd>
</dl>

<dl>
<dd>

**front:** `str` — Front ID image in Base64 (PNG/JPG). Can be any official document globally.
    
</dd>
</dl>

<dl>
<dd>

**threshold:** `typing.Optional[int]` — Acceptance threshold (1-100). Default: **90** (KYC-grade). Lower (e.g. 80) is more permissive; higher is stricter. Overridable per request.
    
</dd>
</dl>

<dl>
<dd>

**document_type:** `typing.Optional[MatchFacesRequestDocumentType]` — Optional. Use a specific type (e.g. `INE`, `MEX_PASSPORT`) for strict validation against that exact document type. Use `ANY` to require the image be some recognized ID (auto-detected) — returns `NO_DOCUMENT_DETECTED` if not. Omit it to skip document validation.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.biometrics.<a href="src/origoid/biometrics/client.py">check_liveness</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Analyzes a selfie to determine whether it depicts a real, live person in front of the camera (`isLive: true`) or a spoofing attempt (printed photo, screen replay, mask). Returns a liveness score, confidence level, and detected attack types when applicable.

Use this endpoint at the start of a remote KYC flow to filter out automated bots, recycled images, and basic presentation attacks before invoking heavier downstream checks.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.biometrics.check_liveness(
    selfie="selfie",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**selfie:** `str` — Selfie image of the subject in Base64 (PNG/JPG).
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Compliance
<details><summary><code>client.compliance.<a href="src/origoid/compliance/client.py">search_sat69</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates if an individual or legal entity is listed in the Mexican Tax Authority (SAT) Article 69 blacklist. This endpoint covers all sub-lists of Art. 69. Note: This endpoint does NOT evaluate Article 69-B (EFOS/simulated operations).

**Business Rules (Search Priority):**
1. The client must provide EITHER a name (`name` / Razón Social) OR an exact identifier (`rfc`).
2. If `rfc` is provided, the backend performs a strict exact match. If only `name` is provided, the backend performs a highly restrictive text search.

**Risk Level Matrix (`riskLevel`):**
- `NONE`: No matches found. Safe for automated approval.
- `LOW` (Informativo / Sin Riesgo Operativo): The subject has historical or administrative records but is legally operating. **Lists:** Condonados (Todos los decretos/artículos), Reducción Art. 74 CFF, Retorno de Inversiones, Entes Públicos y de Gobierno Omisos.
- `MEDIUM` (Riesgo Financiero / Morosidad): The subject has active enforceable debts or the SAT declared them insolvent/uncollectible. **Lists:** Firmes, Exigibles, Cancelados (Incosteabilidad / Insolvencia).
- `HIGH` (Riesgo Operativo Grave): The subject cannot be found by authorities or their digital billing seals (CSD) have been revoked, halting their operations. **Lists:** No Localizados, CSD Sin Efectos.
- `CRITICAL` (Riesgo Legal / Fraude Penal): The subject has criminal convictions related to tax crimes. **Lists:** Sentencias.

**Multi-match selection:** When the subject appears in more than one SAT list, the top-level `riskLevel` reflects only the record with the most recent `publicationDate`. All historical records are still returned in `matches[]` so you can audit the full timeline.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.compliance.search_sat69(
    request={"name": "JUAN PEREZ LOPEZ"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `SearchSat69Request` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.compliance.<a href="src/origoid/compliance/client.py">search_sat69b</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates if an individual or legal entity is listed in the Mexican Tax Authority (SAT) Article 69-B blacklist. This list is specifically for EFOS (Empresas que Facturan Operaciones Simuladas), commonly known as 'Factureros' or shell companies involved in tax fraud and money laundering.

**Business Rules (Search Priority):**
1. The client must provide EITHER a name (`name` / Razón Social) OR an exact identifier (`rfc`).
2. If `rfc` is provided, the backend performs a strict exact match. If only `name` is provided, the backend performs a highly restrictive text search.

**Risk Level Matrix (`riskLevel` mapped to SAT Status):**
- `NONE`: No matches found in the SAT 69-B list. Safe for automated approval.
- `LOW`: The SAT status is **'Desvirtuado'** (Investigated but successfully proved innocence) or **'Sentencia Favorable'** (Won in court / cleared). Provided for audit trails and historical record.
- `MEDIUM`: Reserved for intermediate risk states. Currently not produced by the SAT 69-B classification.
- `HIGH`: The SAT status is **'Presunto'** (Currently under investigation for simulated operations). Extreme caution advised; usually triggers Enhanced Due Diligence (EDD) or temporal blocks.
- `CRITICAL`: The SAT status is **'Definitivo'** (Confirmed shell company / EFOS). Legally binding block required for AML compliance.

**Multi-match selection:** A taxpayer may appear in more than one record (e.g., first listed as `PRESUNTO`, later reclassified to `DEFINITIVO` or `SENTENCIA_FAVORABLE`). The top-level `riskLevel` reflects only the record with the most recent `publicationDateSat` (falling back to `publicationDateDof`). All historical records are still returned in `matches[]` so you can audit the full timeline.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.compliance.search_sat69b(
    request={"name": "JUAN PEREZ LOPEZ"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `SearchSat69BRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.compliance.<a href="src/origoid/compliance/client.py">search_ofac</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Searches the consolidated OFAC sanctions lists for the provided name. Coverage includes:

- **SDN consolidated** — Specially Designated Nationals (general SDN, plus sub-programs `SDGT` Specially Designated Global Terrorists and `SDNTK` Specially Designated Narcotics Traffickers / Kingpin Act).
- **Non-SDN consolidated** — Foreign Sanctions Evaders (`FSE`), Sectoral Sanctions Identifications (`SSI`), Correspondent Account / Payable-Through Account restrictions (`CAPTA`), Menu-Based Sanctions (`NS-MBS`), Iran Sanctions Act non-SDN (`NS-ISA`), Palestinian Legislative Council (`NS-PLC`).
- **UN consolidated sanctions** — surfaced as `OFAC_UN` for clients who want a single endpoint covering both US and UN screening.

Each match carries the originating list (`listType`), the sanction programs that apply, entity type (individual, entity, vessel, aircraft), full aliases and any compliance remarks published with the record.

**Risk Level Matrix (`riskLevel`):**
- `NONE` (envelope `type: SUCCESS`): no matches at or above `minSimilarityScore`. Safe for automated approval from an OFAC perspective.
- `CRITICAL` (envelope `type: SUCCESS`): one or more matches found in **any** OFAC or UN sanctions list. Treat as a hard block, reject the relationship, and file the SAR (Suspicious Activity Report) required by your jurisdiction.

There are intentionally only two levels. AML best practice treats any sanctions list hit — SDN, sectoral, informational, anywhere — as a binding stop. Surfacing intermediate gradations (HIGH / MEDIUM / LOW) misleads the client into believing some hits are merely advisory and is the most common cause of regulatory findings against KYC systems. The `listType` and `complianceDetails.programs` of every match are still surfaced so your compliance team can apply finer policy on top of the binary verdict.

**Multi-match handling:** common names can produce dozens of fuzzy hits. The response returns **every record at or above `minSimilarityScore`**, sorted by `similarityScore` descending. There is no fixed truncation, so a genuine hit will never be hidden by a cap. If the volume of matches is higher than your review process can absorb, raise `minSimilarityScore` to tighten the match. `data.totalMatchesFound` mirrors `matches[].length` and is provided for convenience.

**Which variant matched:** every entry in `matches[]` carries a `matchedOn` object indicating whether the query matched the canonical `entityName` or one of the record's aliases:
- `matchedOn.type` is either `entityName` (the query matched the canonical name) or `alias` (the query matched an alias on the record).
- `matchedOn.value` is the alias text that produced the hit when `type` is `alias`, and `null` when `type` is `entityName`.

Why this matters: OFAC sometimes lists a person under a family member's record (e.g. `OSEGUERA CERVANTES, Nemesio` — better known as El Mencho — is published as an alias of the canonical `OSEGUERA CERVANTES, Ruben`). Without `matchedOn` a reviewer scanning the response would see `Ruben` with score 100 and discount the hit. With `matchedOn` the alias is surfaced explicitly so the reviewer can confirm the right person was matched.

**Multi-identifier matching:** `name` is required, but you can pass `passportNumber` and/or `nationalIdNumber` alongside it to tighten the match. Each extra identifier you provide is used to:

- **Boost the score** when it agrees with the record (record's passport or national ID matches the one you sent → +5 added to `similarityScore`, capped at 100).
- **Downgrade or filter the match** when it contradicts the record (different passport or national ID → −20 points). If the final score falls below `minSimilarityScore`, the record is excluded from `matches[]` entirely.
- **Stay neutral** when the record does not publish that identifier (most OFAC records don't have a passport number, for example). Missing data never penalises a match — only contradicting data does.

A `matchedOn` entry per match always carries an `identifiersMatched` array — for example `name` and `passportNumber` — listing exactly which fields agreed, so the reviewer can audit the confidence behind a hit.

**Practical coverage of the identifier boost:** OFAC publishes passport numbers and national IDs on a minority of records — most often on non-Mexican subjects (Iranian, Russian, Venezuelan, Cuban records carry passports more frequently). For purely Mexican counterparties the boost rarely applies in practice: OFAC does not publish CURP, RFC appears only on a handful of records, and most Mexican subjects are sanctioned with name + aliases only. Use these identifiers for cross-border screening where the upside is real; for MX-only KYC the `name` field is what does the work and the optional identifiers stay neutral.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.compliance.search_ofac(
    name="JOAQUIN GUZMAN LOERA",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**name:** `str` — Full name of the individual or Legal Name of the entity. Required.
    
</dd>
</dl>

<dl>
<dd>

**passport_number:** `typing.Optional[str]` — Passport number, normalised (trimmed, uppercase). Optional. Exact match against a record's passport ID is a strong signal — adds 5 to the score and is reported in `matchedOn.identifiersMatched`. A mismatch drops the score by 20.
    
</dd>
</dl>

<dl>
<dd>

**national_id_number:** `typing.Optional[str]` — National identifier as published by OFAC — typically `National ID`, `Cedula`, `RFC`, `SSN`, or `Tax ID` depending on jurisdiction. Optional. Normalised (trimmed, uppercase). Exact match adds 5 to the score; mismatch drops 20. OFAC does not publish CURP, and RFC appears only on a handful of records, so for Mexican subjects this field is usually neutral. Highest practical value is for cross-border screening of foreign counterparties.
    
</dd>
</dl>

<dl>
<dd>

**min_similarity_score:** `typing.Optional[int]` — Minimum similarity score required to return a fuzzy match. Defaults to 90 — a conservative threshold tuned to suppress common-name false positives. Lower it for broader recall when manual review is in place; raise it for stricter automated approval. There is no enforced floor — you can request very low thresholds, but the lower you go the noisier the response will be. The final score after multi-identifier boosts and penalties is the value compared against this threshold.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.compliance.<a href="src/origoid/compliance/client.py">search_peps</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 2 per call.

Searches the consolidated PEP (Politically Exposed Persons) database for a subject — covering active PEPs, former PEPs (`EX_PEP`), and their immediate family and close associates (`PEP_AFFINITY`, `EX_PEP_AFFINITY`).

**Input flexibility — three valid invocation forms:**

The endpoint accepts a search in any of three forms — pick the one that matches the data you have on hand. You may include `nationalIdNumber` alongside any of them to tighten the match.

1. **Single name (`name`)** — pass the full name as one string. Quick and convenient when you only have the full name as a single value.
2. **Separated name (`givenNames` + `firstSurname` + optional `secondSurname`)** — Mexican-style split. **Strongly recommended for best match quality**, because the matching engine can apply per-component logic that produces fewer false positives on compound first names and compound surnames. When using this form, both `givenNames` and `firstSurname` are required.
3. **Identifier only (`nationalIdNumber`)** — pass a CURP or RFC by itself when that is all you have. The search runs against records that publish the identifier.

If more than one form is sent (for example `name` plus separated fields), the separated fields take precedence over `name`. The `nationalIdNumber` is normalised (trim, uppercase) and reported back per match in `matchedOn.identifiersMatched` and in the standalone `identifierMatch` field.

**Risk Level Matrix (`riskLevel`):**

Each match in `matches[]` has a base severity per its `listType`:

| `listType` | base severity |
|---|---|
| `PEP_ACTIVE` | HIGH |
| `EX_PEP` | MEDIUM |
| `PEP_INACTIVE` | MEDIUM |
| `PEP_AFFINITY` | MEDIUM |
| `EX_PEP_AFFINITY` | LOW |

The endpoint `riskLevel` is the **maximum** severity across `matches[]`:

- `NONE` — no matches at or above `minSimilarityScore`. Safe for automated approval from a PEP perspective.
- `LOW` — only `EX_PEP_AFFINITY` hits found (family or close associate of a former PEP).
- `MEDIUM` — at least one `EX_PEP`, `PEP_INACTIVE`, or `PEP_AFFINITY` hit.
- `HIGH` — at least one `PEP_ACTIVE` hit. Aligned with the categories that FATF Recommendation 12 and Mexican LFPIORPI flag for Enhanced Due Diligence (EDD).

`riskLevel` is a screening signal intended to feed your compliance workflow. Combine it with `identifierMatch`, `similarityScore`, and the per-match `complianceDetails` we surface so your team can apply its own internal policy.

**Which variant matched:** every entry in `matches[]` carries a `matchedOn` object identifying the canonical record that was hit and an `identifiersMatched` array — for example `name` and `nationalIdNumber` — listing which fields agreed. The standalone `identifierMatch` field exposes the precision of the identifier comparison (`EXACT`, `PARTIAL`, `MISMATCH`, or `NOT_PROVIDED` when no identifier was sent). Use these together to audit the confidence behind a hit.

**How `similarityScore` is computed:** the score reflects **combined name + identifier confidence**, not name alone. A match's `similarityScore` is the higher of (a) the name match quality and (b) the strength of the identifier comparison — an `EXACT` `nationalIdNumber` match contributes a full-confidence score, a `PARTIAL` match contributes a strong-but-not-certain score, and `MISMATCH` / `NOT_PROVIDED` contribute nothing (the name drives the score in those cases). This means a search by `nationalIdNumber` alone still returns a high-confidence hit even though no name was supplied to compare — a CURP or RFC coincidence is a deterministic identity signal. Inspect `identifierMatch` and `matchedOn.identifiersMatched` to see *why* a given match scored the way it did.

**Multi-match handling:** the response returns every match at or above `minSimilarityScore`, sorted by `similarityScore` descending. `data.totalMatchesFound` mirrors `matches[].length` and is provided for convenience.

Use this endpoint as part of AML programs where screening counterparties against PEPs is part of your KYC workflow — common in regulated financial services, cross-border payments, and onboarding pipelines.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.compliance.search_peps(
    request={"name": "ANDRES MANUEL LOPEZ OBRADOR"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `SearchPepsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Email
<details><summary><code>client.email.<a href="src/origoid/email/client.py">validate_email</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates an email address for deliverability and risk. Returns the normalized address, deliverability verdict (`deliverable`, `risky`, `undeliverable`), a quality score (0–100), a toxicity score, and a set of boolean verdicts (`isFree`, `isDisposable`, `isRoleAccount`, `isFull`, `isCatchAll`, `isToxic`).

Use this endpoint at signup time to reject typos and disposable addresses before they enter your database, reducing bounce rates on transactional email and fraud signals from throwaway accounts.

**`riskLevel` reference** — standardized scoring you can branch on:

| Level | When |
|---|---|
| `NONE` | Mailbox is deliverable and not flagged as disposable or toxic. |
| `MEDIUM` | Domain is catch-all — accepts every address, so the specific mailbox cannot be confirmed to exist. |
| `HIGH` | Deliverability is `risky` or `unknown` (e.g. mail server rejects probes), OR the domain belongs to a disposable / temporary email provider (Mailinator, 10minutemail, Guerrillamail, etc.). |
| `CRITICAL` | Mailbox is `undeliverable` (does not exist or is full), OR the address scores above 40 on the toxicity index (associated with spam / abuse). |

The `verdicts` object always carries the underlying signals (`isFree`, `isDisposable`, `isRoleAccount`, `isFull`, `isCatchAll`, `isToxic`) so you can apply your own scoring on top if you need finer granularity.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.email.validate_email(
    email="user@example.com",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**email:** `str` — The email address to be validated.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Proof of Address
<details><summary><code>client.proof_of_address.<a href="src/origoid/proof_of_address/client.py">extract_proof_of_address</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Performs OCR on a Mexican proof-of-address document — utility bills (water, electricity, gas, internet, telephone) and bank statements — and returns the structured data printed on it.

Returns:

- **`provider`**: the issuing utility or institution (CFE, Telmex, Agua, etc.), so you can apply provider-specific business rules and recognize legitimate document layouts.
- **`personalInfo`**: holder name as printed on the document.
- **`address`**: full address as printed (street, exterior / interior number, neighborhood, municipality, state, postal code).
- **`billing`**: issuance date, account number, period covered.
- **`validations`**: flags about document age, document type detection confidence, and structural consistency checks.

Use this endpoint to automate address verification in KYC flows. The extracted address can be cross-checked against the address your customer submitted at signup.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.proof_of_address.extract_proof_of_address(
    file="file",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**file:** `str` — Proof of Address file in Base64 (PNG, JPG, or PDF).
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Fiscal
<details><summary><code>client.fiscal.<a href="src/origoid/fiscal/client.py">validate_rfc</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates the structure and current status of a Mexican RFC (Registro Federal de Contribuyentes) against the SAT registry. Returns the taxpayer type (individual or legal entity), registration status, and the official SAT message.

Use this endpoint to confirm that the RFC your customer provided is real, well-formed, and currently active with SAT before extending credit, issuing invoices, or signing contracts.

**Risk Level Matrix (`riskLevel`):**
- `NONE` (envelope `type: SUCCESS`): RFC exists in the SAT padrón and is authorized to issue / receive invoices (`isBillable: true`). Safe for automated approval.
- `LOW` (envelope `type: RFC_NOT_INVOICEABLE`): RFC exists in the SAT padrón but is **restricted** for invoicing — typically a legal entity that has not completed its fiscal-status onboarding or has been suspended. The taxpayer is real, just not currently invoiceable.
- `CRITICAL` (envelope `type: RFC_NOT_FOUND`): RFC is **not registered** in the SAT padrón. Either it was never issued, or the value provided is a typo or fabricated. Do not extend credit, issue invoices, or accept as a counterparty without further verification.

`MEDIUM` and `HIGH` are not produced by this endpoint — the SAT padrón only distinguishes the three outcomes above.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.fiscal.validate_rfc(
    rfc="GARM900101HDF",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**rfc:** `str` — Taxpayer's RFC (12 or 13 characters).
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.fiscal.<a href="src/origoid/fiscal/client.py">extract_csf</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Extracts structured data from a Constancia de Situación Fiscal (CSF) — the official PDF document issued by SAT that proves a taxpayer's fiscal situation. You can submit the CSF as a base64-encoded file (PDF/PNG/JPG) and get back the full content as JSON, or alternatively pass RFC + CIF (the tax-certificate code) to retrieve the same data directly from SAT's public QR validator.

Returns the legal name, address, fiscal regime, economic activities, registration date, and tax obligations. Use this endpoint to automate vendor onboarding and to keep your records of partners' fiscal data continuously up to date.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.fiscal.extract_csf(
    request={"rfc": "PELJ900101AAA", "cif": "24010199999"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `ExtractCsfRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.fiscal.<a href="src/origoid/fiscal/client.py">validate_cfdi</a>(...) -> Envelope</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates a CFDI (Comprobante Fiscal Digital por Internet) — Mexico's mandatory electronic invoice — by checking its current status with SAT. Returns whether the CFDI is currently valid (`VALID`) or cancelled (`CANCELED`), the cancellation status (e.g. requires receiver acceptance), and the fiscal effect (`INCOME`, `EXPENSE`, `TRANSPORT`, `PAYROLL`, `PAYMENT`).

Use this endpoint when reconciling supplier invoices, processing expense reports, or ensuring that the invoices you receive are real and not later cancelled by the issuer without your knowledge.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.fiscal.validate_cfdi(
    request={"key": "value"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `ValidateCfdiRequest` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Banking
<details><summary><code>client.banking.<a href="src/origoid/banking/client.py">validate_cep</a>(...) -> ValidateCepResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

**Credits:** 1 per call.

Validates a Mexican SPEI transfer against Banco de México's official **CEP** service ("Comprobante Electrónico de Pago") and returns the verified transfer details together with Banxico's cryptographic proof. Use it to confirm that a SPEI payment actually settled, for reconciliation, payout confirmation, or fraud checks on a claimed transfer.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from origoid import OrigoID
from origoid.environment import OrigoIDEnvironment

client = OrigoID(
    api_key="<value>",
    environment=OrigoIDEnvironment.DEFAULT,
)

client.banking.validate_cep(
    request={"key": "value"},
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `ValidateCepRequest` — Identify the transfer by its tracking key or its reference number (choose the matching request body below), and provide the beneficiary account, bank code and amount to match. When the account is an 18-digit CLABE the bank code is resolved from it automatically; for a card account, include the bank code.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

