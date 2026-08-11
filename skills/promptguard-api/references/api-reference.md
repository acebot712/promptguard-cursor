# PromptGuard API — Detailed Reference

## Authentication

All requests must include the `X-API-Key` header:

```
X-API-Key: pg_live_xxx
```

Keys are created in the [dashboard](https://app.promptguard.co/settings/api-keys).
All keys start with `pg_live_`.

## Threat types

The `threatType` field in scan responses carries one of these exact values.
Match on them literally — they are `snake_case`, and several read differently
from the plain-English name of the attack (`prompt_injection`, not `injection`;
`pii_leak`, not `pii`).

| Value | Description |
|-------|-------------|
| `prompt_injection` | Prompt injection, including embedded jailbreaks |
| `pii_leak` | Personally identifiable information |
| `data_exfiltration` | Data exfiltration attempt |
| `toxicity` | Toxic, harmful, or offensive content |
| `api_key_leak` | Exposed API key |
| `secret_key_leak` | Leaked token or other secret |
| `system_prompt_leak` | System prompt disclosure |
| `policy_violation` | Matched a customer-defined policy |
| `fraud_abuse` | Social engineering or financial abuse |
| `malware` | Malicious code or payload |
| `insecure_code` | Vulnerable code |
| `url_violation` | Dangerous or disallowed URL |
| `malicious_entity` | Known-bad entity |
| `mcp_violation` | Malicious tool / MCP invocation |
| `off_topic` | Outside the configured topic scope |
| `gibberish` | Non-language input |
| `language_violation` | Disallowed language |

A further set covers agent-directed attacks (content injection, semantic
manipulation, memory and RAG poisoning, multi-agent dynamics) — for example
`rag_poisoning`, `memory_poisoning`, `sub_agent_spawning`, `html_obfuscation`,
`image_stego`. Several of those are opt-in and surface through correlated
events rather than a single scan.

> These values are the `ThreatType` enum in the platform's
> `backend/api/shared/security/engine.py`. They are **not** published in
> `openapi-developer.json`, which types `threatType` as a bare nullable string —
> so there is nothing to generate them from, and this table is maintained by
> hand. Verify against that enum before changing it. See `AGENTS.md`.

## PII entity types

`piiFound` in a redact response lists the detector labels that matched. The
common ones:

`email`, `phone_us`, `phone_intl`, `ssn`, `credit_card`, `iban`, `crypto`,
`ipv4`, `ipv6`, `mac_address`, `date_of_birth`, `us_passport`,
`drivers_license`, `medical_license`, `aba_routing`, `us_itin`, `us_zip`,
`medicare`, `api_key`

Detection is not US-only: there are national-identifier patterns for the UK,
Ireland, Spain, Italy, Australia, India, Korea, Poland, Singapore, Finland and
Switzerland (`nhs_number`, `uk_nino`, `es_nif`, `it_fiscal_code`, `au_tfn`,
`in_aadhaar`, `kr_rrn`, `pl_pesel`, `sg_nric_fin`, `fi_personal_id`, `ch_ahv`
and others).

> Labels come from `PIIDetector` in the platform's
> `backend/api/shared/security/detectors.py`. Note these are **response**
> labels: treat `piiFound` as the reliable surface, and do not promise a user
> that a `pii_types` request filter narrows what gets redacted.

## Rate limits

| Tier | Requests/month | Rate limit |
|------|---------------|------------|
| Free | 10,000 | 10 req/s |
| Pro | 100,000 | 50 req/s |
| Enterprise | Custom | Custom |

## Proxy mode

PromptGuard can act as a transparent proxy for LLM providers.
Set the LLM SDK's `base_url` to `https://api.promptguard.co/api/v1/proxy`
and pass both `X-API-Key` (PromptGuard) and `Authorization` (provider) headers.

```python
from openai import OpenAI

client = OpenAI(
    api_key=os.environ["OPENAI_API_KEY"],
    base_url="https://api.promptguard.co/api/v1/proxy/openai/v1",
    default_headers={"X-API-Key": os.environ["PROMPTGUARD_API_KEY"]},
)
```

## Health check

```
GET /health → { "status": "ok" }
```

No authentication required.
